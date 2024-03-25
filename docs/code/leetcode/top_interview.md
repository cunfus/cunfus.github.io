# top-interview-150

[leetcode.com top-interview-150][0]


## Array/String

=== "88"
    [merge-sorted-array][88]

    Imagine there are two stacks, top_m and top_n.
    ```
    (this takes the situation to an extreme)

    Traverse top >= 0
        (if top_n < 0 and top_m >=0, [top] = [top_m] , and both --)
        (if top_m < 0 and top_n >=0, [top] = [top_n] , and both --)
        if top_m >=0 and top_n >=0
            if top_m > top_n, [top] = [top_m], and both --.
            else              [top] = [top_n], and both --.

    ```

=== "27"
    [remove-element][27]

    1. just Focus on the right index. 
    ```
    (this takes the situation to an extreme)

    Traverse
        look for != val from right，(if right < 0, break.)
        look for == val from left， (if left >= size, break.)
        if left < right , swap [left] & [right]; (else break.)
    ```
    2. fast-slow point, and Focus on the fast point.
    ```
    Traverse
        if [fast] == val, `fast` step forward.
        else            write [fast] to [slow], and step forward both. 
    ```

=== "26"
    [remove-duplicates-from-sorted-array][26]

    fast-slow point, and Focus on the fast point.
    ```
    (this takes the situation to an extreme)
    (if size <= 1, just return size)
    let's start at index 1.

    slow = fast = 1
    Traverse fast < size
        if [fast] == [fast - 1], `fast` step forward.
        else            write [fast] to [slow], and step forward both. 
    ```

=== "80"
    [remove-duplicates-from-sorted-array-ii][80]

    adjust thinking from [last problem][3].

    fast-slow point, Focus on `repeat times`, which is steps.
    ```
    steps = `repeat times`
    (this takes the situation to an extreme)
    (if size <= steps, just return size)
    
    let's start at index steps.
    slow = fast = steps

    [troubleshoot old = nums]
    ^
    |   Traverse fast < size
    |       if old[fast] == old[fast - steps], `fast` step forward.
    |       else        write old[fast] to num[slow], and step forward both. 
    |
    [i encounter a trouble, write will override origin value.
     as a result,  `if` condition failed.
     so i need an extra vector to store old value.]
    ```

=== "169"
    [majority-element][169]

    1. sort and fetch middle element.
    2. traverse nums, use `map` store counts; traverse `map`, found max counts.

=== "189"
    [rotate-array][189]

    Focus on the mapping relationship of indexes of elements.
    ```
    compare 1st element's index before/after mapping,
                                    using `k` and nums.size().

    you will find:
        new_index = (k + old_index) % nums.size()

    by using another vector,
    Traverse 
        new[(i + k) % size] = old[i]
    old = new
    ``` 

=== "121"
    [best-time-to-buy-and-sell-stock][121]

    Handleing continuous interval, let's use "sliding window".
    
    1. based on feeling.
    ```
    record min_price, max_profit
    Traverse 
        update max_profit
        update min_price
    ```
    
    2. sliding window. (fast-slow)
    ```
    record max_profit, left, right
    Traverse
        if [left] < [right], update max_profit, ++right
        else                 left = right     , ++right
    ```

=== "122"
    [best-time-to-buy-and-sell-stock-ii][122]

    Just count all monotonically increasing intervals.
    
    ```
    Traverse right < size
        if [right] > [left], add diff between two
        ++left, ++right
    ```
=== "55"
    [jump-game][55]

    fast-slow pointer
    ```
    Traverse
        confirm right based on [left]
        move left forward
    ```

## Hashmap

## Stack

## Linked List

## Binary Tree General

## Binary Search Tree

## Divide & Conquer

## Binary Search

## Heap

## Bit Manipulation

## Math




[0]: https://leetcode.com/studyplan/top-interview-150
[88]: https://leetcode.com/problems/merge-sorted-array
[27]: https://leetcode.com/problems/remove-element
[26]: https://leetcode.com/problems/remove-duplicates-from-sorted-array
[80]: https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii
[169]: https://leetcode.com/problems/majority-element
[189]: https://leetcode.com/problems/rotate-array
[121]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock
[122]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii
[55]: https://leetcode.com/problems/jump-game
