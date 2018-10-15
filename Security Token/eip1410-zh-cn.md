---

eip: ERC1410
title: Partially Fungible Token Standard
author: Adam Dossa (@adamdossa), Pablo Ruiz (@pabloruiz55), Fabian Vogelsteller (@frozeman), Stephane Gosselin (@thegostep)
discussions-to: #1410
status: Draft
type: Standards Track
category: ERC
created: 2018-09-13
require: ERC-777

---

## 简要总结

A standard interface for organising an owners tokens into a set of tranches.

## 摘要

Describes an interface to support an owners tokens being grouped into tranches, with each tranche being represented by an identifying key and a balance.

Tokens are operated upon at a tranche granularity, but data about the overall supply of tokens and overall balances of owners is also tracked.

This standard is an extension of ERC777 and therefore is implicitly compatible with ERC20 and ERC777.

## 动机

在构建与这些通证相关联的功能时，能够将元数据与单个可替换通证相关联是很有用的。

例如，知道何时铸造单个通证允许为通证持有者余额的一部分实施归属或锁定逻辑。

代表证券的通证通常需要将元数据附加到单个通证，例如股票类别或与股票相关的限制。

能够将任意元数据与用户持有的通证组相关联在各种用例中是有用的。它可用于通证出处（即记录先前的通证所有者）或将数据附加到通证用于决定该通证的任何传输限制。

一般情况下，虽然通证在某些情况下是可替代的，但它们不属于其他情况（例如游戏中的积分和存款余额）。能够定义这样的分组并对它们进行操作，同时保持关于通证的整体分布的数据，对于对这种类型的资产进行建模是很有用的。

## 解释

部分可换通证允许将元数据附加到通证持有者的余额的一部分。这些部分余额称为`tranches`，并由`bytes32_tranche`键来索引，该键可以与链上或链下的元数据相关联。

除了存在用于标识它的 `_tranche` 键之外，此元数据的规范不属于此标准的一部分。如果数据在具有相同tranche的通证持有者之间变化，则通证持有者地址可以与tranche配对以用作元数据键（例如，一个“受限”tranche可以与每个通证持有者的不同锁定日期相关联）。

对于个人所有者，部分中的每个通证因此共享公共元数据。

通证可替换性包括元数据，因此我们有：

* 对于特定用户，指定tranche的通证是可替换的
* 对于特定用户，来自不同tranche的通证可能是不可替代的

注：具体实现中，具有相同 `bytes32` 键的tranche根据不同的用户可以与不同的元数据相关联。

## 规范

### 通证信息

#### balanceOfByTranche

包括通过 `balanceOf` 查询的所有tranche的总余额，可能需要确定指定tranche的余额。
```solidity
function balanceOfByTranche(bytes32 _tranche, address _tokenHolder) external view returns (uint256);
```

#### tranchesOf

通证持有者可以将其余额拆分为多个单元（即tranche），此函数将返回与指定通证持有者地址关联的所有tranche
```solidity
function tranchesOf(address _tokenHolder) external view returns (bytes32[]);
```

### Tokens Transfers

通证传输始终具有相关联的来源和目标tranche，以及数量和发送方/接收方地址。

#### sendByTranche

通过扩展ERC777标准并提供一个默认的tranche（通过 `getDefaultTranches` ），可以发送通证（来自tranche）。 要从指定的tranche发送令牌，可以使用 `sendByTranche` 函数。

例如，权限受控通证可以使用tranche元数据来强制执行基于以下的传输限制：

* `_tranche` 值
* 与 `_tranche` 值关联的任何其他数据（例如，可能与 `_tranche` 关联的锁定时间戳）
* 与通证发送者或接收者相关的任何细节（例如，已经认定的他们的身份）
* 转移的通证数量（例如，它是否遵守任何每日或其他单位时间的数量限制）
* `_data` 参数允许调用者提供与传输相关的任何其他授权或详细信息（例如，来自被授权传输的授权实体的签名数据）

其他用例包括通过将先前持有者与目标tranche相关联来跟踪通证的出处。

如果因任何原因转移令牌不成功，则此函数**必须**抛出异常。

当从指定tranche转移通证时，知道这些通证的链上目标tranche（即，不仅仅是通过被触发的事件event）是很有用的。 目标tranche将由此函数的具体实现来决定，并将根据具体用例而有所不同。

该函数必须返回接受者的 `bytes32 _tranche`

如果没有在链上生成接受者的 `bytes32 _tranche` 可以在 `bytes _data` 中定义。

此函数必须发出 `SentByTranche` 事件才能成功转移。

如果接收方的tranche与发送方不同，则此函数必须发出 `ChangedTranche` 事件。

```solidity
function sendByTranche(bytes32 _tranche, address _to, uint256 _amount, bytes _data) external returns (bytes32);
function sendByTranches(bytes32[] _tranches, address[] _tos, uint256[] _amounts, bytes _data) external returns (bytes32);
```

#### operatorSendByTranche

允许操作员代表通证持有者发送证券通证。

如果由缺少 `isOperatorForTranche` 定义的适当批准的地址调用，则此函数必须还原revert

此函数必须发出 `SentByTranche` 事件以成功发送通证。

如果接收方的tranche与发送方不同，则此函数必须发出 `ChangedTranche` 事件。

```solidity
function operatorSendByTranche(bytes32 _tranche, address _from, address _to, uint256 _amount, bytes _data, bytes _operatorData) external returns (bytes32);
function operatorSendByTranches(bytes32[] _tranches, address _from, address _to, uint256[] _amounts, bytes _data, bytes _operatorData) external returns (bytes32[]);
```

### 默认管理

#### getDefaultTranches

为了提供与ERC777 / ERC20的兼容性，实现需要确定在执行ERC777 `send` 函数时要使用的tranche

此函数返回在此情况下使用的tranche，例如，一个证券通证可能返回 `bytes32(“unrestricted”)` tranche，或者使用一小组可能的tranche的简单实现可以返回与通证持有者相关联的所有tranche

返回值可以为空，这意味着没有默认的tranche（因此ERC777 `send` 函数将抛出异常），或返回多个tranche，在这种情况下，ERC777 `send` 函数应按顺序循环这些tranche，直到指定的数量已成功转让为止。

```solidity
function getDefaultTranches(address _tokenHolder) external view returns (bytes32[]);
```

#### setDefaultTranches

允许为指定地址设置默认tranche，这将在ERC777 `send` 函数执行期间使用。

```solidity
function setDefaultTranche(bytes32[] _tranches) external;
```

### Operators

操作员可以获得以下授权：

* 所有通证持有者和tranche（ `defaultCperators` 继承自ERC777）
* 指定tranche的所有通证持有者（ `defaultOperatorsByTranche` ）
* 指定通证持有者的所有tranche（当前和未来）（ `isOperatorFor` 继承自ERC777）
* 指定通证持有者的指定tranche（ `isOperatorForTranche` ）

#### defaultOperatorsByTranche

此函数返回获得所有通证持有者和一种指定tranche授权的默认操作员。
```solidity
function defaultOperatorsByTranche(bytes32 _tranche) external view returns (address[]);
```

#### authorizeOperatorByTranche

允许一个通证持有者为他的指定tranche的通证设置操作员。

每次调用它时，该函数必须发出事件 `AuthorizedOperatorByTranche` 

```solidity
function authorizeOperatorByTranche(bytes32 _tranche, address _operator) external;
```

#### revokeOperatorByTranche

允许一个通证持有者为他的指定tranche的通证撤销操作员。

注意：操作员通过 `defaultOperatorsByTranche` 或 `defaultOperators` 可能会保留对此通证持有者和tranche的授权。

每次调用时，此函数必须发出事件 `RevokedOperatorByTranche`

```solidity
function revokeOperatorByTranche(bytes32 _tranche, address _operator) external;
```

#### isOperatorForTranche

返回指定的地址是否是给定通证持有者和tranche的操作员。

如果地址是上述任何类别下的操作员，则应返回TRUE

```solidity
function isOperatorForTranche(bytes32 _tranche, address _operator, address _tokenHolder) external view returns (bool);
```

### Interface

```solidity
/// @title ERC-1410 Partially Fungible Token Standard
/// @dev See https://github.com/SecurityTokenStandard/EIP-Spec

interface IERC1410 is IERC777 {

    // Token Information
    function balanceOfByTranche(bytes32 _tranche, address _tokenHolder) external view returns (uint256);
    function tranchesOf(address _tokenHolder) external view returns (bytes32[]);

    // Token Transfers
    function sendByTranche(bytes32 _tranche, address _to, uint256 _amount, bytes _data) external returns (bytes32);
    function sendByTranches(bytes32[] _tranches, address _to, uint256[] _amounts, bytes _data) external returns (bytes32[]);
    function operatorSendByTranche(bytes32 _tranche, address _from, address _to, uint256 _amount, bytes _data, bytes _operatorData) external returns (bytes32);
    function operatorSendByTranches(bytes32[] _tranches, address _from, address _to, uint256[] _amounts, bytes _data, bytes _operatorData) external returns (bytes32[]);

    // Default Tranche Management
    function getDefaultTranches(address _tokenHolder) external view returns (bytes32[]);
    function setDefaultTranche(bytes32[] _tranches) external;

    // Operators
    function defaultOperatorsByTranche(bytes32 _tranche) external view returns (address[]);
    function authorizeOperatorByTranche(bytes32 _tranche, address _operator) external;
    function revokeOperatorByTranche(bytes32 _tranche, address _operator) external;
    function isOperatorForTranche(bytes32 _tranche, address _operator, address _tokenHolder) external view returns (bool);

    // Transfer Events
    event SentByTranche(
        bytes32 indexed fromTranche,
        address operator,
        address indexed from,
        address indexed to,
        uint256 amount,
        bytes data,
        bytes operatorData
    );

    event ChangedTranche(
        bytes32 indexed fromTranche,
        bytes32 indexed toTranche,
        uint256 amount,
    );

    // Operator Events
    event AuthorizedOperator(address indexed operator, address indexed tokenHolder);
    event RevokedOperator(address indexed operator, address indexed tokenHolder);

    event AuthorizedOperatorByTranche(bytes32 indexed tranche, address indexed operator, address indexed tokenHolder);
    event RevokedOperatorByTranche(bytes32 indexed tranche, address indexed operator, address indexed tokenHolder);

}
```

## 向后兼容性

为了保持向后兼容 ERC20/ERC777（以及其他可互换通证标准），有必要定义在执行 `transfer` / `send` 操作时（即未明确指定tranche时）使用的tranche

可以使用以下可选扩展和规则：

```solidity
function getDefaultTranches(address _tokenHolder) external view returns (bytes32[]);
function setDefaultTranche(bytes32[] _tranches) external;
```

如果具体实现保证每个通证持有者有少量可能的tranche，则迭代一个通证持有者的所有tranche是合理的（即使用 `tranchesOf` 定义 `defaultTranches` ）。

通证创建者必须指定ERC20 / ERC777函数用于所有通证持有者的默认tranche。通证持有者的完整通证余额的所有tranche的每个通证持有者或操作员可以更改通证持有者的默认tranche。通证持有者更改其默认tranche的能力允许他们更改ERC20 / ERC777传输中使用的未显式指定的tranche

以下是对ERC777函数的含义的描述：

* `send()` MUST 使用 `getDefaultTranche()` 获取默认tranche
* `operatorSend()` MUST 使用 `getDefaultTranche()` 获取默认tranche
* `burn()` MUST 使用 `getDefaultTranche()` 获取默认tranche
* `operatorBurn()` MUST 使用 `getDefaultTranche()` 获取默认tranche
* `balanceOf()` MUST 计算分配给通证持有者的所有tranche余额的总和
* `totalSupply()` MUST 计算此合约跟踪的所有通证
* `defaultOperators()` MUST 查询可以对所有地址和tranche进行操作的操作员列表
* `authorizeOperator()` MUST 为 `msg.sender` 的所有tranche授权一个操作员
* `revokeOperator()` MUST 撤销先前为 `msg.sender` 的所有tranche提供的一个操作员的授权
* `isOperatorFor()` MUST 查询 `_operator` 是否是一个 `_tokenHolder` 的所有tranche的操作员
* event `Minted()` and event `MintedByTranche()` MUST be emited for any increases in token supply
* event `Burned()` and event `BurnedByTranche()` MUST be emited for any decreases in token supply
* event `AuthorizedOperator()` MUST be emited by `authorizeOperator()`
* event `RevokedOperator()` MUST be emited by `revokeOperator()`



























