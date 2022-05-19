# leetcode 总结

## 目录

[TOC]

### 周赛

####  第293周赛（题目在 leetcode 上搜题号就搜得到）

##### 2273. 移除字母异位词后的结果数组(easy): [link](https://leetcode.cn/problems/find-resultant-array-after-removing-anagrams/)

###### 题目

>  给你一个下标从 **0** 开始的字符串 `words` ，其中 `words[i]` 由小写英文字符组成。
> 在一步操作中，需要选出任一下标 `i` ，从 `words` 中 **删除** `words[i]` 。其中下标 `i` 需要同时满足下述两个条件：
>
> 1. `0 < i < words.length`
> 2. `words[i - 1]` 和 `words[i]` 是 **字母异位词** 。
>
> 只要可以选出满足条件的下标，就一直执行这个操作。
>
> 在执行所有操作后，返回 `words` 。可以证明，按任意顺序为每步操作选择下标都会得到相同的结果。
>
> **字母异位词** 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。例如，`"dacb"` 是 `"abdc"` 的一个字母异位词。
>
> **示例 1：**
>
> ```
> 输入：words = ["abba","baba","bbaa","cd","cd"]
> 输出：["abba","cd"]
> 解释：
> 获取结果数组的方法之一是执行下述步骤：
> - 由于 words[2] = "bbaa" 和 words[1] = "baba" 是字母异位词，选择下标 2 并删除 words[2] 。
>   现在 words = ["abba","baba","cd","cd"] 。
> - 由于 words[1] = "baba" 和 words[0] = "abba" 是字母异位词，选择下标 1 并删除 words[1] 。
>   现在 words = ["abba","cd","cd"] 。
> - 由于 words[2] = "cd" 和 words[1] = "cd" 是字母异位词，选择下标 2 并删除 words[2] 。
>   现在 words = ["abba","cd"] 。
> 无法再执行任何操作，所以 ["abba","cd"] 是最终答案。
> ```
>
> **示例 2：**
>
> ```
> 输入：words = ["a","b","c","d","e"]
> 输出：["a","b","c","d","e"]
> 解释：
> words 中不存在互为字母异位词的两个相邻字符串，所以无需执行任何操作。
> ```
> 提示：
>
> ``1 <= words.length <= 100`
> `1 <= words[i].length <= 10`
> `words[i] `由小写英文字母组成

------------

###### 解题思路(AC)

 简单难度题，直接选择最好想的方法，赋值一个相同数组，让数组中的每个字符串都排序，然后从后往前逐一比较，相同就删除原数组项

```js
/**
 * @param {string[]} words
 * @return {string[]}
 */
 var removeAnagrams = function(words) {
    // 复制一份一样的数组，并对每一项进行处理
    let sortWords = words.map(e => {
        return [...e].sort().join('');
    })
    // 从后往前逐一比较，当 `sortWords[i] === sortWords[i - 1]` 删除sortWords[i]
    for (let i = sortWords.length - 1; i > 0; i--) {
        if (sortWords[i] === sortWords[i - 1]) {
            words.splice(i, 1);
        }
    }
    return words;
};
```

##### 2274.不含特殊楼层的最大连续楼层数(medium): [link](https://leetcode.cn/problems/maximum-consecutive-floors-without-special-floors/)



###### 题目

> - Alice 管理着一家公司，并租用大楼的部分楼层作为办公空间。Alice 决定将一些楼层作为 **特殊楼层** ，仅用于放松。
>
>   给你两个整数 `bottom` 和 `top` ，表示 Alice 租用了从 `bottom` 到 `top`（含 `bottom` 和 `top` 在内）的所有楼层。另给你一个整数数组 `special` ，其中 `special[i]` 表示  Alice 指定用于放松的特殊楼层。
>
>   返回不含特殊楼层的 **最大** 连续楼层数。
>
>    
>
>   **示例 1：**
>
>   ```
>   输入：bottom = 2, top = 9, special = [4,6]
>   输出：3
>   解释：下面列出的是不含特殊楼层的连续楼层范围：
>   - (2, 3) ，楼层数为 2 。
>   - (5, 5) ，楼层数为 1 。
>   - (7, 9) ，楼层数为 3 。
>   因此，返回最大连续楼层数 3 。
>   ```
>
>   **示例 2：**
>
>   ```
>   输入：bottom = 6, top = 8, special = [7,6,8]
>   输出：0
>   解释：每层楼都被规划为特殊楼层，所以返回 0 。
>   ```
>
>    
>
>   **提示**
>
>   - `1 <= special.length <= 105`
>   - `1 <= bottom <= special[i] <= top <= 109`
>   - `special` 中的所有值 **互不相同**

-----

###### 解题思路(AC)

最开始我的想法是前缀和，建数组存每层楼对应的值，然后找最大，运行的时候 timeout 才想起来 ，special 楼层中并不需要特殊处理，直接用相邻的特殊楼层相减即为一个连续楼层，于是有了以下

```javascript
/**
 * @param {number} bottom
 * @param {number} top
 * @param {number[]} special
 * @return {number}
 */
 var maxConsecutive = function(bottom, top, special) {
    // 先对数组排序
    special.sort((a,b) => a - b);
    // 插入 起始楼层 和 末楼层（后续有优化）
    special.unshift(bottom);
    special.push(top);
    let max = 0;
    for (let i = 1; i < special.length; i++) {
        let temp = special[i] - special[i  - 1] - 1;
        // 因为上面插入的时候处理的并不精细，起始和结束楼层也算正常楼层，所以并不能直接相减，当时反应最快的是 对起始楼层和结束楼层单独处理，有了下面这个。
        if (i === 1 || i === special.length - 1) { 
            temp++;
        }
        max = Math.max(max, temp);
    }
    return max;
}
```

**优化**

当时对起始楼层和结束楼层的插入处理并非很好， 起始楼层 和 结束楼层 是正常楼层，但是可以把 起始楼层 的下一层视为特殊楼层，以及 结束楼层 的上一层视为特殊楼层，这样在直接相减时，起始和结束楼层也包含在内，不需要特殊处理，代码如下：

```javascript
/**
 * @param {number} bottom
 * @param {number} top
 * @param {number[]} special
 * @return {number}
 */
var maxConsecutive = function(bottom, top, special) {
    special.sort((a,b) => a - b);
    special.unshift(bottom - 1);
    special.push(top + 1);
    let max = 0;
    for (let i = 1; i < special.length; i++) {
        let temp = special[i] - special[i  - 1] - 1;
        // 此时不需要判断 
        // if (i === 1 || i === special.length - 1) {
        //     temp++;
        // }
        max = Math.max(max, temp);
    }
    return max;
}
```

##### 2275.按位与结果大于零的最长组合 (medium): [link](https://leetcode.cn/problems/largest-combination-with-bitwise-and-greater-than-zero/)

###### 题目：

> 对数组 `nums` 执行 **按位与** 相当于对数组 `nums` 中的所有整数执行 **按位与** 。
>
> - 例如，对 `nums = [1, 5, 3]` 来说，按位与等于 `1 & 5 & 3 = 1` 。
> - 同样，对 `nums = [7]` 而言，按位与等于 `7` 。
>
> 给你一个正整数数组 `candidates` 。计算 `candidates` 中的数字每种组合下 **按位与** 的结果。 `candidates` 中的每个数字在每种组合中只能使用 **一次** 。
>
> 返回按位与结果大于 `0` 的 **最长** 组合的长度*。*
>
>  
>
> **示例 1：**
>
> ```
> 输入：candidates = [16,17,71,62,12,24,14]
> 输出：4
> 解释：组合 [16,17,62,24] 的按位与结果是 16 & 17 & 62 & 24 = 16 > 0 。
> 组合长度是 4 。
> 可以证明不存在按位与结果大于 0 且长度大于 4 的组合。
> 注意，符合长度最大的组合可能不止一种。
> 例如，组合 [62,12,24,14] 的按位与结果是 62 & 12 & 24 & 14 = 8 > 0 。
> ```
>
> **示例 2：**
>
> ```
> 输入：candidates = [8,8]
> 输出：2
> 解释：最长组合是 [8,8] ，按位与结果 8 & 8 = 8 > 0 。
> 组合长度是 2 ，所以返回 2 。
> ```
>
>  
>
> **提示：**
>
> - `1 <= candidates.length <= 105`
> - `1 <= candidates[i] <= 107`

-----

###### 解题思路(AC)

>  **思路真的很重要** 



这道题一开始没想通，看了别人的题解发现这道题只是需要有一位bit与不为0的最长字串，也就是每位 bit 逐一对比，每位 bit 上不为零的数量相加，得到每位bit上的最大结果

中等难度主要是难在思路上，思路歪了这道题基本就 timeout 了，思路对了就是道 easy 难度题

```javascript
/**
 * @param {number[]} candidates
 * @return {number}
 */
var largestCombination = function(candidates) {
	// 因为 10^7 转换为二进制只有24比特，所以只用开长度为24的数组    
    let maxBit = Array(24).fill(0);
    for (let i in maxBit) {
        for (let j of candidates) {
            if ((j >> i) % 2 === 1) {
                maxBit[i]++;
            }
        }
    }
    return Math.max(...maxBit);
};
```





##### 2276.统计区间中的整数数目(hard): [link](https://leetcode.cn/problems/count-integers-in-intervals/)

###### 题目

> 给你区间的 **空** 集，请你设计并实现满足要求的数据结构：
>
> - **新增：**添加一个区间到这个区间集合中。
> - **统计：**计算出现在 **至少一个** 区间中的整数个数。
>
> 实现 `CountIntervals` 类：
>
> - `CountIntervals()` 使用区间的空集初始化对象
> - `void add(int left, int right)` 添加区间 `[left, right]` 到区间集合之中。
> - `int count()` 返回出现在 **至少一个** 区间中的整数个数。
>
> **注意：**区间 `[left, right]` 表示满足 `left <= x <= right` 的所有整数 `x` 。
>
>  
>
> **示例 1：**
>
> ```
> 输入
> ["CountIntervals", "add", "add", "count", "add", "count"]
> [[], [2, 3], [7, 10], [], [5, 8], []]
> 输出
> [null, null, null, 6, null, 8]
> 
> 解释
> CountIntervals countIntervals = new CountIntervals(); // 用一个区间空集初始化对象
> countIntervals.add(2, 3);  // 将 [2, 3] 添加到区间集合中
> countIntervals.add(7, 10); // 将 [7, 10] 添加到区间集合中
> countIntervals.count();    // 返回 6
>                            // 整数 2 和 3 出现在区间 [2, 3] 中
>                            // 整数 7、8、9、10 出现在区间 [7, 10] 中
> countIntervals.add(5, 8);  // 将 [5, 8] 添加到区间集合中
> countIntervals.count();    // 返回 8
>                            // 整数 2 和 3 出现在区间 [2, 3] 中
>                            // 整数 5 和 6 出现在区间 [5, 8] 中
>                            // 整数 7 和 8 出现在区间 [5, 8] 和区间 [7, 10] 中
>                            // 整数 9 和 10 出现在区间 [7, 10] 中
> ```
>
>  
>
> **提示：**
>
> - `1 <= left <= right <= 109`
> - 最多调用  `add` 和 `count` 方法 **总计** `105` 次
> - 调用 `count` 方法至少一次

------

###### 解题思路(AC)

标准线段树题目

```javascript
function Node (start, end) {
    this.val = 0
    this.start = start
    this.end = end
    this.left = null
    this.right = null
}

function SegmentTree (start, end) {
    this.root = new Node(start, end)
}

SegmentTree.prototype.update = function (start, end) {
    this.updateNode(this.root, start, end)
}

SegmentTree.prototype.updateNode = function(node, start, end) {
    if (!node) {
        return
    }
    if (start > node.end || end < node.start) {
        return
    } else if (start <= node.start && end >= node.end) {
        node.val = node.end - node.start + 1
        return
    } else {
        this.pushdown(node);
        this.updateNode(node.left, start, end);
        this.updateNode(node.right, start, end);
        this.pushup(node);
    }
}

SegmentTree.prototype.pushdown = function (node) {
    if (!node) {
        return
    }
    const mid = Math.floor((node.start + node.end) / 2) 
    if (!node.left) {
        node.left = new Node(node.start, mid)
    }
    if (!node.right) {
        node.right = new Node(mid + 1, node.end)
    }
    if (node.val === (node.end - node.start + 1)) {
        node.left.val = mid - node.start + 1
        node.right.val = node.end - mid
    }
}

SegmentTree.prototype.pushup = function (node) {
    node.val = node.left.val + node.right.val
}

SegmentTree.prototype.query = function (start, end) {
    return this.queryNode(this.root, start, end)
}

SegmentTree.prototype.queryNode = function (node, start, end) {
    if (!node) {
        return 0;
    }
    if (start > node.end || end < node.start) {
        return 0;
    } else if (start <= node.start && end >= node.end) {
        return node.val;
    } else {
        this.pushdown(node);
        return this.queryNode(node.left, start, end) + this.queryNode(node.right, start, end);
    }
}

var CountIntervals = function() {
    this.tree = new SegmentTree(0, 1e9)
};

/** 
 * @param {number} left 
 * @param {number} right
 * @return {void}
 */
CountIntervals.prototype.add = function(left, right) {
    this.tree.update(left, right)
};

/**
 * @return {number}
 */
CountIntervals.prototype.count = function() {
    return this.tree.query(0, 1e9)
};

```

