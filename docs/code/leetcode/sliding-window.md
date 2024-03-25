# sliding-window

记录在做 leetcode-problem-121 时的一些想法，比较适合用来理解滑动窗口。

这题难度属于 easy，先看用 fast-slow 方法的解：

```c++
int maxProfit(vector<int>& prices) {

    int max_profit = 0;
    
    int left = 0, right = 1;
    while (right < prices.size()) {
        if (prices[left] < prices[right]) {
            max_profit = max(max_profit, prices[right] - prices[left]);
            ++right;
        } else {
            left = right; /* find a smaller one to buy */
            ++right;
        }
    }

    return max_profit;
}
```

分支逻辑：价格大于买入点，更新最大利润；价格低于原先点，更新最小成本。

这么一想，属于这道题难度的原先解法应该是这样：

```c++

    int max_profit = 0;
    int min_price = prices[0];

    for (int i = 1; i < prices.size(); ++i) {
        max_profit = max(max_profit, prices[i] - min_price);
        min_price = min(min_price, prices[i]);
    }

    return max_profit;

```

分支逻辑：出现最大利润就更新；出现最小价格就更新。


滑动窗口和快慢指针基本上是一个东西，前者处理区间的意味更强，后者更多表达的是处理边界点。

可以看出，

- 窗口始终是向一个方向移动（不管是 `++right` 还是 `++i`），
- 右侧移动到一个新的位置，判断是否符合维持窗口的条件（比如这里的左边界和右边界两点呈单调增，来获取最大利润），
    - 符合条件，右边界继续移动，
    - 不符合条件，就要修改左边界（比如这里的 `left = right`），右边界继续移动，
- 直到抵达终点。


---

一些题外话：

在大脑里，上述过程其实是一个很快的思维过程。比如回过头去看，买的股票过去一年的趋势，只要一眼就能判断出什么点买入，什么点卖出能最大化盈利。

思维不单单体现在逻辑，还有图形，声音，语言，随机念头（引入不确定性）之类。这里训练的只有专门面对机器的逻辑（因为最终提交的只有代码，或许还能用其余的方式来表达）。
