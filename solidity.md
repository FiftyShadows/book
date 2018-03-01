### private 意味着它只能被合约内部调用； internal 就像 private 但是也能被继承的合约调用； external 只能从合约外部调用；最后 public 可以在任何地方调用，不管是内部还是外部。
```js
  function _generateRandomDna(string _str) private view returns (uint) {
        // 这里开始
    }
    
```
### 函数实际上没有改变 Solidity 里的状态，即，它没有改变任何值或者写任何东西。
这种情况下我们可以把函数定义为 view, 意味着它只能读取数据不能更改数据:
```js
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```
### pure 告诉我们这个函数不但不会往区块链写数据，它甚至不从区块链读取数据。这两种在被从合约外部调用的时候都不花费任何gas（但是它们在被内部其他函数调用的时候将会耗费gas）。

`msg.sender`指的是当前调用合约这的 adderss,在 Solidity 中，功能执行始终需要从外部调用者开始。 一个合约只会在区块链上什么也不做，除非有人调用其中的函数。所以 msg.sender总是存在的。

<br>
<br>


### Storage 变量是指永久存储在区块链中的变量。 Memory 变量则是临时的，当外部函数对某合约调用完成时，内存型变量即被移除。 你可以把它想象成存储在你电脑的硬盘或是RAM中数据的关系。

### 大多数时候你都用不到这些关键字，默认情况下 Solidity 会自动处理它们。 状态变量（在函数之外声明的变量）默认为“存储”形式，并永久写入区块链；而在函数内部声明的变量是“内存”型的，它们函数调用结束后消失。

然而也有一些情况下，你需要手动声明存储类型，主要用于处理函数内的 结构体 和 数组 时
```js
contract SandwichFactory {
  struct Sandwich {
    string name;
    string status;
  }

  Sandwich[] sandwiches;

  function eatSandwich(uint _index) public {
    // Sandwich mySandwich = sandwiches[_index];

    // ^ 看上去很直接，不过 Solidity 将会给出警告
    // 告诉你应该明确在这里定义 `storage` 或者 `memory`。

    // 所以你应该明确定义 `storage`:
    Sandwich storage mySandwich = sandwiches[_index];
    // ...这样 `mySandwich` 是指向 `sandwiches[_index]`的指针
    // 在存储里，另外...
    mySandwich.status = "Eaten!";
    // ...这将永久把 `sandwiches[_index]` 变为区块链上的存储

    // 如果你只想要一个副本，可以使用`memory`:
    Sandwich memory anotherSandwich = sandwiches[_index + 1];
    // ...这样 `anotherSandwich` 就仅仅是一个内存里的副本了
    // 另外
    anotherSandwich.status = "Eaten!";
    // ...将仅仅修改临时变量，对 `sandwiches[_index + 1]` 没有任何影响
    // 不过你可以这样做:
    sandwiches[_index + 1] = anotherSandwich;
    // ...如果你想把副本的改动保存回区块链存储
  }
}
```

<br>
<br>



### solidity 的 interface

```js
contract LuckyNumber {
  mapping(address => uint) numbers;

  function setNum(uint _num) public {
    numbers[msg.sender] = _num;
  }

  function getNum(address _myAddress) public view returns (uint) {
    return numbers[_myAddress];
  }
}

//首先，我们只声明了要与之交互的函数 —— 在本例中为 getNum —— 在其中我们没有使用到任何其他的函数或状态变量。

其次，我们并没有使用大括号（{ 和 }）定义函数体，我们单单用分号（;）结束了函数声明。这使它看起来像一个合约框架。
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}


//调用方式
contract MyContract {
  address NumberInterfaceAddress = 0xab38...;
  // ^ 这是FavoriteNumber合约在以太坊上的地址
  NumberInterface numberContract = NumberInterface(NumberInterfaceAddress);
  // 现在变量 `numberContract` 指向另一个合约对象

  function someFunction() public {
    // 现在我们可以调用在那个合约中声明的 `getNum`函数:
    uint num = numberContract.getNum(msg.sender);
    // ...在这儿使用 `num`变量做些什么
  }
}
```
<br>
<br>
###solidity 多返回值的处理方式
```js
function multipleReturns() internal returns(uint a, uint b, uint c) {
  return (1, 2, 3);
}

function processMultipleReturns() external {
  uint a;
  uint b;
  uint c;
  // 这样来做批量赋值:
  (a, b, c) = multipleReturns();
}

// 或者如果我们只想返回其中一个变量:
function getLastReturnValue() external {
  uint c;
  // 可以对其他字段留空:
  (,,c) = multipleReturns();
}
```

<br>
<br>

https://openzeppelin.org/api/docs/get-started.html    OpenZeppelin Solidity 库
<br>
<br>
###所有者合约
```js
/**
 * @title Ownable
 * @dev The Ownable contract has an owner address, and provides basic authorization control
 * functions, this simplifies the implementation of "user permissions".
 */
contract Ownable {
  address public owner;
  event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

  /**
   * @dev The Ownable constructor sets the original `owner` of the contract to the sender
   * account.
   */
  function Ownable() public {
    owner = msg.sender;
  }

  /**
   * @dev Throws if called by any account other than the owner.
   */
  modifier onlyOwner() {
    require(msg.sender == owner);
    _;
  }

  /**
   * @dev Allows the current owner to transfer control of the contract to a newOwner.
   * @param newOwner The address to transfer ownership to.
   */
  function transferOwnership(address newOwner) public onlyOwner {
    require(newOwner != address(0));
    OwnershipTransferred(owner, newOwner);
    owner = newOwner;
  }
}
```

<br>
<br>

### solidity 的坑爹时间

变量 now 将返回当前的unix时间戳（自1970年1月1日以来经过的秒数）。

Solidity 还包含秒(seconds)，分钟(minutes)，小时(hours)，天(days)，周(weeks) 和 年(years) 等时间单位。它们都会转换成对应的秒数放入 uint 中。所以 1分钟 就是 60，1小时是 3600（60秒×60分钟），1天是86400（24小时×60分钟×60秒），以此类推。
```js
uint lastUpdated;

// 将‘上次更新时间’ 设置为 ‘现在’
function updateTimestamp() public {
  lastUpdated = now;
}

// 如果到上次`updateTimestamp` 超过5分钟，返回 'true'
// 不到5分钟返回 'false'
function fiveMinutesHavePassed() public view returns (bool) {
  return (now >= (lastUpdated + 5 minutes));
}
```


<br>
<br>


### view 函数不消耗 gas,这是因为 view 函数不会真正改变区块链上的任何数据 - 它们只是读取。因此用 view 标记一个函数，意味着告诉 web3.js，运行这个函数只需要查询你的本地以太坊节点，而不需要在区块链上创建一个事务（事务需要运行在每个节点上，因此花费 gas）。一般写为external view


###提现合约
```js
contract GetPaid is Ownable {
  function withdraw() external onlyOwner {
    owner.transfer(this.balance);
  }
}
```
###随机数keccak256函数
```js
//生成一个0到100的随机数:
uint randNonce = 0;
uint random = uint(keccak256(now, msg.sender, randNonce)) % 100;
randNonce++;
uint random2 = uint(keccak256(now, msg.sender, randNonce)) % 100;
```
这个方法首先拿到 now 的时间戳、 msg.sender、 以及一个自增数 nonce （一个仅会被使用一次的数，这样我们就不会对相同的输入值调用一次以上哈希函数了）。

然后利用 keccak 把输入的值转变为一个哈希值, 再将哈希值转换为 uint, 然后利用 % 100 来取最后两位, 就生成了一个0到100之间随机数了。
https://ethereum.stackexchange.com/questions/191/how-can-i-securely-generate-a-random-number-in-my-smart-contract  一个好的随机数选择