# 牛客网题解

## 目录

[牛客网题解](#牛客网题解)
  - [目录](#目录)
    - [前缀和](#前缀和)
      - [NC125 和为K的连续子数组 （中等）link](#nc125和为k的连续子数组-中等link)
    - [链表](#链表)
      - [BM7 链表中环的入口结点：link](#bm7-链表中环的入口结点link)

### 前缀和

#### NC125 和为K的连续子数组 （中等）[link](https://www.nowcoder.com/practice/704c8388a82e42e58b7f5751ec943a11)

题目问的是连续最长子数组，如果题目给的是有序数组的话，可以很轻松想到双指针，大了 right 左移，小了 left 右移。但是题目给的是无序数组，如果再用双指针就无法判断左右缩小的条件了。

于是想到了前缀和方法，因为前缀和就是从头开始的字串的和，如果我们需要中间的一个字串。只需要相减即可。

以题目中示例1为例子

![preSum](img/preSum.png)

所以不难看出，问题从  `arr[i] + arr[i+1] + arr[i+2] + ... arr[i+j] === k ` 转变成

`preSum[j] - preSum[i] === k` 的问题上，首先降低了复杂度，那首先来构造一个前缀和数组。

**Tips：** 前缀和多构造一个，就可以不用判断是否为首位了，否则 遍历 `preSum[i] === k - preSum[3]` 时，还得多判断一下 `k === preSum[3]`  0-3这一个字串是否正好满足条件。

```javascript
let preSum = [0];
for (let i in arr) {
    preSum[i + 1] = arr[i] + preSum[i];
}
```

之后一个简单的方法就是用双重遍历去判断最长的字串时哪一串

```js
let maxLen = 0;
for (let i in perSum) {
    for (let j in perSum) {
        if (perSum[j] - perSum[i] === k) {
            maxLen = Math.max(maxLen, perSum[j] - perSum[i]);
        }
    }
}
```

我们会发现，无论怎么优化，只要用到双循环，时间复杂度都是O(n^2^)，不满足题目要求的时间复杂度O(n)，但是我们发现，内层循环仅仅是找对应匹配的子串，也就是寻找 `preSum[j] === k + preSum[i] ` 或者 `preSum[i] === k - perSum[j]` ，那我们是否可以把每个的 perSum[i] 都存放在哈希表中（在 js 中只有 Map），这样我们可以很快的找到 满足条件的 preSum[i] 不需要进行双重循环

```javascript
// 下面代码无法AC
let preSum = [0];
let map = new Map([[0, 0]]);
for (let i = 0；i < arr.length; i++) {
    preSum[i + 1] = arr[i] + preSum[i]; // **** 这里在构造 preSum ，是否可以看作遍历 preSum
    map.set(preSum[i + 1], i + 1);
}
let ans = 0;
let maxLen = 0;
for (let i = 0；i < preSum.length; i++) { // **** 这里在遍历 preSum
    let diff = k - preSum[j];
    if (map.has(diff)) {
        ans = Math.max(i - map.get(diff), ans);
    }
}
return ans;
```

此时不仅需要处理 冲突，还会让问题进一步复杂化：若 preSum 数组中有多个 相同值的前缀和呢，例如上图中 `preSum[1]` 和 `preSum[4]` ，这样做就得不偿失。

在上面代码中，我们的思想基本都是“左右看” ，也就是处在 i 的位置，往左和右双向寻找可以匹配的字串

**思考：**是否真的需要左右看，是否可以只看一边，如何只看一边？

在这样一个问题下，可以发现，我们只看一边，也能找到最长的字串，因为站在 `i`处，找到 匹配的`preSum[j]`（i > j）和站在`j` 处，找到匹配的`preSum[i]` 是同一种答案，所以可以只看一边。

如何只看一边？：

我们在构造preSum 的时候，是不是相当于遍历 preSum，同时 map 中是不是只有已经构造了的字串和，也就是向左看。

至此，思路已经比较清晰了，接下来就是如何改进代码

```javascript
/**
 * max length of the subarray sum = k
 * @param arr int整型一维数组 the array
 * @param k int整型 target
 * @return int整型
 */
function maxlenEqualK( arr ,  k ) { 
	let preSum = [0];
    let map = new Map([[0,0]]);
    let ans = 0;
    for (let i = 0; i < arr.length; i++) {
        preSum[i + 1] = arr[i] + preSum[i];
        // **** 重点注意：此时应该是 preSum[i + 1] - k 还是 k + preSum[i + 1] ?
        // 我们此时是向前看，即 i > j 时，  preSum[i] - preSum[j] === k 
        // 向前看的我们处在 i 上，即 preSum[i], 那我们在散列表(map)中需要寻找的是 preSum[j],
        // 所以这里应该是 preSum[i] - k; 
        let diff = preSum[i + 1] - k;
        if (map.has(diff)) {
            ans = Math.max((i + 1 - map.get(diff)), ans);
        }
        if (map.has(preSum[i + 1]) === false) {
            map.set(preSum[i + 1], i + 1);
        }
    }
    return ans;
}
module.exports = {
    maxlenEqualK : maxlenEqualK
};
```

![preSum](D:\Markdown记录\img\preSum1.png)

至此，代码已经可以AC了，但是在上段代码中，我们可以发现，其实我们每轮循环都只用用到了preSum[i+1]

原本的代码`preSum[i + 1] = arr[i] + preSum[i];` 前缀和就是一种累加，既然我们没用到前缀和数组的其他项，那我们是否可以优化成 `acc = arr[i] + acc`

```javascript
/**
 * max length of the subarray sum = k
 * @param arr int整型一维数组 the array
 * @param k int整型 target
 * @return int整型
 */
function maxlenEqualK( arr ,  k ) { 
	let preSum = 0; // ** 去掉数组，只用单个数值即可
    let map = new Map([[0,0]]);
    let ans = 0;
    for (let i = 0; i < arr.length; i++) {
        preSum = arr[i] + preSum;
        let diff = preSum - k;
        if (map.has(diff)) {
            ans = Math.max((i + 1 - map.get(diff)), ans);
        }
        if (map.has(preSum) === false) {
            map.set(preSum, i + 1);
        }
    }
    return ans;
}
module.exports = {
    maxlenEqualK : maxlenEqualK
};
```

至此，我的解题思路就到此为止了，如果大家还有什么进一步优化的想法，欢迎找我，咱们一起讨论。

### 链表

--------

#### BM7 链表中环的入口结点：[link](https://www.nowcoder.com/practice/253d2c59ec3e4bc68da16833f79a38e4?tpId=295&tqId=23449&ru=/exam/oj&qru=/ta/format-top101/question-ranking&sourceUrl=%2Fexam%2Foj%3Fpage%3D1%26tab%3D%25E7%25AE%2597%25E6%25B3%2595%25E7%25AF%2587%26topicId%3D295)



本题是思路题，对代码的要求不高。

**问题1：**如何在低时间空间复杂度下判断是否有环？

我们可以选择快慢指针，即追击问题，在操场上，跑的快的人始终会追上或超过慢的人一圈。即设置 `slow` 每走一步，`fast` 走两步，如果 `fast` 最终遇到空指针，说明链表中没有环，如果`fast === slow` 那说明有环，俩指针碰到一起了。

**问题2：** 找到了环，怎么找到环的入口？

我们这个时候画个图，全流程就出来了。

![BM7_1](D:\Markdown记录\img\BM7_1.svg)
