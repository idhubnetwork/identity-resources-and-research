# 去中心化数字身份产品方案
## [1]生成去中心化数字身份
### 功能定义
用户新建一个自我独立的去中心化数字身份，包括生成身份标识符密钥对、身份找回密钥对、在智能合约注册身份记录（ERC1484）和创建信息声明智能合约（ERC735、是否实现待定）。
### 使用场景
新用户使用新密钥对新建一个去中心化数字身份
### 功能流程图

### 功能流程
* 前置条件 - 下载IDHub移动端App（数字身份钱包）
* 功能规则
	1. 生成两组ECDSA密钥对，一组用作身份标识符，一组用作身份找回
	2. 将身份注册到ERC1484合约`createIdentity`，其中
		* `recoveryAddress`是上一步身份找回密钥对地址
		* `identity`表现为`associatedAddress`形式，值是上一步身份标识符密钥对地址
		* `resolvers`包括`ERC1056`智能合约地址[DID]和`ERC725RegistryResolver`智能合约地址[Claims]（是否实现待定）
	3. 在`ERC725RegistryResolver`合约中调用`create725`为用户建立ERC725档案以用于可验证声明的链上发布（是否实现待定）
* 后置流程 - 身份信息认证

### 功能原型

### 测试要点

### 功能作用或优势
用户通过区块链可以使用自我确权的去中心化数字身份，由于非对称加密的特点，去中心化数字身份完全由用户自主管理。通过智能合约记录属性可以使去中心化数字身份具有现实意义，信息声明智能合约则可以提供身份信息可信的保障。

## [2]创建去中心化数字身份
### 功能定义
用户使用提前准备好的密钥对创建一个自我独立的去中心化数字身份，包括在智能合约注册身份记录（ERC1484）和创建信息声明智能合约（ERC735、是否实现待定）。
### 使用场景
新用户使用已拥有的密钥对创建一个去中心化数字身份
### 功能流程图

### 功能流程
* 前置条件
	* 下载IDHub移动端App（数字身份钱包）
	* 两组ECDSA密钥对，一组用作身份标识符，一组用作身份找回，支持格式为助记词
* 功能规则 
	1. 将身份注册到ERC1484合约`createIdentity`，其中
		* `recoveryAddress`是提前准备好的身份找回密钥对地址
		* `identity`表现为`associatedAddress`形式，值是提前准备好的身份标识符密钥对地址
		* `resolvers`包括`ERC1056`智能合约地址[DID]和`ERC725RegistryResolver`智能合约地址[Claims]（是否实现待定）
	2. 在`ERC725RegistryResolver`合约中调用`create725`为用户建立ERC725档案以用于可验证声明的链上发布（是否实现待定）
* 后置流程 - 身份信息认证

### 功能原型

### 测试要点

### 功能作用或优势
用户通过区块链可以使用自我确权的去中心化数字身份，由于非对称加密的特点，去中心化数字身份完全由用户自主管理。通过智能合约记录属性可以使去中心化数字身份具有现实意义，信息声明智能合约则可以提供身份信息可信的保障。

## [3]恢复去中心化数字身份
### 功能定义
用户使用已注册过的密钥对在另一个设备恢复原来拥有的去中心化数字身份。
### 使用场景
原用户将去中心化数字身份更换到另一移动端设备。
### 功能流程图

### 功能流程
* 前置条件
	* 新设备下载IDHub移动端App（数字身份钱包）
	* 已在旧设备拥有去中心化数字身份
* 功能规则
	1. 手动填入或二维码扫描录入身份标识符和身份找回密钥对的私钥，支持格式如下
		* 十六进制字符串
		* 助记词（推荐格式）
		* Keystore文件
	2. 从智能合约读取相关信息展示去中心化数字身份主页
* 后置流程 - 去中心化数字身份主页

### 功能原型

### 测试要点

### 功能作用或优势
用户通过区块链可以使用自我确权的去中心化数字身份，由于非对称加密的特点，去中心化数字身份完全由用户自主管理。通过智能合约记录属性可以使去中心化数字身份具有现实意义，信息声明智能合约则可以提供身份信息可信的保障。

## [4]保存去中心化数字身份找回密钥
**可选方案之一，应继续研究其他解决方案**
### 功能定义
提示用户务必通过冷存储形式保存找回密钥对的私钥
### 使用场景
每次新生成、创建或恢复去中心化数字身份之后提示用户保存找回密钥对的私钥
### 功能流程图

### 功能流程
* 前置条件 - 新生成、创建或恢复去中心化数字身份之后
* 功能规则
	1. 提示用户冷存储找回密钥对的私钥，然后点击下一步
	2. 乱序验证用户的冷存储行为
		* 顺序还原正确则通过进入主页
		* 顺序还原错误则退回上一步
* 后置流程 - 去中心化数字身份主页

### 功能原型

### 测试要点

### 功能作用或优势
用户通过区块链可以使用自我确权的去中心化数字身份，由于非对称加密的特点，去中心化数字身份完全由用户自主管理。通过智能合约记录属性可以使去中心化数字身份具有现实意义，信息声明智能合约则可以提供身份信息可信的保障。

## [5]找回去中心化数字身份
### 功能定义
用户通过找回密钥对的私钥在App内找回重建去中心化数字身份。
### 使用场景
用户丢失设备之后在另一设备的App内通过找回密钥重建已丢失设备内的去中心化数字身份。
### 功能流程图

### 功能流程
* 前置条件
	* 新设备下载IDHub移动端App（数字身份钱包）
	* 冷存储的找回密钥对的私钥
	* 原有数字身份标识符（EIN或数字身份以太坊账户地址）
* 功能规则
	1. 用户进入找回页面填入以下数据并确定
		* 想要找回的数字身份以太坊账户地址（可选）
		* 想要找回的EIN
	2. 用户选取重建方式
		* 重新生成新的身份标识符密钥对，其中私钥保存且地址作为`newAssociatedAddress `参数的值进入下一步
		* 亦或者用户自己手动输入或二维码录入身份标识符密钥对的私钥，计算出地址作为`newAssociatedAddress `参数的值进入下一步
	3. 用户手动输入或二维码扫描录入原有身份找回密钥对的私钥并进行本地保存
	4. 调用`IdentityRegistry `合约（ERC1484）的`triggerRecovery`接口
	5. App读取合约数据显示身份主页
* 后置流程 - 去中心化数字身份主页

### 功能原型

### 测试要点

### 功能作用或优势
用户通过区块链可以使用自我确权的去中心化数字身份，由于非对称加密的特点，去中心化数字身份完全由用户自主管理。通过智能合约记录属性可以使去中心化数字身份具有现实意义，信息声明智能合约则可以提供身份信息可信的保障。

## [6]去中心化数字身份链上协议及其智能合约实现
暂定参考以下：

* http://eips.ethereum.org/EIPS/eip-1484#recovery
* https://github.com/NoahZinsmeister/ERC-1484

## [7]IDHub以太坊交互服务
提供一个用户通过元**交易**（特指以太坊交易的概念）与链交互的服务端，以帮助用户节省gas费用

## [8]登录中心化服务商
类似于open-id的概念，但此功能仅特指登录环节
### 功能定义
用户通过IDHub移动端App登录中心化网站
### 使用场景
用户访问兼容去中心化数字身份的中心化网站，无需注册，直接通过IDHub移动端App登录中心化网站访问用户中心或其他后续服务
### 功能流程图

### 功能流程
* 前置条件
	* 中心化网站愿意兼容去中心化数字身份，提供去中心化数字身份登录服务
	* 用户拥有去中心化数字身份和IDHub移动端App（数字身份钱包）
* 功能规则
	1. 用户通过PC端网页浏览器输入URI进入中心化网站的登录页面，点击【通过去中心化数字身份登录】，登录页面展示二维码，二维码信息说明如下
		* 采用json字符串数据格式
		* 必须包含`sub`字段且值恒为`did`
		* 必须包含`act`字段且值恒为`login`
		* 必须包含`jti`字段且值采用定制化的后续可以验证的UUID
		* 必须包含`aud`字段且必须为中心化网站登录页面URL，建议从前端页面`location.href`属性获得
		* 示例
		```json
		{
			"sub": "did",
			"act": "login",
			"aud": "www.idhub.com",
			"jti": "550E8400-E29B-11D4-A716-446655440000"
		}
		 ```
	2. 用户使用IDHub移动端App（数字身份钱包）扫描前端页面提供的二维码，前端页面发起带有`jti`参数的轮询请求检测登录行为的回调状态，IDHub移动端App读取信息之后生成待认证信息，待认证信息说明如下
		* json字符串数据格式且原样保留上一步二维码扫描信息里的字段和值
		* 必须包含`exp`字段且值为当前时间增加10秒，数据格式必须为`Unix timestamp`
		* 必须包含`iss`字段且值为当前用户的去中心化数字身份标识符地址，数据格式采用十六进制字面值
	3. 用户用身份标识符的私钥签名待认证信息生成一个[JSON Web Token](https://jwt.io/)形式的登录令牌，令牌信息说明如下
		* `HEADER`字段编码前值恒为
		```json
		{
			"alg": "ES256k",
			"typ": "JWT"
		}
		```
		* `PAYLOAD`字段的值必须原样保留前两步生成的带认证信息里的字段和值
		* `SIGNATURE`字段的值采用以太坊非对称加密算法（此文档内暂定命名为`ES256k`）生成，算法参考[EIP191](https://github.com/ethereum/EIPs/issues/191)
		* JSON Web Token 的组装方式请参考[此处](https://jwt.io/introduction/)
	4. 用户将登陆令牌（JWT）发送到中心化网站，中心化网站验证令牌有效性并设置登录行为的状态以便用户发起登录行为的前端页面响应回调，验证规则如下
		* `exp`必须大于服务器当前时间
		* 任何字段的值必须符合前三步字段描述的要求
		* `jti`必须在服务端存在且唯一
		* 对登录令牌（JWT）使用以太坊非对称加密算法的`ecrecover`计算出的地址必须和`iss`字段的值相同
	5. 验证通过之后，中心化网站丢弃令牌标记登录行为成功，前端页面轮询回调响应进入用户中心或后续服务页面，此步骤有如下说明
		* 第一次登录此网站时，中心化网站需要在用户系统中新建一个使用去中心化数字身份标识的用户
		* 用户中心或后续服务页面依靠登录令牌（JWT）的`iss`字段的值查找和标示相应用户
* 后置流程 - 中心化网站用户中心或后续服务

### 功能原型

### 测试要点

### 功能作用或优势

## [9]IDHub会员申请
**会员服务后续定义**
### 功能定义
用户通过上传个人身份信息，经过审核之后可成为IDHub会员。
### 使用场景
提交ST合格投资人、ST合格购买人认证所需的必要身份信息，提交会员服务所必须的身份信息等。
### 功能流程图

### 功能流程
* 前置条件
	* 参考[[8]登录中心化服务商](#[8]登录中心化服务商)所述登录IDHub网页版网站
	* 亦或者在App主页内打开`会员申请`页面
	* 用户同意**会员申请协议**
* 功能规则
	1. 填入基本身份信息，包括
		* 姓名，`姓/Last Name      名/First Name`，必填
		* 生日，`生日/Date of Birth        （月）/      （日）/     （年）`，必填
		* 国籍，`国籍/ Nationality`，必填
		* 居住国，`居住国/ Country of Residence`，必填
		* 身份证件号码，`身份证件号码/ID Number`
			* 美国可选，若填写需上传身份证件照片（图片或PDF）
			* 中国必填，需上传身份证件照片（图片或PDF）
		* 护照号码，`护照号码/Passport Number`，可选
			* 若填写需上传护照照片（图片或PDF）
	2. 根据国籍和居住地填写基本认证信息，说明如下
		* 若国籍或居住国任一为美国，则填写
			* 地址，`地址/Address`，需上传住址证明照片`Proof of Address`（图片或PDF）
			* 邮箱，`邮箱/eMail`
			* 手机号，`手机号/Phone Number`
			* 纳税号，`纳税号/TaxID`
			* 社保号，`社保号 SSN` 
		* 国籍和居住国都不是美国，则**暂定**填写
			* 地址，`地址/Address`
			* 邮箱，`邮箱/eMail`
			* 手机号，`手机号/Phone Number`
	3. 进行已填信息确认`information confirm`
	4. 进行双因子认证确保邮箱和手机号信息真实可控，以便后续进行账户信息权限管理
		* 短信验证码 `SMS Code`
		* 邮箱认证 `Email confirmation`
	5. 通知用户等待信息审核，提交以下信息至IDHub数据库并严密保存
		* 以上所有已填信息
		* 用户身份标识符密钥对的私钥签名过的会员申请协议，采用EIP191签名方案
	6. 审核完毕通知用户
		* 未通过提示用户更正信息再次提交
		* 若通过则颁发IDHub会员的可验证声明，App内点亮IDHub会员图标
* 后置流程
	* 申请ST合格投资人或合格购买人的资格
	* IDHub的会员服务

## [10]合格投资人认证
### 功能定义
用户成为IDHub会员之后，获取并保留合格投资人资格。
### 使用场景
获得IDHub认证的ST合格投资人的资格。
### 功能流程图

### 功能流程
* 前置条件
	* 参考[[8]登录中心化服务商](#[8]登录中心化服务商)所述登录IDHub网页版网站
	* 亦或者在App主页内打开`合格投资人认证`页面
	* 用户同意**合格投资人申请协议**
* 功能规则
	1. 选择符合以下哪一类情况，上传相应的文件
		* 连续三年年收入超过20万美金，或与配偶一起超过30万
Any individual who had an income in excess of US$200,000 in each of the two most recent years or joint income with that person’s spouse in excess of US$300,000 in each of those years and reasonably expects to reach the same income level in the current year.(上传图片或PDF格式的银行流水）
		* 值超过1百万美金（除自住房之外）Any individual whose net worth, or joint net worth with that person’s spouse, at the time of his or her purchase of an Interest, exceeds US$1,000,000. As used herein, “net worth” means the excess of total assets at fair market value, including homes (but excluding the value of the undersigned’s primary residence), home furnishings and automobiles, over total liabilities.（上传图片或PDF格式的资产证明/负债证明，如果国籍或居住国任一为美国需要上传税表）
	2. 通知用户等待信息审核，提交以下信息至IDHub数据库并严密保存
		* 以上所有已填和上传信息
		* 用户身份标识符密钥对的私钥签名过的合格投资人申请协议，采用EIP191签名方案
	3. 审核完毕通知用户
		* 未通过提示用户更正信息再次提交
		* 若通过则颁发合格投资人的可验证声明（有效期3个月），App内点亮合格投资人图标
	4. 监测合格投资人的信息时效性，原则为每次相关投资行为都要求合格投资人的资格认证来源于最近三个月内，因此
		* 合格投资人的可验证声明有效期为3个月，到期之后应被丢弃
		* 申请合格投资人的所需上述信息3个月有效期由IDHub服务端维护
		* 每三个月App内点灭合格投资人图标，提示用户重新申请合格投资人资格并更新必要的身份信息
* 后置流程
	* 合格投资人可享服务
	* ST用户身份认证服务

## [11]合格投资人认证
### 功能定义
用户成为IDHub会员之后，获取并保留合格购买人资格。
### 使用场景
获得IDHub认证的ST合格购买人的资格。
### 功能流程图

### 功能流程
* 前置条件
	* 参考[[8]登录中心化服务商](#[8]登录中心化服务商)所述登录IDHub网页版网站
	* 亦或者在App主页内打开`合格购买人认证`页面
	* 用户同意**合格购买人申请协议**
* 功能规则
	1. 选择符合以下哪一类情况，上传相应的图片或PDF格式的CPA/CFA律师证明文件
		* A natural person (including any person who holds a joint, community property, or other similar shared ownership interest in an issuer that is excepted under Section (c)(7) of the 1940 Act with that person’s qualified purchaser spouse) who owns not less than $5,000,000 in investments as defined by the Securities and Exchange Commission.(上传图片或PDF格式的银行流水）
		* A trust that is not covered by clause the family entity definition and that was not formed for the specific purpose of acquiring the Partnership Interest offered, as to which the trustee or other person authorized to make decisions with respect to the trust, and each settlor of the other person who has contributed assets to the trust, a natural person, family entity.
		* A natural person, a corporation, partnership, association, joint stock company, trust, fund or other organized group of persons, whether incorporated or not, acting for his/her/its own account or the account of other qualified purchasers, (i) who in the aggregate owns and invests on a discretionary basis, not less than $25,000,000 in investments and (ii) An entity in which each of the beneficial owners of the entity’s securities is a qualified purchaser, as that term is defined in the 1940 Act.
		* A “knowledgeable employee,” as defined for purposes of Section 3(c)(5) under the 1940 Act.
	2. 通知用户等待信息审核，提交以下信息至IDHub数据库并严密保存
		* 以上所有已填和上传信息
		* 用户身份标识符密钥对的私钥签名过的合格购买人申请协议，采用EIP191签名方案
	3. 审核完毕通知用户
		* 未通过提示用户更正信息再次提交
		* 若通过则颁发合格购买人的可验证声明（有效期3个月），App内点亮合格购买人图标
	4. 监测合格购买人的信息时效性，原则为每次相关投资行为都要求合格购买人的资格认证来源于最近三个月内，因此
		* 合格购买人的可验证声明有效期为3个月，到期之后应被丢弃
		* 申请合格购买人的所需上述信息3个月有效期由IDHub服务端维护
		* 每三个月App内点灭合格购买人图标，提示用户重新申请合格购买人资格并更新必要的身份信息
* 后置流程
	* 合格购买人可享服务
	* ST用户身份认证服务




