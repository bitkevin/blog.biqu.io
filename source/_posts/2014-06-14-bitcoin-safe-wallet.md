title: '比特币安全钱包探讨'
date: 2014-06-14 03:47:36
tags:
---

从财富不可侵犯角度讲，比特币是人类史上最安全的货币。但令人郁闷的是，安全地存储比特币却遇到诸多麻烦。私钥机制使得其神圣不可侵犯，也使其安全存储遇到麻烦。

自行存储管理比特币非常不现实，安全性差，丢币概率高。线上钱包主要分两类：链下(Off-Chain)、链上(On-Chain)。链下钱包服务即银行模式，若服务商出现任何问题，储户资金即出问题；链上钱包“理论上”安全，因其不掌握用户私钥（仅保存私钥的密文），但也不绝对，例如服务器被入侵后，钱包关键代码被恶意修改，导致能够恢复出用户私钥，无论多少币，均瞬间被转移。也就是说，目前还没有一种逻辑上安全的钱包方案，能够满足绝对安全存储。

这里尝试提出一种钱包的实现机制，从逻辑满足绝对安全。分为服务端，客户端两个部分。

服务端

* 数据服务。只读模式，包括：
  * balance by address
  * unspent outputs
  * tx/block query
* 广播服务。半只读模式，协助将交易(Raw Tx)广播至比特币网络。

客户端

* 硬件部分
  * 支持如ECDSA, SHA256等
  * 支持硬件隔离技术，存储敏感数据，如私钥数据
* APP部分
  * 通过外设与硬件部分进行通信
  * UI，实现钱包操作交互

客户端硬件部分像拉卡拉类似的一个东西，通过耳机走音频设备与手机APP通讯。

* 当用户使用钱包时，插入硬件部分，启动APP，此时APP从硬件读取比特币地址列表，并去服务端查询地址余额，APP更新用户最新余额。收款同理。
* 当用户发币时，APP根据比特币地址从服务端获取相关未花费交易，设定转出地址和金额，APP根据未花费交易构建待签名的空交易，将空交易传入硬件，通知其完成签名。硬件设备从隔离区读取数据并由加密芯片完成签名后，将完整交易放入普通数据区，APP从普通数据区读取签名后的交易并提交至服务端广播。

安全点：

* 服务端必须是只读模式的。只读的设计，保障客户端不会提交敏感数据，即使服务端被黑，也无法截获用户私钥。
  * 这块的工作就是将bitcoind里的key-value的数据，梳理为关系型结构化数据，并对外服务。社区已经有类似的开源方案。
* 客户端必须实现硬件隔离，使得操作系统无法读取敏感信息，木马、恶意软件就没有办法盗取私钥







若是自行保存的话，担心硬盘损坏遗失，担心脑震荡忘记密码，私钥告知老婆又担心感情背叛，一不小心就丢失人生财富，同时还要掌握各种技巧工具，真是各种麻烦，所以近两年线上钱包(Online Wallet)慢慢成为主流方案，从屌丝至万币户均使用线上钱包。线上钱包基本可以分为两类：链上(On-Chain)、链下(Off-Chain)。

链下钱包是钱庄，基于一个信任机制运作，用户的余额由钱庄记账，存币、取币、转账只需账目变更，私钥不由用户控制。链下钱包的安全问题是服务商的问题：管理不善导致丢币，或者老板跑路，

链上钱包只是工具，服务商不掌握用户私钥，但存储用户私钥密文，