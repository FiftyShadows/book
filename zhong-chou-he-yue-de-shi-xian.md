#### 1. 什么是ICO?

　ICO是以初始产生的数字加密货币作为投资回报的一种筹措资金的方式，它的概念源自证券界的Initial Public Offering(IPO，首次公开发行)。

　　相较于传统意义上的IPO，ICO具有可以缩短投融资链、降低投融资门槛、流动性佳、全球性投资等优势。常见的ICO里，数字货币和区块链项目向早期爱好者出售项目代币。项目团队通过ICO获取技术开发和市场拓展资金；而项目爱好者通过ICO支持项目，同时也可在对应代币进入交易市场后选择交易退出。

　　当你有一个好的想法，需要大家的资金来资助你。你可以使用众筹合约来发起请求捐款。众筹合约的基本思路是，你设定一个众筹目标，在达到目标的最后期限时，如果没有完成众筹，所有的捐款将被退回，因此减少了捐赠者的风险。由于代码是开放的，可被审计的，也就不需要一个集中的、可信的平台来担保，每个捐款的人，只需要支付一定的gas。

####2. 众筹的奖励-代币

一般来说，那些筹集资金的人在资金筹集和资金管理不善之后，根本就不能说这笔钱是如何使用的，这常常导致项目根本无法交付任何东西。这时我们可以使用智能合适中投票的方式来做决定，这样对所有人都是公平的。(这个例子不在本文中介绍，可以参考链接)

　　在下面的例子里，我们在众筹中，主要解决两个重要的问题：如何管理和保存用于奖励的代币；筹集奖金后如何使用。

　　传统的众筹或奖励记录通常有一个中央数据库，来保存、跟踪所有捐助者的过程：谁错过了众筹的最后期限了，谁在众筹过程中捐赠了多少等。与之相反，在区块链中我们将以分散的方式来做这件事，只需创建一个标记来记录众筹的每一条记录、奖励了多少代币，后面每个捐赠者都可以得到一个他们可以交易、出售或保留的代币。如果要给予实物奖励，生产者只需要交换实物产品的代币。捐赠者也可以将代币做为纪念品保留，不管这个众筹项目有没有达到它的目标，都可以收藏。

```javascript
pragma solidity 0.4.18;
/**
 * 一个简单的代币合约。
 */
 contract token {

     string public standard = 'https://mshk.top';
     string public name; //代币名称
     string public symbol; //代币符号比如'$'
     uint8 public decimals = 2;  //代币单位，展示的小数点后面多少个0,和以太币一样后面是是18个0
     uint256 public totalSupply; //代币总量
     /* This creates an array with all balances */
     mapping (address => uint256) public balanceOf;

     event Transfer(address indexed from, address indexed to, uint256 value);  //转帐通知事件


     /* 初始化合约，并且把初始的所有代币都给这合约的创建者
      * @param _owned 合约的管理者
      * @param tokenName 代币名称
      * @param tokenSymbol 代币符号
      */
     function token(address _owned, string tokenName, string tokenSymbol) {
         //合约的管理者获得的代币总量
         balanceOf[_owned] = totalSupply;

         name = tokenName;
         symbol = tokenSymbol;

     }

     /**
      * 转帐，具体可以根据自己的需求来实现
      * @param  _to address 接受代币的地址
      * @param  _value uint256 接受代币的数量
      */
     function transfer(address _to, uint256 _value){
       //从发送者减掉发送额
       balanceOf[msg.sender] -= _value;

       //给接收者加上相同的量
       balanceOf[_to] += _value;

       //通知任何监听该交易的客户端
       Transfer(msg.sender, _to, _value);
     }

     /**
      * 增加代币，并将代币发送给捐赠新用户
      * @param  _to address 接受代币的地址
      * @param  _amount uint256 接受代币的数量
      */
     function issue(address _to, uint256 _amount) public{
         totalSupply = totalSupply + _amount;
         balanceOf[_to] += _amount;

         //通知任何监听该交易的客户端
         Transfer(this, _to, _amount);
     }
  }

/**
 * 众筹合约
 */
contract Crowdsale is token {
    address public beneficiary = msg.sender; //受益人地址，测试时为合约创建者
    uint public fundingGoal;  //众筹目标，单位是ether
    uint public amountRaised; //已筹集金额数量， 单位是wei
    uint public deadline; //截止时间
    uint public price;  //代币价格
    bool public fundingGoalReached = false;  //达成众筹目标
    bool public crowdsaleClosed = false; //众筹关闭


    mapping(address => uint256) public balance; //保存众筹地址

    //记录已接收的ether通知
    event GoalReached(address _beneficiary, uint _amountRaised);

    //转帐通知
    event FundTransfer(address _backer, uint _amount, bool _isContribution);

    /**
     * 初始化构造函数
     *
     * @param fundingGoalInEthers 众筹以太币总量
     * @param durationInMinutes 众筹截止,单位是分钟
     * @param tokenName 代币名称
     * @param tokenSymbol 代币符号
     */
    function Crowdsale(
        uint fundingGoalInEthers,
        uint durationInMinutes,
        string tokenName,
        string tokenSymbol
    ) token(this, tokenName, tokenSymbol){
        fundingGoal = fundingGoalInEthers * 1 ether;
        deadline = now + durationInMinutes * 1 minutes;
        price = 500 finney; //1个以太币可以买 2 个代币
    }


    /**
     * 默认函数
     *
     * 默认函数，可以向合约直接打款
     */
    function () payable {

        //判断是否关闭众筹
        require(!crowdsaleClosed);
        uint amount = msg.value;

        //捐款人的金额累加
        balance[msg.sender] += amount;

        //捐款总额累加
        amountRaised += amount;

        //转帐操作，转多少代币给捐款人
        issue(msg.sender, amount / price * 10 ** uint256(decimals));
        FundTransfer(msg.sender, amount, true);
    }

    /**
     * 判断是否已经过了众筹截止限期
     */
    modifier afterDeadline() { if (now >= deadline) _; }

    /**
     * 检测众筹目标是否已经达到
     */
    function checkGoalReached() afterDeadline {
        if (amountRaised >= fundingGoal){
            //达成众筹目标
            fundingGoalReached = true;
            GoalReached(beneficiary, amountRaised);
        }

        //关闭众筹
        crowdsaleClosed = true;
    }


    /**
     * 收回资金
     *
     * 检查是否达到了目标或时间限制，如果有，并且达到了资金目标，
     * 将全部金额发送给受益人。如果没有达到目标，每个贡献者都可以退出
     * 他们贡献的金额
     */
    function safeWithdrawal() afterDeadline {

        //如果没有达成众筹目标
        if (!fundingGoalReached) {
            //获取合约调用者已捐款余额
            uint amount = balance[msg.sender];

            if (amount > 0) {
                //返回合约发起者所有余额
                msg.sender.transfer(amount);
                FundTransfer(msg.sender, amount, false);
                balance[msg.sender] = 0;
            }
        }

        //如果达成众筹目标，并且合约调用者是受益人
        if (fundingGoalReached && beneficiary == msg.sender) {

            //将所有捐款从合约中给受益人
            beneficiary.transfer(amountRaised);

            FundTransfer(beneficiary, amount, false);
        }
    }
}
```