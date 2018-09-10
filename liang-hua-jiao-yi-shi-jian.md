####我写的是数字货币的量化,主策略为追涨杀跌和止盈止损,选择的交易所为币安
github地址:https://github.com/qq976739120/quantitative_trading
####1. 有一个已经有的币种的dict,price注意是均价,

```python
have_coin_dict =  {
    "BTCETH": {
        "price": 9.9,
        "amount": 434,
        "last_action":"buy",
        "last_trade_time":"2018-07-07 15:42:15"

    },
    "EOSETH": {
        "price": 7.1,
        "amount": 235,
        "last_action": "sell",
        "last_trade_time": "2018-07-07 15:42:15"
    },

}
```

#### 一个只最开始启动的get_price函数,为了获得symbol_price,price注意是list,隔20s会启动个任务,按时间顺序append当时的价格进去

```python
def get_price():
    depth = client.get_all_tickers()
    symbol_price = {}
    for i in depth:
        symbol = i.get("symbol")
        if symbol.endswith("ETH"):
            list_ = []
            list_.append(i.get("price"))
            symbol_price[symbol] = list_
    return symbol_price

# {'QTUMETH': ['0.01723700'], 'EOSETH': ['0.01674300']}
```
