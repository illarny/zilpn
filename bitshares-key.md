### PrivateKey.js
```js
PrivateKey.fromWif = function fromWif(_private_wif) {
  var private_wif = new Buffer(decode(_private_wif));
  var version = private_wif.readUInt8(0);
  assert.equal(0x80, version, 'Expected version ' + 0x80 + ', instead got ' + version);
  // checksum includes the version
  var private_key = private_wif.slice(0, -4);
  var checksum = private_wif.slice(-4);
  var new_checksum = sha256(private_key);
  new_checksum = sha256(new_checksum);
  new_checksum = new_checksum.slice(0, 4);
  var isEqual = deepEqual(checksum, new_checksum); //, 'Invalid checksum'
  if (!isEqual) {
      throw new Error("Checksum did not match");
  }
  private_key = private_key.slice(1);
  return PrivateKey.fromBuffer(private_key);
};
```

### WalletUnlockModal.jsx
```js
let password = "";
let account = "";
let {cloudMode} = WalletDb.validatePassword(password, true, account);
```

### WalletDb.js
```js

generateKeyFromPassword(accountName, role, password) {
    let seed = accountName + role + password;
    let privKey = PrivateKey.fromSeed(seed);
    let pubKey = privKey.toPublicKey().toString();

    return {privKey, pubKey};
}

validatePassword(
    password,
    unlock = false,
    account = null,
    roles = ["active", "owner", "memo"]
) {
  let fromWif;
  try {
    fromWif = PrivateKey.fromWif(password);
  } catch (err) {}
  let acc = ChainStore.getAccount(account, false);
  let key;
  if (fromWif) {
    key = {
      privKey: fromWif,
      pubKey: fromWif.toPublicKey().toString()
    };
  }
  
  roles.forEach(role => {
    if (!fromWif) {
      key = this.generateKeyFromPassword(account, role, password);
    }

    let foundRole = false;

    if (acc) {
      if (role === "memo") {
        if (acc.getIn(["options", "memo_key"]) === key.pubKey) {
          setKey(role, key.privKey, key.pubKey);
          foundRole = true;
        }
      } else {
        acc.getIn([role, "key_auths"]).forEach(auth => {
          if (auth.get(0) === key.pubKey) {
              setKey(role, key.privKey, key.pubKey);
              foundRole = true;
              return false;
          }
        });
      }
    }
  });
};
```