###LeetCode之sort篇
##### 1.Intersection of Two Arrays（数组交集）
题目描述：给定两个数组，写一个函数计算它们的交集

Example:
Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2].

Note:

- Each element in the result must be unique.（结果的元素必须是唯一的，使得本题思考起来比较简单，直接遍历其中一个数组，判断其中元素是否在第二个数组中，即可）
- The result can be in any order.

编程思路：

- 对其中一个数组进行过滤，可以得到其中相同元素组成的数组。
- 再利用Set将数组元素变得独一无二。

代码：
```
var intersection = function(nums1, nums2) {
    var intersection = nums1.filter(function (v) {
        return nums2.includes(v);
    });
    var uniqueIntersection = new Set(intersection);
    var res = [];
    for (let i of uniqueIntersection) {
        res.push(i);
    }
    return res;
    
};
```
从中的启发：

js判断元素是否在数组中，不要用 v in arr(这是python的写法)；ES5是数组的indexof方法，ES6有更好的方法就是数组实例的includes方法。
##### 2.Intersection of Two Arrays II（数组交集II）
题目描述：给定两个数组，写一个函数计算它们的交集

Example:
Given nums1 = [1, 2, 2, 1], nums2 = [2, 2], return [2, 2].

Note:
- Each element in the result should appear as many times as it shows in both arrays.
- The result can be in any order.

Follow up:
- What if the given array is already sorted? How would you optimize your algorithm?（对于两个排好序的数组，如何高效的找到两者的交集？）
- What if nums1's size is small compared to nums2's size? Which algorithm is better?（如果第一个数组比较小？哪个算法更好？）
- What if elements of nums2 are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?（如果数组二的元素存储在硬盘里，而且内存有限不能一次性读取，怎么办？）

编程思想（指针比较法）：

- 排序数组
- 初始化两数组的指针，均从0开始
- 将小数组的指针做为外层循环，在大数组中以大数组指针位置开始比较
- 如果找到相等的，记录结果，同时将大小数组的指针向后移动
- 如果在大数组中找到末尾都没有找到，那么小数组的指针向后移动
- 当小数组的指针移动到最后一个元素后结束算法

代码（没有将所有用例跑通）：
```
var intersect = function (nums1, nums2) {
    nums1.sort(function (a, b) {
        return a - b;
    });
    nums2.sort(function (a, b) {
        return a - b;
    });
    var findIntersection = function (arr1, arr2) {
        var indexNums1 = 0;
        var indexNums2 = 0;
        var sizeNums1 = arr1.length;
        var sizeNums2 = arr2.length;
        var intersection = [];
        while (indexNums1 < sizeNums1) {
            for (let i = indexNums2; i < sizeNums2; i++) {
                if (arr1[indexNums1] === arr2[i]) {
                    intersection.push(arr1[indexNums1]);
                    indexNums1++;
                    indexNums2 = i + 1;
                    break;
                } 
                else if (arr1[indexNums1] < arr2[i]) {
                    indexNums1++;
                    break;
                }
                else if (i === sizeNums2 - 1) {
                    indexNums1++;
                } 
                
            }
        }
        return intersection;
    };
    var res;
    if (nums1.length <= nums2.length) {
        res = findIntersection(nums1, nums2);
    }
    else {
        res = findIntersection(nums2, nums1);
    }
    return res;
};
```
以下解法的思路：
    
1. 分别将两个数组内的元素进行统计，将结果存入map中
2. 将两个map相同的键值的value，进行比较
3. 取小的值作为键值被推入最终结果数组的次数

python 解法
```
from collections import Counter
    def intersect(self, nums1, nums2):
        c1, c2 = Counter(nums1), Counter(nums2)
        return sum([[num] * min(c1[num], c2[num]) for num in c1 & c2], [])
```
js 解法
```
var intersect = function(nums1, nums2) {
    var valCount = function (arr) {
        let count = new Map();
        for (let v of arr) {
            if (count.has(v)) {
                let oldValue = count.get(v);
                count.set(v, oldValue + 1);
            }
            else {
                count.set(v, 1);
            }
        }
        return count;
    };
    var nums1Count = valCount(nums1);
    var nums2Count = valCount(nums2);
    var res = [];
    for (let i of nums1Count.keys()) {
        if (nums2Count.has(i)) {
            if (nums1Count.get(i) <= nums2Count.get(i)) {
                for (let j=1; j<=nums1Count.get(i); j++) {
                    res.push(i);
                }
            }
            else {
                for (let j=1; j<=nums2Count.get(i); j++) {
                    res.push(i);
                }
            }
        }
    }
    return res;
};
```
##### 3.Valid Anagram（真实的可由颠倒字母顺序而构成的字）
题目描述：给出两个字符串s和t，写一个函数证明t可由s调换自身字母顺序得到。

For example,

s = "anagram", t = "nagaram", return true.

s = "rat", t = "car", return false.

Note:
You may assume the string contains only lowercase alphabets.

Follow up:
What if the inputs contain unicode characters? How would you adapt your solution to such case?

编程思路：
1. 跟之前的数组交集思路相似，将两个字符串统计字母次数，建立Hash Table（这里使用数据结构map）；
1. 问题转化为如何求这两个map是否相等的问题。
```
var isAnagram = function(s, t) {
    var valCount = function (str) {
        let count = new Map();
        for (let v of str) {
            if (count.has(v)) {
                let oldValue = count.get(v);
                count.set(v, oldValue + 1);
            }
            else {
                count.set(v, 1);
            }
        }
        return count;
    };
    var mapKey = function (map) {
        var arr = [];
        for (let i of map.keys()) {
           arr.push(i); 
        }
        return arr;
    }
    var newS = s.toLowerCase();
    var newT = t.toLowerCase();
    if (newS === newT) {return true;} //优化代码，当两个字符串相同时，直接返回结果
    var sCount = valCount(newS);
    var tCount = valCount(newT);
    var res = true;
    if (mapKey(sCount).length === mapKey(tCount).length) {
        for (let i of sCount.keys()) {
            if (sCount.get(i) !== tCount.get(i)) {
                res = false;
                break;
            }
        }
    } else {
        res = false;
    }
    return res;
};
```