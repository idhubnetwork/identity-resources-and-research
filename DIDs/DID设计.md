# DID IDHub
IDHub DID 的设计
## 智能合约
DID Rigister Contract

### DID
任何以太坊地址都可以充当 DID 
```solidity
address public identifier;
```

### DID Owner
每个 DID 都有一个 Owner 拥有最高权限，默认为 DID
```solidity
mapping(address => address) public owners;

function identityOwner(address identity) public view returns(address) {
    address owner = owners[identity];
    if (owner != 0x0) {
    	return owner;
    }
    return identity;
}
```

### PublicKeys
用户可以注册任何公钥到自己的 DID
公钥会返回到 DID Document 列出公钥数组供引用
```json
{
  "@context": ["https://w3id.org/did/v1", "https://w3id.org/security/v1"],
  "id": "did:example:123456789abcdefghi",
  ...
  "publicKey": [{
    "id": "did:example:123456789abcdefghi#keys-1",
    "type": "RsaVerificationKey2018",
    "owner": "did:example:123456789abcdefghi",
    "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
  }, {
    "id": "did:example:123456789abcdefghi#keys-2",
    "type": "Ed25519VerificationKey2018",
    "owner": "did:example:pqrstuvwxyz0987654321",
    "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
  }, {
    "id": "did:example:123456789abcdefghi#keys-3",
    "type": "Secp256k1VerificationKey2018",
    "owner": "did:example:123456789abcdefghi",
    "publicKeyHex": "02b97c30de767f084ce3080168ee293053ba33b235d7116a3263d29f1450936b71"
  }],
  ...
}
```

### Authentications
用户可以注册用于验证JWT的公钥到自己的认证属性
认证属性会返回到 DID Document 列出公钥数组供验证JWT
```json
  "authentication": [{
    // this key is referenced
    "type": "RsaSignatureAuthentication2018",
    "publicKey": "did:example:123456789abcdefghi#keys-1"
  }, {
    // this key is embedded
    "type": "RsaSignatureAuthentication2018",
    "publicKey: {
      "id": "did:example:123456789abcdefghi#keys-2",
      "type": "Ed25519VerificationKey2018",
      "owner": "did:example:pqrstuvwxyz0987654321",
      "publicKeyBase58": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
    }
  }],
```

### Attributes
用户可以添加任何属性到 DID
```json
{
  "service": [{
    "id": "did:example:123456789abcdefghi;openid",
    "type": "OpenIdConnectVersion1.0Service",
    "serviceEndpoint": "https://openid.example.com/"
  }, {
    "id": "did:example:123456789abcdefghi;vcr",
    "type": "CredentialRepositoryService",
    "serviceEndpoint": "https://repository.example.com/service/8377464"
  }, {
    "id": "did:example:123456789abcdefghi;xdi",
    "type": "XdiService",
    "serviceEndpoint": "https://xdi.example.com/8377464"
  }, {
    "id": "did:example:123456789abcdefghi;agent",
    "type": "AgentService",
    "serviceEndpoint": "https://agent.example.com/8377464"
  }, {
    "id": "did:example:123456789abcdefghi;hub",
    "type": "HubService",
    "serviceEndpoint": "https://hub.example.com/.identity/did:example:0123456789abcdef/"
  }, {
    "id": "did:example:123456789abcdefghi;messages",
    "type": "MessagingService",
    "serviceEndpoint": "https://example.com/messages/8377464"
  }, {
    "id": "did:example:123456789abcdefghi;inbox",
    "type": "SocialWebInboxService",
    "serviceEndpoint": "https://social.example.com/83hfh37dj",
    "description": "My public social inbox",
    "spamCost": {
      "amount": "0.50",
      "currency": "USD"
    }
  }, {
    "id": "did:example:123456789abcdefghi;authpush",
    "type": "DidAuthPushModeVersion1",
    "serviceEndpoint": "http://auth.example.com/did:example:123456789abcdefg"
  }]
}
```

### Transaction Funding Service
用户应该可以享受交易代发服务而不用自己支付交易手续费


## Json Web Token

### create
用户选用在 Authentications 中注册过的任意 ECDSA 私钥签名

### verify
返回 DID Document 和 bool 以及 payload


## SAML-AWS中的应用

* 使用 DID 登陆 aws-saml-idhub 网站
* 使用 DID 作为智能合约数据管理的标识符

### 集中登陆版本
* 注册组织用户，在 saml 服务器上生成自己的密钥文件
* samlResponse 应该实时生成，所以每次都要访问 saml
* 用户通过 did-jwt 登陆前端网站管理数据

### 分布式管理版本
* 通过 did-jwt 管理数据的 golang 微服务
* 前端提供签名 did-jwt 的 js 微服务
* metamask 为前端提供钱包服务
* samlResponse 有效期可以指定并可以重复使用




















