名词挺多的,其实并不复杂

####  公有链（Public Blockchain）

公有链通常也称为非许可链（Permissionless Blockchain），无官方组织及管理机构，无中心服务器，参与的借点按照系统规格自由接入网路、不受控制，节点间基于共识机制开展工作。

公有链是真正意义上的完全去中心化的区块链，它通过密码学保证交易不可篡，同时也利用密码学验证以及经济上的激励，在互为陌生的网络环境中建立共识，从而形成去中心化的信用机制。在公有链中的共识机制一般是工作量证明（PoW）或权益证明（PoS），用户对共识形成的影响力直接取决于他们在网络中拥有资源的占比。

公有链一般适合于虚拟货币、面向大众的电子商务、互联网金融等B2C、C2C或C2B等应用场景，比特币和以太坊等就是典型的公有链。

#### 联盟链（Consortium Blockchain）

联盟链是一种需要注册许可的区块链，这种区块链也称为许可链（Permissioned Blockchain）。联盟链仅限于联盟成员参与，区块链上的读写权限、参与记账权限按联盟规则来制定。整个网络由成员机构共同维护，网络接入一般通过成员机构的网关节点接入，共识过程由预先选好的节点控制。由于参与共识的节点比较少，联盟链一般不采用工作量证明的挖矿机制，而是多采用权益证明（PoS）或PBFT（Practical Byzantine Fault Tolerant）、RAFT等共识算法。

一般来说，联盟链适合于机构间的交易、结算或清算等B2B场景。例如在银行间进行支付、结算、清算的系统就可以采用联盟链的形式，将各家银行的网关节点作为记账节点，当网络上有超过2/3的节点确认一个区块，该区块记录的交易将得到全网确认。联盟链对交易的确认时间、每秒交易数都与公有链有较大的区别，对安全和性能的要求也比公共链高。

由40多家银行参与的区块链联盟R3和Linux基金会支持的超级账本（Hyperleder）项目都属于联盟链架构。目前国内有影响力的区块链联盟——中国分布式总账基础协议联盟（ChinaLedger）、中国区块链研究联盟、金链盟（金融区块链联盟）等——也都在致力于开发联盟区块链项目。

#### 私有链（Private Blockchain）

私有链建立在某个企业内部，系统的运作规则根据企业要求进行设定。

私有链的应用场景一般是企业内部的应用，如数据库管理、审计等；在政府行业也会有一些应用，比如政府的预算和执行，或者政府的行业统计数据，这个一般来说由政府登记，但公众有权力监督。私有链的价值主要是提供安全、可追溯、不可篡改、自动执行的运算平台，可以同时防范来自内部和外部对数据的安全攻击，这个在传统的系统是很难做到的。


![](/assets/a80udk6235740603190.png)



#### 侧链（Side chain）

侧链是用于确认来自于其它区块链的数据的区块链，通过双向挂钩（TwoWay Peg）机制使比特币、Ripple币等多种资产在不同区块链上以一定的汇率实现转移。

所谓“多种资产在不同区块链上转移”其实并不会实际发生。以比特币为例，侧链的运作机制是，将比特币暂时锁定在比特币区块链上，同时将辅助区块链上的等值数字货币解锁；当辅助区块链上的数字货币被锁定时，原先的比特币就被解锁。

侧链进一步扩展了区块链技术的应用范围和创新空间，使区块链支持包括股票、债券、金融衍生品等在内的多种资产类型，以及小微支付、智能合约、安全处理机制、真实世界财产注册等；侧链还可以增强区块链的隐私保护。



![](/assets/a21gs16235747632323.png)
