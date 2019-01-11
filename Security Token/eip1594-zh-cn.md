# EIP1594

原文链接：https://github.com/SecurityTokenStandard/EIP-Spec/blob/master/eip/eip-1594.md

## 概要


该标准属于与证券型通证相关的ERC-1400（＃1411）标准集。

提供一个支持将数据链外注入通证转移/发行/赎回函数的标准，以及检查与其执行不同的转移有效性的能力。

## 摘要

包含错误信号，链下数据注入和发布/赎回语义。

该标准继承自ERC-20（＃20），如果需要，可以轻松扩展以符合ERC-777（＃777）标准。

## 动机

通过指定一个标准接口，加速发布和管理以太坊区块链上的证券，通过该接口，所有相关方可以操作和查询证券型通证。

证券型通证与其他通证用例存在重大差异，链下和链上参与者之间的交互更为复杂，并且受到相当多的监管审查。

在传输，发布和赎回函数的同时提供数据（例如签名授权）的能力允许证券型通证更灵活地实现传输限制，而不依赖于链上白名单。

使用ERC-1066（＃1066）提供原因代码，说明通证转移失败的原因，而无需用户实际尝试并执行转移，从而允许改进用户体验，并可能通过转移失败的原因节省gas。

形式化发行和赎回语义（类似于挖矿/销毁）(similar to minting / burning)提供了对通证总供应量及其随时间变化的可见性。

## 需求

有关整个标准库的全部需求，请参阅ERC-1400（＃1411）。

在与证券型通证生态系统各方讨论后，编译了以下要求。

* 必须有一个标准接口来查询转移是否成功并返回失败原因。
* 必须为发行和赎回发布标准事件。
* 可能需要将签名数据传递到转移交易中，以便在链上验证它。
* 不应限制可能的司法管辖区内的资产类别范围。
* 必须与ERC-20（＃20）兼容。
* 可能要与ERC-777（＃777）兼容。

## 逻辑一句

### 转移限制

与实用型通证相比，证券的转移可能由于各种原因而失败，而实用型通证通常仅要求发送者具有足够的余额。

这些条件可能与正在转移的证券的元数据（即它们是否受制于锁定期），证券的发送者和接收者的身份（即他们是否通过KYC流程，是否是经认可或发行人的关联公司）或与特定转移无关但与通证的级别设定有关的原因（即通证合约强制执行最大数量的投资者或任何单一投资者持有的百分比上限）。

对于ERC-20/ERC-777通证，`balanceOf`和`allowance`函数提供了一种在执行转移之前检查传输是否可能成功的方法，该转移可以在链上和链下执行。

对于代表证券的通证来说，此标准引入了一种函数`canTransfer`，当失败原因更加复杂时，该函数提供了更通用的方法来实现这一目标；还提供了整个转移的函数（即包括随转移一起发送的任何数据和证券接收者）。

为了支持传递函数的链下数据输入，传递函数被扩展到`transferWithData`/ `transferFromWithData`可以选择性地采用附加`bytes _data`参数。

为了提供比`true`或`false`更丰富的结果，返回数据将是字节返回码。这使我们能够说明转移失败的原因，或者至少是失败原因的原因。查询文档的能力和转移的预期成功包含在证券型通证章节中。

## 规范

### 转移限制

#### canTransfer / canTransferFrom

证券转移可能由于多种原因而失败，例如：

* 通证的发送者或接收者的身份
* 对正在转移的特定通证的限制（即对特定数量的通证的锁定）
* 与通证总体状态相关的限制（即投资者总数）

该标准提供了一个链上函数，用于确定转移是否成功，并返回指示转移无效的原因的详细信息。

这些规则可以使用智能合约和链上数据来定义，或者依赖于`_data`作为`transferWithData`函数的一部分传递，该函数可以代表传输的授权（例如，由转移代理商签署的证明该特定转移的有效性的签名消息）。

该函数将返回遵循EIP-1066标准的ESC（以太坊状态代码）和`bytes32`可用于定义具有其他详细信息的应用程序特定原因代码的附加参数（例如，导致使转移操作无效响应的转移限制规则）。

如果`bytes _data`为空，则这对应于检查`transfer`（或`transferFrom`）请求是否成功，如果`bytes _data`已填充，那么这对应于检查`transferWithData`（或`transferFromWithData`）是否会成功。

`canTransfer`假设通证的发送者是`msg.sender`并且将通过`transfer`或者`transferWithData`执行，而`canTransferFrom`允许指定通证的发送者，并且转移将通过`transferFrom`或执行`transferFromWithData`。

```solidity
function canTransfer(address _to, uint256 _value, bytes _data) external view returns (byte, bytes32);
function canTransferFrom(address _from, address _to, uint256 _value, bytes _data) external view returns (byte, bytes32);
```

#### transferWithData

转移限制可以采取多种形式，通常涉及链上规则或白名单。然而，对于许多类型的批准转移，维护批准转移的链上列表可能是麻烦且昂贵的。另一种选择是共同签名方法，其中除了通证持有者批准令牌转移之外，授权实体还将提供可以进一步验证转移的签名数据。

`bytes _data`允许任意数据与转移一起提交，通证合约将会解释或记录。这可以是授权转移的签名数据（例如，动态白名单），但也足够灵活以适应其他用例。

`transferWithData`必须发出一个包含转移细节的`Transfer`事件。

```solidity
function transferWithData(address _to, uint256 _value, bytes _data) external;
```

#### transferFromWithData

这是`transferWithData`函数的类比。

`msg.sender`必须有足够设置过的`allowance`，这个`allowance`必须由`_value`借记减少。

```solidity
function transferFromWithData(address _from, address _to, uint256 _value, bytes _data) external;
```

### 通证发行

#### isIssuable

证券型通证发行者可以指定通证的发行已经完成（即，不能铸造或发行新的通证）。

如果通证返回`FALSE`，`isIssuable()`则它必须始终返回`FALSE`。

如果通证返回`FALSE`，`isIssuable()`那么它绝不允许发出额外的通证。

```solidity
function isIssuable() external view returns (bool);
```

#### issue


增加总供应量必须调用此函数。

`bytes _data`参数可用于注入链下数据（例如签名数据）以授权或认证新发行通证的发行方和接收者。

被调用时，此函数必须发布`Issued`事件。

```solidity
function issue(address _tokenHolder, uint256 _value, bytes _data) external;
```

### 通证赎回

#### redeem

允许通证持有者赎回通证。

必须从总供应量和通证持有者的余额中减去赎回的通证。通证赎回应该像发送通证一样，并且受到相同条件的限制。

`Redeemed`事件必须发布每当这个函数被调用时。

与`transferWithData`函数一样，有一个`bytes _data`参数可以在通证合同中用于验证赎回操作。

```solidity
function redeem(uint256 _value, bytes _data) external;
```

#### redeemFrom

这是`redeem`函数的类比。

`msg.sender`必须有设置过足够的`allowance`，这个`allowance`必须由`_value`借记减少。

`Redeemed`事件必须发布每当这个函数被调用时。

```solidity
function redeemFrom(address _tokenHolder, uint256 _value, bytes _data) external;
```

### 接口

```solidity
/// @title IERC1594 Security Token Standard
/// @dev See https://github.com/SecurityTokenStandard/EIP-Spec

interface IERC1594 is IERC20 {

    // Transfers
    function transferWithData(address _to, uint256 _value, bytes _data) external;
    function transferFromWithData(address _from, address _to, uint256 _value, bytes _data) external;

    // Token Issuance
    function isIssuable() external view returns (bool);
    function issue(address _tokenHolder, uint256 _value, bytes _data) external;

    // Token Redemption
    function redeem(uint256 _value, bytes _data) external;
    function redeemFrom(address _tokenHolder, uint256 _value, bytes _data) external;

    // Transfer Validity
    function canTransfer(address _to, uint256 _value, bytes _data) external view returns (bool, byte, bytes32);
    function canTransferFrom(address _from, address _to, uint256 _value, bytes _data) external view returns (bool, byte, bytes32);

    // Issuance / Redemption Events
    event Issued(address indexed _operator, address indexed _to, uint256 _value, bytes _data);
    event Redeemed(address indexed _operator, address indexed _from, uint256 _value, bytes _data);

}

```



























