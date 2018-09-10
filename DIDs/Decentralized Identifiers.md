# 去中心化标识符
去中心化标识符（[Decentralized Identifiers](https://w3c-ccg.github.io/did-spec/)）是一种新型可验证的且拥有“自我主权”的数字身份标识符，简写为DID。DIDs完全被DID主题控制，不依赖于任何中心注册机构，身份提供商或者证书颁发机构。DIDs是一些将DID主题与可信任的交互方式相关联的URL。DIDs解析为DID文档 —— 一种描述如何使用该特定DID的简单文档。每个DID文档至少包含三件事：加密材料，身份验证套件和服务端点。与身份验证套件相结合的加密材料提供了一组用于作为DID主题进行身份验证的机制（例如，公钥，匿名生物识别协议等）。服务端点支持与DID主题的可信交互。

本文档指定了所有DID支持的通用数据模型，格式和操作。

## 简介

### 概述

传统的身份管理系统基于集中的权限，例如公司目录服务，证书颁发机构或域名注册。从加密信任验证的角度来看，这些集中式权限中的每一个都是其自己 的信任根。要使身份管理跨这些系统工作，需要实现联合身份管理。

分布式账本技术（DLT）的出现，有时被称为区块链技术，为完全[去中心化的身份管理](https://w3c-ccg.github.io/did-spec/#dfn-decentralized-identity-management)提供了机会。在去中心化身份系统中，实体可以自由使用任何共享的信任根。全球分布式分类账（或提供类似功能的去中心化P2P网络）提供了一种管理信任根的方法，既没有集中权限也没有单点故障。结合使用，DLT和去中心化身份系统使任何实体都能够在任意数量的独立信任根上创建和管理自己的标识符。

实体由去中心化标识符（DIDs）标识。他们可以通过证明（例如，数字签名，隐私保护生物识别协议等）进行身份验证。DIDs指向DID文档。DID文档包含一组用于与实体交互的服务端点。遵循隐私设计的格言，每个实体可以根据需要拥有尽可能多的DID，以尊重实体所希望的身份，人物角色和背景的分离。

要将DID与特定分布式帐本或网络一起使用，需要在单独的DID方法规范中定义DID方法。DID方法指定了一组在特定分布式帐本或网络上如何注册，解析，更新和撤销DID的规则集。

此设计消除了对集中式注册机构以及密钥管理的集中式证书颁发机构 —— 分层PKI（公钥基础结构）标准模式的依赖性。由于DIDs驻留在分布式帐本上，因此每个实体都可以充当其自己的根权限 —— 一种称为 DPKI（去中心化PKI）的体系结构。

注意，还可以为在联合或集中身份管理系统中注册的标识符开发DID方法。就他们而言，所有类型的标识符系统都可以添加对DID的支持。这在集中式，联合式和去中心化标识符的世界之间建立了互操作性桥梁。

#### URIs, URLs, and URNs

DID依靠URLs作为基础，因此了解 W3C 在2001年9月如何声明术语[URI]（统一资源标识符），[URL]（统一资源定位符）和[URN]（统一资源名称）非常重要。这三类标识符之间的关键区别是：

[ URI ]是标识抽象或物理资源的任何类型的标识符（例如，URN，URL等）的术语。它可能是也可能不是一个可解析的资源。
[ URL ]是可以被解析或解引用以定位Web上的资源表示的任何类型的URI的术语（例如，网页，文件，图像等）。
[ URN ]是用于持久识别资源的特定类型的URI的术语，即，无论资源移动的频率，更改标签，更改所有者等都永远不会改变的标识符。URN旨在永久地持续。

#### DID的动机

对去中心化标识符的需求日益增长，对于仍适合Web体系结构的新类型URL产生了两个特定要求，并且还有一些额外要求，即更传统的URL（如基于HTTP的URL）没有：

* 1.新类型的URL 不应该需要集中的权限来注册，解析，更新或撤销标识符。今天绝大多数URI都基于DNS名称或IP地址，这些名称或IP地址依赖于集中授权进行注册和最终控制。可以在没有任何此类权限的情况下创建和管理DID。
* 2.可以加密验证其所有权和相关元数据（包括公钥）的URL。通过DIDs和DID文档利用与分布式帐本相同的公钥/私钥密码技术进行身份验证。

#### 人类友好标识符的作用
DID实现全球唯一性，无需中央注册机构。然而，这是以人类可记忆性为代价的。能够生成全局唯一标识符的算法自动生成没有人类意义的随机字符串。这证明了关于被称为Zooko三角形的标识符的公理：“人类有意义，去中心化，安全挑选任何两个”。

当然有许多用例需要从人性化的标识符开始发现DID —— 自然语言名字，域名或DID所有者的传统地址，例如移动电话号码，电子邮件地址，Twitter句柄或博客URL。但是，将人性化标识符映射到DID（并且以可以验证和信任的方式这样做）的问题超出了本规范的范围。

这个问题的解决方案（有很多）应该在引用本规范的单独规范中定义。强烈建议此类规范仔细考虑：（a）基于欺骗用户关于目标实体的真正人类友好标识符的众多安全攻击，以及（b）使用本质上相关的人性化标识符的隐私后果，特别是如果它们是全球唯一的。

#### 本规范的目的
本规范的第一个目的是定义通用DID方案和DID上的通用操作集，这些操作可以为任何能够支持DID的分布式帐本或网络实现。本规范的第二个目的是定义DID方法规范的一致性要求 —— 一个单独的规范，它定义特定DID方案和特定分布式总账或网络的特定DID记录操作集。

从概念上讲，此规范与DID方法规范的关系类似于IETF通用URI规范（[ RFC3986 ]）与特定URI方案（[ IANA-URI-SCHEMES ]（例如http：和https： [ RFC7230 ]中指定的方案。它也类似于IETF通用URN规范（[ URN ]）与特定URN命名空间定义（如[ RFC4122 ]中定义的UUID URN命名空间）的关系。不同之处在于除了定义特定的DID方案之外，DID方法规范还必须指定在其编写的网络上读取，写入和撤销DID记录的方法。

有关DID方法及其相应规范的列表，请参阅DID方法注册表[ [DID-METHOD-REGISTRY](https://w3c-ccg.github.io/did-spec/#bib-did-method-registry) ]。

### 设计目标

本节总结了DID架构的设计目标和原则。

* 分权：DID体系结构应消除对标识符管理的集中权限或单点故障的要求，包括全局唯一标识符，公共验证密钥，服务端点和其他元数据的注册。
* 自主权：DID架构应该为人类和非人类实体提供直接拥有和控制其数字标识符的能力，而无需依赖外部权限。
* 隐私：DID架构应该使实体能够控制其信息的隐私，包括属性或其他数据的最小，选择性和渐进式公开。
* 安全：DID体系结构应为依赖方提供足够的安全性，以依赖DID文档达到所需的保证级别。
* 证据基础：DID体系结构应该使实体能够提供身份验证的加密证明和授权权限证明。
* 可发现：DID架构应该使实体能够发现其他实体的DID，以了解有关这些实体的更多信息或与这些实体进行交互。
* 互通性：DID架构应使用可互操作的标准，因此DID基础架构可以利用为互操作性而设计的现有工具和软件库。
* 可移植性：DID体系结构应该是独立于系统和网络的，并使实体能够将其数字标识符用于支持DID和DID方法的任何系统。
* 简单：为了实现这些设计目标，DID架构应该（用阿尔伯特爱因斯坦的话来说）“尽可能简单但不简单”。
* 可扩展性：如果可能，DID体系结构应该支持可扩展性，前提是它不会极大地妨碍互操作性，可移植性或简单性。

### 简单举例

这是DID的一个简单示例：

`did:example:123456789abcdefghi`

#### 自我管理的DID文档
以下是描述上述DID的DID文档的示例。此示例假设控制此标识符的私钥的实体对DID文档具有权威性。

```json

{
  "@context": "https://w3id.org/did/v1",
  "id": "did:example:123456789abcdefghi",
  "publicKey": [{
    "id": "did:example:123456789abcdefghi#keys-1",
    "type": "RsaVerificationKey2018",
    "owner": "did:example:123456789abcdefghi",
    "publicKeyPem": "-----BEGIN PUBLIC KEY...END PUBLIC KEY-----\r\n"
  }],
  "authentication": [{
    // this key can be used to authenticate as DID ...9938
    "type": "RsaSignatureAuthentication2018",
    "publicKey": "did:example:123456789abcdefghi#keys-1"
  }],
  "service": [{
    "type": "ExampleService",
    "serviceEndpoint": "https://example.com/endpoint/8377464"
  }]
}

```

## 术语

本文档试图通过使用专门术语来讨论特定概念，从而传达去中心化标识符空间中概述的概念。该术语包含在下面，并与整个文档相关联，以帮助读者：

### 去中心化标识符（DID）
全局唯一标识符，不需要集中注册机构，因为它是使用分布式帐本技术或其他形式的去中心化网络注册的。DID的通用格式在本规范中定义。在DID方法规范中定义了特定的DID规格。 
### 去中心化身份管理
基于去中心化标识符的身份管理。去中心化身份管理将标识符创建权限扩展到[X.500目录服务](https://en.wikipedia.org/wiki/X.500)，域名系统和大多数国家ID系统所需的传统信任根之外。
### DID文档
描述DID的一组数据 ，包括实体可用于将自身认证为DID的机制，例如公钥和匿名生物识别。DID文档还可以包含描述该实体的其他属性或声明。这些文档是基于图的数据结构，通常使用[ JSON-LD ]表示，但可以使用其他兼容的基于图的数据格式表示。
### DID片段
DID引用的一部分，位于第一个井号字符（“＃”）之后。DID片段使用与URI片段相同的语法。请注意，DID片段必须紧跟DID。如果DID引用包括后跟片段的DID路径，则该片段不是DID片段。
### DID方法
定义如何在特定的分布式账本或网络上实现特定的DID方案，包括可以读取，写入和撤销DID和DID文档的精确方法。
### DID路径
DID引用的一部分，位于第一个正斜杠字符后面。DID路径使用相同的语法作为URI路径。请注意，如果DID路径后跟一个片段，则该片段不是DID片段。
### DID引用
DID加上可选的DID路径或DID片段。
### DID规格
去中心化标识符的正式语法。通用DID方案在本说明书中定义。DID方法规范定义的特定DID方案，工程与特定DID方法。
### 分布式帐本（又名DLT）
一种分布式数据库，其中各个节点使用共识协议来维护共享分类帐，其中每个事务都以加密方式签名并链接到先前的事务。
### 服务端点
服务代表实体运行的网络地址。特定服务的示例包括发现服务，社交网络，文件存储服务和可验证的声明存储库服务。服务端点也可以由诸如可扩展数据交互的通用数据交互协议提供。
### 可扩展数据交互（又名XDI）
由OASIS XDI技术委员会定义的语义图格式和语义数据交互协议 。

### 基本规格
本规范依赖于一些基本规范。下面列出了依赖关系及其用途。

* [ JSON ] : JSON规范提供了此规范使用的基本数据格式。
* [ JSON-LD ] : JSON-LD规范支持在JSON数据之上分层数据语义。

## 去中心化标识符（DID）
全球唯一的去中心化标识符的概念并不新鲜; 通用唯一标识符（UUID）最初是在20世纪80年代开发的，后来成为开放软件基金会分布式计算环境的标准功能 。通过使用生成具有足够熵的128位值的算法，UUID在没有集中式注册服务的情况下实现全局唯一性，以使碰撞的机会极小。UUID在[ [RFC4122](https://tools.ietf.org/html/rfc4122) ] 中正式指定为特定类型的统一资源名称（URN）。

DID类似于UUID，除了：（a）像URL一样，它可以被解析或解引用到描述实体的标准资源（DID文档），以及（b）与URL不同， DID文档通常包含加密材料，该加密材料允许对与DID相关联的实体进行认证。

### 通用DID规格
通用DID规格是符合[ [RFC3986](https://tools.ietf.org/html/rfc3986) ] 的URI规格。它由DID后跟可选路径和/或片段组成。术语DID仅指符合以下ABNF中的do规则的标识符; 单独使用时，它不包含路径或片段。可选地包括路径和/或片段的DID称为DID引用。

以下是使用[ [RFC5234](https://tools.ietf.org/html/rfc5234) ]中的语法的ABNF定义（将ALPHA定义为大写或小写AZ）。

```
did-reference      = did [ "/" did-path ] [ "#" did-fragment ]
did                = "did:" method ":" specific-idstring
method             = 1*methodchar
methodchar         = %x61-7A / DIGIT
specific-idstring  = idstring *( ":" idstring )
idstring           = 1*idchar
idchar             = ALPHA / DIGIT / "." / "-"
```

### 特定DID方法规格
DID方法规范必须确切地定义一个特定的DID规格，该规格只由一个方法名称（“通用DID方案”中的方法规则）标识。由于DID旨在用于去中心化身份基础结构，因此建议不要建立唯一DID方法名称的注册表。相反，DID方法名称的唯一性应该通过人类共识来建立，即，特定的DID规格必须使用在发布时规范作者已知的所有DID方法名称中唯一的方法名称。

[附录A注册表](https://w3c-ccg.github.io/did-method-registry/)中提供了已知DID方法名称及其相关规范的列表。

由于方法名称是DID的一部分，因此它应该尽可能短。建议使用五个字符或更少字符的方法名称。方法名称可能可以反映DID方法规范适用的分布式帐本或网络的名称。

特定DID方案的DID方法规范必须指定如何生成DID 的specific-idstring组件。必须能够在不使用集中式注册表服务的情况下生成specific-idstring值。specific-idstring值 本身应该是全局唯一的。在通用DID规格中did规则定义的完全限定DID必须是全局唯一的。

如果需要，特定的DID规格可以定义多个特定的specific-idstring格式。建议一个特定的DID规格尽量定义为少数特定specific-idstring格式。

### 路径
通用DID路径与URI路径相同，并且必须 符合[ [RFC3986](https://tools.ietf.org/html/rfc3986) ]中路径无根ABNF规则的ABNF。DID路径应该用于解决通过DID服务端点标记可用的资源。

特定的DID规格可能为DID路径指定ABNF规则，这些规则比本节中的通用规则更具限制性。

### 片段
通用DID片段与URI片段相同，并且必须符合[ [RFC3986](https://tools.ietf.org/html/rfc3986) ]中片段ABNF规则的ABNF。DID片段必须仅用作与DID文档无关的方法指针，以标识唯一的密钥描述或其他DID文档组件。要解析此指针，必须将包含DID片段的完整DID引用用作目标JSON对象的id键的值。

特定的DID方案可以为DID片段指定ABNF规则，这些规则比本节中的通用规则更具限制性。

### 标准化
对于最广泛的互操作性，DID规范化应该尽可能简单和通用。因此：
1. did：规格 name 必须是小写的。
2. 方法名称必须是小写的。
3. 区段中的specific-idstring规则的区分大小写和规范化必须由管理DID方法规范来定义。 

### 持久化
DID必须是持久且不可变的，即绑定到实体一次并且永远不会改变（永远）。理想情况下，DID将是一个完全抽象的分去中心化标识符（如UUID），可以随着时间的推移绑定到多个底层分布式帐本或网络，从而保持其持久性独立于任何特定的分类账或网络。但是，在多个帐本或网络上注册相同的标识符会引入极其困难的实体和权限启动（[SOA](https://en.wikipedia.org/wiki/List_of_DNS_record_types#SOA)）问题。它还大大增加了开发人员的实现复杂性。

为了避免这些问题，建议 DID方法规范仅生成绑定到强大，稳定的帐本或网络的DID和DID方法，这些方法能够对DID和DID方法的持久性做出最高水平的承诺。

注意：虽然未包含在此版本中，但此规范的未来版本可能支持DID文档equivID属性，以在多个分类帐或网络上表示代表相同标识符所有者的DID记录之间建立可验证的等效关系。这种等价关系可以产生单个持久抽象DID的实际等价物。

## DID文档
如果DID是键值对中的索引键，则DID Document是索引键指向的值。DID及其关联的DID文档的组合形成去中心化标识符的根记录。

DID文档必须是符合[ [RFC7159](https://tools.ietf.org/html/rfc7159) ] 的单个JSON对象。出于此版本的DID规范的目的，此JSON对象的格式在JSON-LD中指定，JSON-LD是一种将JSON数据映射到[ JSON-LD ] 定义的RDF语义图模型的格式。本规范的未来版本可以为DID文档指定其他语义图格式，例如JXD（JSON XDI数据），一种XDI图模型的序列化格式。

以下部分定义此JSON对象的属性，包括这些属性是必需的还是可选的。

### Context

JSON-LD格式的JSON对象必须包含JSON-LD上下文语句。本声明的规则是：

1. DID文档有且只有顶级上下文语句。
2. 这个属性的键必须是`@context`。
3. 此键的值必须是通用DID上下文的URL : `https://w3id.org/did/v1`.

示例（使用假设的URL）：
```JSON
{
  "@context": "https://w3id.org/did/v1"
}
```
DID方法规范可以定义自己的JSON-LD上下文。但是，除非有必要正确实施该方法，否则不建议定义新的上下文。特定于方法的上下文 绝不能覆盖通用DID上下文中定义的术语。

### DID主题
DID主题是DID文档所关注的标识符，即，DID文档用来描述的DID。DID主题的规则是：

1. 一个DID文档有且只有一个DID主题。
2. 这个属性的键必须是id。
3. 该密钥的值必须是有效的DID。
4. 当此DID文档注册到目标分布式账本或网络时，已注册的DID必须与此DID主题值匹配。

示例：
```json
{
  "id": "did:example:21tDAKCERh95uGgKbJNHYp"
}
```
注意：DID方法规范可以创建不包含id密钥的DID文档的中间表示，例如DID解析器执行解析时。但是，完全解析的DID文档必须包含有效id属性。

### 公钥
公钥用于数字签名，加密和其他加密操作，而这些操作又是身份验证或与服务端点建立安全通信等目的的基础。此外，公钥可能在DID CRUD操作的授权机制中发挥作用; 这可以通过DID方法规范来定义。

主要目的是DID文档列出公钥，其对应的私钥由DID标识的实体（“拥有”公钥）控制。当然，DID文档也可以还列出了“未拥有”公钥。

如果DID文档中不存在公钥，则必须假定密钥已被撤销或无效。DID文档可能包含已撤销的密钥。包含已撤销密钥的DID文档 还必须包含或引用密钥的撤销信息（例如，撤销列表）。每个DID方法规范都要详细说明如何执行和跟踪撤销。

公钥的规则是：
1. DID文档可以包含publicKey属性。
2. publicKey属性的值应该是公钥的一个数组。
3. 每个公共密钥必须包括id与type属性，以及一个值属性。
4. 每个公钥可以包括owner属性，该属性标识控制相应私钥的实体；如果缺少此属性，则假定它是DID主题。
5. 公共密钥的值属性可以是 `publicKeyPem`，`publicKeyJwk`，`publicKeyHex`，`publicKeyBase64`或类似的，这取决于公共密钥的格式和编码方式。
6. 密钥类型和格式的注册表参考[附录A注册机构](https://w3c-ccg.github.io/ld-cryptosuite-registry/) 。
示例：
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
可以在DID文档中嵌入或引用密钥。例如，属性可以通过两种方式引用键`authentication`：
```json
{
...

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

...
}
```
处理DID文档中的`publicKey`属性时使用的算法是： 

1. 设value为与publicKey属性关联的数据，并将结果初始化为 `null`。
2. 如果value是对象，则嵌入密钥材料。将结果设置 为值。
3. 如果value是字符串，则通过引用包含该键。假设值是一个URL。
	1. 取消引用URL并检索publicKey 与URL关联的属性（例如，处理publicKey取消引用文档顶层的属性）。
	2. 遍历每个公钥对象。
		1. 如果id对象的属性与value匹配 ，则将result设置为该对象。
4. 如果结果不包含至少`id`，`type`和`owner`属性以及任何强制性公共加密材料，如由所确定的结果的type属性，抛出异常。
虽然在`owner`上面的一些示例中该字段可能看起来是多余的，但是密钥可以在DID文档中表示，其中所有者在另一个DID文档中描述。关联数据证明库通常期望该`owner`字段始终存在，并且如果缺少该字段则可能抛出异常。此外，根据DID文档可解释为图或树的要求，`owner`无法通过使用树中的键位置来推断默认字段。
DID文档中密钥的缓存和到期完全由DID解析器和其他客户端负责。

### 认证
身份验证是实体可以通过加密方式证明它们与DID和DID描述相关联的机制。请注意，身份验证与授权是分开的，因为实体可能希望不在其能够证明所有权（因此能够模仿实体）的情况下使其他实体来更新DID文档（例如协助密钥恢复 ）。

身份验证的规则是：
1. DID文档可能包含`authentication` 属性。
2. `authentication`属性的值应该是一个证明机制的数组。
3. 每个证明机制必须包括type属性。
4. 每个证明机制可以嵌入或引用公钥。
```json
{
  "@context": "https://w3id.org/did/v1",
  "id": "did:example:123456789abcdefghi",
  ...
  "authentication": [{
    // this key can be used to authenticate as DID ...fghi
    "type": "RsaSignatureAuthentication2018",
    "publicKey": "did:example:123456789abcdefghi#keys-1"
  }, {
    // this key can be used to authenticate as DID ...fghi
    "type": "PseudonymousBiometricAuthentication2018",
    "biometricTemplate": "did:example:123456789abcdefghi#bio-1"
  }],
  ...
}
```

### 授权和代理
授权是实体说明如何代表实体执行操作的机制。代理是实体可用于授权其他实体代表其行事的机制。请注意，授权与身份验证是分开的。这对于密钥丢失情况下的密钥恢复尤其重要，当实体不再能够访问其密钥或密钥泄露时，所有者的可信第三方需要覆盖攻击者的恶意活动。

由于授权和代理通常由分布式帐本实施，因此每个DID方法规范都应详细说明如何为帐本执行授权和代理。

至少有两种建议的方法用于实现授权，代理和监护的概念：

1. 帐本可以通过重新使用`authentication`属性使用的相同加密套件模式来实现粗粒度模式`guardian` ，或者更优选地
2. 帐本可以实施基于功能性的方法，并提供更细粒度的授权，代理和监护控制。

### 服务端点
除了发布身份验证和授权机制之外，DID文档的另一个主要目的是为实体发现服务端点。服务端点可以表示实体希望通告的任何类型的服务，包括用于进一步发现，认证，授权或交互的去中心化身份管理服务。服务端点的规则是：

1. DID文档可能包含`service`属性。
2. `service`属性的值应该是一个服务端点的数组。
3. 每个服务端点必须包含`id`， `type`和`serviceEndpoint`属性，并且可能包含其他属性。
4. 服务端点协议应该应该以开放标准规范发布。
5. `serviceEndpoint`属性的值必须是JSON-LD对象或符合[ [RFC3986](https://tools.ietf.org/html/rfc3986) ] 的有效URI，并根据[ [RFC3986](https://tools.ietf.org/html/rfc3986) ] 第6节中的规则以及其适用的URI方案规范中的任何规范化规则进行规范化。
示例
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




























