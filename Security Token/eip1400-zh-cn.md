---

eip: ERC1400
title: Security Token Standard
author: Adam Dossa (@adamdossa), Pablo Ruiz (@pabloruiz55), Fabian Vogelsteller (@frozeman), Stephane Gosselin (@thegostep)
discussions-to: #1411
status: Draft
type: Standards Track
category: ERC
created: 2018-09-09
require: ERC-1066 (#1066), ERC-1410 (#1410)

---

## 简要总结

用于发布证券通证，管理其所有权和转移限制的标准接口。

## 摘要

基于部分可互换通证的标准（ERC 1410），以提供额外的功能来管理代表资产所有权的可互换通证的所有权的不同类型。

可以选择扩展此标准以实现ERC777和ERC20（参见ERC 1410＃1410中的向后兼容性）。

## 动机

通过指定一个标准接口，加速发布和管理以太坊区块链上的证券，通过该接口，所有相关方可以操作和查询证券通证。

证券通证与其他通证用例存在重大差异，链下和链上参与者之间的交互更为复杂，并且受到相当多的监管审查。

证券通证应该能够代表任何资产类别，在任何司法管辖区内发布和管理，并遵守相关的监管限制。

## 要求

将证券的发行，交易和生命周期事件转移到公共分布式账本上需要采用标准的方式对证券，其所有权及其在链上的属性进行建模。

在与证券通证生态系统各方讨论后，编译了以下要求：

* 必须有一个标准接口来查询转移是否成功并返回失败原因。
* 必须能够执行强制转移以进行法律诉讼或资金回收。
* 必须发布标准事件以进行发行和赎回。
* 必须能够将元数据附加到通证持有者的余额的子集，例如特殊股东权利或转移限制的数据。
* 必须能够根据链下数据，链上数据和转移参数在转移时修改元数据。
* 可能需要将签名数据传递到转移事务中，以便在链上验证它。
* 不应限制可以代表的司法管辖区内的资产类别范围。
* 应该与ERC20和ERC777兼容。

## 摘要

有许多类型的证券虽然代表相同的标的资产，但需要有与之相关的差异化数据。

这些额外的元数据隐式地使这些证券不可替代，但实际上这些数据通常应用于证券的一个子集而不是单个证券。将通证持有者的余额划分为各个tranche的能力，每个部分具有单独的元数据，在Partially-Fungible Token章节中进行了说明。

例如，通证持有人的余额可以分为两部分：在主要发行期间发行的通证和通过二级交易收到的通证。

证券通证合同可以引用此元数据以便应用其他逻辑来确定转移是否有效，并确定一旦转移到接收方余额中应该与通证相关联的元数据。

为了表示证券元数据，我们使用ERC 1410部分可互换通证标准。

与实用型通证相比，证券的转移可能由于各种原因而失败，而实用通证通常仅要求发送者具有足够的余额。

这些条件可能与正在转移的证券的元数据（即它们是否受制于锁定期），证券的发送者和接收者的身份（即他们是否通过KYC流程，是否是经认可或发行人的关联公司）或与特定转让无关而设定在通证级别的原因（即通证合同强制执行最大数量的投资者或任何单一投资者持有的百分比上限）。

对于ERC20 / ERC777通证，`balanceOf` 和 `allowance` 函数提供了一种在执行转移之前检查转移是否可能成功的方法，可以在链上和链下执行。

对于代表证券的通证，此标准引入了一个函数 `canSend` ，当失败的原因更复杂时，它提供了一种更通用的方法来实现这一目标和一个完整转移的函数（即包括与证券的转移和接收者一起发送的任何数据）。

为了提供比true或false更丰富的结果，将会返回一个字节返回码。这使我们能够说明转移失败的原因，或者至少是失败原因类别。查询文档的能力和转移的预期成功包含在 Security Token 章节中。

## 规范

### Document Management

#### getDocument / setDocument

这些函数用于管理与通证关联的文档库。 这些文档可以是法律文件或其他参考资料。

一份文档与一个短名称（表示为 `bytes32` ）相关联，并且可以选择在链上存储与其链接的文档内容的散列值。

```solidity
function getDocument(bytes32 _name) external view returns (string, bytes32);
function setDocument(bytes32 _name, string _uri, bytes32 _documentHash) external;
```

### Transfer Validity

#### canSend

证券转移可能由于多种原因而失败，例如：

* 通证的发送者或接收者的身份
* 对转让的指定通证的限制（即与转移的通证部分相关的限制）
* 与通证总体状态相关的限制（即投资者总数）

该标准提供了一个链上函数，用于确定转移是否成功，并返回指示转移无效的原因的详细信息。

可以使用智能合约和链上数据来定义这些规则，或者依赖于作为 `sendByTranche` 函数的一部分传递的 `_data` 参数可以表示转移的授权（例如，转移代理的签名消息可以证明此特定转移的有效性）。

该函数将返回遵循EIP-1066标准的ESC（以太坊状态码）和另外的 `bytes32` 参数，该参数可用于定义具有其他详细信息的应用程序特定原因代码（例如，转移限制规则导致无效的发送操作）。

它还以与 `sendByTranche` 类似的方式返回正在传输的通证的目标tranche

```solidity
function canSend(address _from, address _to, bytes32 _tranche, uint256 _amount, bytes _data) external view returns (byte, bytes32, bytes32);
```

### Token Issuance

#### isIssuable

证券通证发行者可以指定通证的发行已经完成（即不能铸造或发行新的通证）。

如果令牌为 `isIssuable()` 返回FALSE，则以后它必须始终返回FALSE。

```solidity
function isIssuable() external view returns (bool);
```

#### issueByTranche

必须调用此函数来增加总供应量。

调用时，此函数必须触发 `IssuedByTranche` 事件。

```solidity
function issueByTranche(bytes32 _tranche, address _tokenHolder, uint256 _amount, bytes _data) external;

```

### Token Redemption

#### operatorRedeemByTranche

允许操作员代表通证持有者销毁或赎回通证。

必须从总供应量和通证持有者的余额中减去已销毁或已赎回的通证。通证销毁应该像发送通证一样，并且受到相同的条件限制。 每次调用此函数时都必须触发 `BurnedByTranche` 事件。

```solidity
function operatorRedeemByTranche(bytes32 _tranche, address _tokenHolder, uint256 _amount, bytes _operatorData) external;
```

#### redeemByTranche

允许通证持有者销毁或赎回通证。

必须从总供应量和通证持有者的余额中减去已销毁或已赎回的通证。通证销毁应该像发送通证一样，并且受到相同的条件限制。 每次调用此函数时都必须触发 `BurnedByTranche` 事件。

```solidity
function redeemByTranche(bytes32 _tranche, uint256 _amount, bytes _data) external;
```

### Controller Operation

为了提供发行者是否可以定义 `defaultOperators` 或 `defaultOperatorsByTranche` 的透明度，可以使用函数 `isControllable`

如果一个通证的 `isControllable()` 返回FALSE，那么它必须：

* 以后一直返回FALSE
* 返回 `defaultOperators` 和 `defaultOperatorsByTranche` 的空列表
* 永远不要为 `defaultOperators` 和 `defaultOperatorsByTranche` 添加地址

换句话说，如果发行者将 `isControllable` 设置为返回TRUE，则该通证不能有默认操作员。

### Interface

```solidity
/// @title IERCST Security Token Standard (EIP 1400)
/// @dev See https://github.com/SecurityTokenStandard/EIP-Spec

interface IERCST is IERCPFT {

    // Document Management
    function getDocument(bytes32 _name) external view returns (string, bytes32);
    function setDocument(bytes32 _name, string _uri, bytes32 _documentHash) external;

    // Controller Operation
    function isControllable() external view returns (bool);

    // Token Issuance
    function isIssuable() external view returns (bool);
    function issueByTranche(bytes32 _tranche, address _tokenHolder, uint256 _amount, bytes _data) external;
    event IssuedByTranche(bytes32 indexed tranche, address indexed operator, address indexed to, uint256 amount, bytes data, bytes operatorData);

    // Token Redemption
    function redeemByTranche(bytes32 _tranche, uint256 _amount, bytes _data) external;
    function operatorRedeemByTranche(bytes32 _tranche, address _tokenHolder, uint256 _amount, bytes _operatorData) external;
    event RedeemedByTranche(bytes32 indexed tranche, address indexed operator, address indexed from, uint256 amount, bytes operatorData);

    // Transfer Validity
    function canSend(address _from, address _to, bytes32 _tranche, uint256 _amount, bytes _data) external view returns (byte, bytes32, bytes32);

}
```

### 注

#### 强制转移Forced Transfers

法规可能要求发行人或受信任的第三方保留代表投资者转移通证的权力。因此，ERC-1400（证券通证标准）规范取代ERC-1410（部分可互换通证）并规定不得允许通证持有者撤销默认操作员或tranche默认操作员。

#### Restricted Transfers限制转移

与实用通证相比，证券通证的转移可能由于多种原因而失败，而实用通证通常仅要求发送者具有足够的余额。

这些条件可能与被转移的证券通证的元数据（即它们是否受制于锁定期），令牌的发送者和接收者的身份和资格（即他们是否通过KYC过程和他们是否是经过认证的或者发行人的附属公司），或者与特定转移无关的原因但出于监管目的而设置在证券层面（即证券强制执行最大数量的投资者或对任何单一投资者持有的百分比设定上限）。

对于实用通证（ERC20 / ERC777）， `balanceOf` 和 `allowance` 函数提供了一种在执行传输之前检查传输是否可能成功的方法，可以在链上和链下执行。

此标准引入了一个函数 `canSend` ，它提供了一种更通用的方法来查询发送通证是否成功。它接受一组参数，这些参数可能包括签名数据，并返回一个原因字节代码，其中包含有关事务成功或失败的信息。

注意：调用 `canSend` 的结果可能会根据链上状态（包括块编号或时间戳）以及可能的链下反馈而改变。因此，在作为不修改任何状态的view类型函数调用之后，它不保证将来的传输肯定成功。

#### 身份 Identity

在许多司法管辖区，一方是否能够接收和发送证券通证取决于该方身份的特征。例如，在某账户有资格购买或出售特定证券之前，大多数司法管辖区都要求某种程度的KYC / AML流程。另外，账户可以被分类为投资者资格类别（例如，认可投资者，合格购买者），并且他们的公民身份也可以告知与其证券相关联的限制。

存在各种身份标准（例如ERC725，Civic，uPort），可用于捕获该账户的身份数据，以及集中管理的其他方法（例如，维护已经从KYC角度批准的地址的白名单）。这些身份标准的共同点是以以太坊地址为标识符（可能是一方的钱包或身份合同），因此 `canSend` 函数在确定是否符合资格要求时可以使用证券通证的发送方和接收方的地址作为代理身份。

除此之外，该标准并未强制要求任何指定的身份识别方法。

#### Reason Codes 原因代码
为了改善通证持有者体验， `canSend` 必须根据下面指定的EIP-1066应用程序特定状态代码在成功或失败时返回原因字节代码。具体实现还可以将任意数据作为 `bytes32` 返回，以提供原因代码未捕获的其他信息。

| Code   | Reason                                                        |
| ------ | ------------------------------------------------------------- |
| `0xA0` | Transfer Verified - Unrestricted                              |
| `0xA1` | Transfer Verified - On-Chain approval for restricted token    |
| `0xA2` | Transfer Verified - Off-Chain approval for restricted token   |
| `0xA3` | Transfer Blocked - Sender lockup period not ended             |
| `0xA4` | Transfer Blocked - Sender balance insufficient                |
| `0xA5` | Transfer Blocked - Sender not eligible                        |
| `0xA6` | Transfer Blocked - Receiver not eligible                      |
| `0xA7` | Transfer Blocked - Identity restriction                       |
| `0xA8` | Transfer Blocked - Token restriction                          |
| `0xA9` | Transfer Blocked - Token granularity                          |
| `0xAA` |                                                               |
| `0xAB` |                                                               |
| `0xAC` |                                                               |
| `0xAD` |                                                               |
| `0xAE` |                                                               |
| `0xAF` |                                                               |

#### On-chain vs. Off-chain Transfer Restrictions 链上与链下转移限制
确定是否可以发送证券通证的规则可以是自动执行（例如，限制证券中投资者的最大数量的规则）或需要链下输入（例如，对交易的显式经纪人批准）。为了方便后者， `sendByTranche` 和 `canSend` 函数接受一个额外的 `bytes _data` 参数，该参数可以由批准方签名并用于验证传输。

该数据的规范超出了本标准的范围，并且应该由特定的实现来指定。



























