# bitshares password

## 验证密码过程
* 从钱包中获取私钥
* fromWif = PrivateKey.fromWif(password)

* 从链上获取账户
* acc = ChainStore.getAccount(account, false)

* 如果钱包中获取到了私钥，根据私钥生成本地公私钥对
* key = {
*   privKey: fromWif,
*   pubKey: fromWif.toPublicKey().toString()
* }

* 如果钱包中没有获取到私钥，根据密码生成本地公私钥对
* key = this.generateKeyFromPassword(account, role, password)

* 从账户中获取 active.key_auths, 并循环处理
* 判断本地公私钥对中公钥和 key_auths中的公钥，有相等则验证通过
* let foundRole = false;
* acc.getIn([role, "key_auths"]).forEach(auth => {
*   if (auth.get(0) === key.pubKey) {
*     setKey(role, key.privKey, key.pubKey);
*     foundRole = true;
*     return false;
*   }
* });

* 如果 active.key_auths 没有找到
* 从账户中获取 owner.key_auths, 并循环处理
* 判断本地公私钥对中公钥和 key_auths中的公钥，有相等则验证通过
* if (!foundRole) {
*   let alsoCheckRole =
*     role === "active" ? "owner" : "active";
*   acc.getIn([alsoCheckRole, "key_auths"]).forEach(auth => {
*     if (auth.get(0) === key.pubKey) {
*       setKey(
*         alsoCheckRole,
*         key.privKey,
*         key.pubKey
*       );
*       foundRole = true;
*       return false;
*     }
*   });
* }

