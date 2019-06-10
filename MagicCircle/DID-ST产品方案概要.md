# DID-ST服务产品方案概要

MagicCircle实现一种在ST领域内通用的使用DID的数字身份信息服务方案
http://eips.ethereum.org/EIPS/eip-1484

## DID服务

### DID介绍

https://w3c-ccg.github.io/did-spec/

### 密钥管理与轮换

* 由用户自主在合约管理密钥
	* App内提供管理界面
	* 从合约解析did-document以用于信息验证
* 参考链接
	* https://github.com/NoahZinsmeister/ERC-1484
	* https://github.com/uport-project/ethr-did

### 身份信息认证

用于标记身份合法且KYC通过
https://github.com/ethereum/wiki/wiki/ERC-735:-Claim-Holder-Registry-vs.-in-contract

* 链上claim标记 
	* https://github.com/ethereum/EIPs/issues/735
	* IDHub向用户在链上颁发claim
* 链下中心化服务器
	* 同时和身份信息存储
	* 可同时提供身份信息读取服务
	* 使用标准化的身份信息数据结构https://w3c.github.io/vc-use-cases/

## ST服务
ST领域中的相关身份服务的需求

### ST合法身份
用户提交必要的身份信息以获得合法使用ST服务的授权

* 身份信息项目及其数据格式
	* 身份信息保存在IDHub服务器
	* 身份信息的读取接口和数据格式及其所需的授权信息格式
* 身份信息验证[KYC/AML]
	* 如何使用KYC/AML服务商的接口[流程描述和数据格式]
* 身份信息的管理流程
	* APP和网页版身份信息提交的页面和交互流程
	* 用户端身份信息的可视化界面和管理界面及其流程
	* 身份信息读取的授权流程及所需的前置组件和数据格式

### ST领域数字身份钱包
* ST资产管理
* ST市场[DEX DApp浏览器]
* ST身份信息管理[如何配合DID密钥管理]

## 加密技术组件
https://github.com/ethereum/EIPs/issues/191