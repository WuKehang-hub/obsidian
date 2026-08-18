---
tags:
  - 刷题
  - 算法
date: 2026-06-15
---

==此篇为Hot100刷题笔记(Python版)==

# 底层认知

## 1. 算法评判基础：复杂度分析

* **时间复杂度 (Time Complexity)**：衡量算法运行速度随数据规模 $n$ 增大的**增长趋势**。
* **空间复杂度 (Space Complexity)**：衡量算法运行所需额外内存随数据规模 $n$ 增大的**增长趋势**。
* **大 O 符号 (Big O Notation)**：忽略常数项和低阶项，只保留最高阶项。
    * *示例*：精确操作次数为 $\frac{n(n-1)}{2} = \frac{1}{2}n^2 - \frac{1}{2}n$，化简后时间复杂度为 $O(n^2)$。
* **常见复杂度等级 (从快到慢)**：$O(1)$ > $O(\log n)$ > $O(n)$ > $O(n \log n)$ > $O(n^2)$

>   **核心策略：空间换时间**
>   现代计算机内存充裕，面对海量数据时，通常优先优化时间复杂度，通过消耗额外空间换取极速查询。

## 2. LeetCode 平台机制 (OJ 原理)

LeetCode 后台是一个测试引擎。它会自动**导入**你写的 `Solution` 类，**实例化**对象，并将成百上千组测试用例循环传入你的核心函数，最后比对你的 `return` 结果。因此，刷题时无需编写文件读取或 `print` 等外围代码，100% 专注算法逻辑本身即可。

# 哈希表 (Hash Table)

## 1. 概念

**本质**：本质就是python字典（只不过是先有哈希表再有字典）。

**查询语法**：`if X in dict:`。**注意：`in` 关键字只搜索字典的“键 (Key)”，绝不会搜索“值 (Value)”。**

**可哈希性 (谁能当字典的键？)**

字典的底层逻辑要求键必须拥有**固定不变的指纹**。

| 数据类型 | 能否当字典的键 | 为什么？ |
| :--- | :--- | :--- |
| **字符串 (String) / 数字** | **能** | 不可变，内容定死，指纹永远不变。 |
| **元组 (Tuple)** | **能** | 不可变，不能增删改，指纹永远不变。 |
| **列表 (List) / 字典** | **不能** | 可变，一旦内容被 `append` 或修改，哈希指纹改变，会导致找不到数据报错。 |

## 2. 常用方法

* **`enumerate(iterable, start=0)`（同时获取索引和元素）**

    可用于字符串、列表、元组等可迭代对象：

    ```python
    for i, num in enumerate(nums):
        # i 是索引，num 是元素

    for i, ch in enumerate("abc"):
        # ch 是当前字符
    ```

    `start` 可以指定编号起点，例如 `enumerate(names, start=1)`。如果不需要索引，直接写 `for item in iterable` 即可。

* **`dict[key]` vs `dict.get(key, 默认值)` (取值方式)**

    `dict[key]` 适合在**百分百确定键存在**时使用；如果键不存在，会直接抛出 `KeyError`。`dict.get(key, 默认值)` 更像安全取值，键不存在时不会报错，而是返回你给的默认值。

    ```python
    mp = {"apple": 2}

    print(mp["apple"])        # 2
    print(mp.get("banana", 0)) # 0，不报错
    ```

    刷题时的经验：如果数据来源未知、键不一定存在，用 `get()` 更稳；如果已经用 `if key in mp:` 判断过，后面再用 `mp[key]` 就很合适。

* **`get()` 只负责查，不负责存**

    很容易误以为 `mp.get(key, 0)` 会把默认值写进字典，其实不会。它只是“临时拿出一个默认值给你用”，字典本身没有变化。

    ```python
    mp = {}

    mp.get("apple", 0)
    print(mp) # {}

    mp["apple"] = mp.get("apple", 0) + 1
    print(mp) # {"apple": 1}
    ```

    如果只是想“键不存在就先放进去”，可以用 `setdefault()`：

    ```python
    mp = {}
    mp.setdefault("apple", 0)
    print(mp) # {"apple": 0}
    ```

* **`collections.defaultdict(默认工厂)` (自带兜底的字典)**

    `defaultdict` 本质上还是字典，是 `dict` 的子类；只是在访问不存在的键时，会自动调用你传进去的“默认工厂”，创建一个默认值并存进字典。

    ```python
    import collections

    mp = collections.defaultdict(list)
    mp["a"]              # 自动执行 mp["a"] = list()，也就是 []
    print(isinstance(mp, dict)) # True
    ```

    它不只能传 `list`，常见默认工厂还有：

    ```python
    groups = collections.defaultdict(list) # 默认值：[]，适合分组追加
    count = collections.defaultdict(int)   # 默认值：0，适合计数
    seen = collections.defaultdict(set)    # 默认值：set()，适合去重分组
    info = collections.defaultdict(dict)   # 默认值：{}，适合嵌套记录
    ```

    重点是：传进去的东西要能像 `list()`、`int()`、`set()`、`dict()` 这样**不带参数直接调用**，因为它会在缺 key 时自动执行一次。

    分组追加时，`defaultdict(list)` 比 `get()` 更省心。比如把单词按首字母分组：

    ```python
    # 错误示范：append 到了 get 临时造出来的列表里，没有写回字典
    mp = {}
    mp.get("a", []).append("apple")
    print(mp) # {}

    # 正确写法：键不存在时自动创建空列表，并真正挂到字典里
    import collections
    mp = collections.defaultdict(list)
    mp["a"].append("apple")
    print(mp) # {"a": ["apple"]}
    ```

    所以只要遇到“按某个 key 分组，然后不断 append”的题型，优先想到 `defaultdict(list)`；遇到词频统计，可以想到 `defaultdict(int)`。

* **字符串重组：`sorted()` + `join()` / `tuple()`**

    `sorted(str)` 会返回一个**列表**，而列表是可变类型，不能直接做字典的键。必须化零为整：

    ```python
    sorted("tea") # ["a", "e", "t"]

    key1 = "".join(sorted("tea"))   # "aet"，字符串可以做键
    key2 = tuple(sorted("tea"))     # ("a", "e", "t")，元组也可以做键
    ```

    `join()` 的语法有点反直觉：**胶水在前，列表在后**。

    ```python
    "".join(["a", "e", "t"])            # "aet"
    "-".join(["2026", "07", "07"])      # "2026-07-07"
    ```

    铁律：被拼接的列表里必须全是字符串。如果有数字，要先转成字符串。

    ```python
    "-".join(map(str, [2026, 7, 7]))    # "2026-7-7"
    ```

* **字典键的死规矩：必须可哈希**

    字典底层是哈希表，键必须是不可变对象。字符串、数字、元组可以当键；列表、字典不能当键。

    ```python
    mp = {}
    mp[tuple(sorted("tea"))] = "tea" # 正确
    # mp[sorted("tea")] = "tea"      # 错误：list 不能当键
    ```

* **`dict.values()` (提取字典里的所有值)**

    返回字典中所有值组成的视图对象，常配合 `list()` 转成列表。

    ```python
    groups = list(mp.values())
    ```

* **`set(iterable)`（创建集合）**

    `set()` 是 Python 用来**创建集合（Set）** 的内置函数。集合是一种只保存唯一元素的数据结构：同一个值无论加入多少次，在集合中都只会保留一份。

    ```python
    nums = [1, 2, 2, 3, 3, 3]
    num_set = set(nums)

    print(num_set)  # {1, 2, 3}
    ```

    `set(iterable)` 可以接收列表、元组、字符串等可迭代对象，并把其中的元素依次加入新集合。因此它经常用于**去重**：

    ```python
    set([1, 1, 2, 3])   # {1, 2, 3}
    set((1, 1, 2, 3))   # {1, 2, 3}
    set("banana")       # {'b', 'a', 'n'}
    ```

    集合有以下特点：

    1. **元素唯一**：自动忽略重复值。
    2. **无序**：不通过下标保存元素，不能写 `num_set[0]`；打印顺序也不应被当作固定顺序。
    3. **查询很快**：集合底层使用哈希表，`value in num_set` 的平均时间复杂度为 $O(1)$。
    4. **集合本身可变**：可以添加或删除元素。
    5. **集合中的元素必须可哈希**：数字、字符串、元组可以加入集合；列表、字典和普通集合不能直接作为集合元素。

    ```python
    num_set = {1, 2, 3}

    num_set.add(4)       # 添加一个元素
    num_set.remove(2)    # 删除元素；元素不存在时抛出 KeyError
    num_set.discard(10)  # 删除元素；元素不存在也不会报错

    print(3 in num_set)      # True
    print(10 not in num_set) # True
    ```

    > [!warning] 空集合的易错写法
    > `{}` 创建的是**空字典**，不是空集合。创建空集合必须写 `set()`。

    ```python
    empty_set = set()  # 空集合
    empty_dict = {}    # 空字典
    ```

    集合和字典都基于哈希表，但用途不同：字典保存的是 `键: 值` 映射，集合只关心某个元素**是否存在**。刷题时，如果不需要记录索引、次数或其他附加信息，只需要快速查存在性和去重，通常优先使用集合。

* **`max(a, b)` (取最大值)**

    返回多个值中的最大值，常用于维护历史最优答案。

    ```python
    longest_streak = max(longest_streak, current_streak)
    ```

## 3. 题目

### 题目 1：两数之和（LeetCode 1，简单）

==原题==

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** `target` 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

==答案==

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        hashtable = {} # 格式：{数值: 索引}
        for i, num in enumerate(nums):
            # 1. 计算需要的同伙数值
            complement = target - num
            # 2. 查哈希表：同伙是否已经登记过？
            if complement in hashtable:
                return [hashtable[complement], i]
            # 3. 没找到：把自己登记到小本本上，供后续数字匹配
            hashtable[num] = i
        return []
```

==解析==

* **核心逻辑**：边遍历，边查找，边登记。
* **为什么用哈希表**：我们需要频繁地“回头寻找”某个特定的数字是否出现过。哈希表可以将这种查找的时间复杂度从 $O(n)$ 降到 $O(1)$。
* **巧妙之处**：“先查字典，后存自己”而不是“先存自己，再查字典”，这种执行顺序避开了重复数字相互覆盖的问题（例如 `[3, 3]` 找 `6`），也保证了绝不会在第一个数字就发生错误的匹配。

---

### 题目 2：字母异位词分组（LeetCode 49，中等）

==原题==

给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。

字母异位词是由重新排列源单词的所有字母得到的一个新单词。

* **示例:** 输入: `strs = ["eat", "tea", "tan", "ate", "nat", "bat"]` -> 输出: `[["bat"],["nat","tan"],["ate","eat","tea"]]`

==错误答案==

```python
import collections

class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        a=collections.defaultdict(list)
        for i in strs:
            j= sorted(i)
            if j in a:
                a[j].append(i)
            else:
                a.join(sorted(i))
    return list(a.values)

```

* **错误 1：`sorted(i)` 返回的是列表，不能当字典的键**

    `j = sorted(i)` 得到的是类似 `["a", "e", "t"]` 的列表。列表是可变对象，不能被哈希，所以这一句会出问题：

    ```python
    if j in a:
    ```

    因为 `a` 是字典，`j in a` 本质是在检查 `j` 这个键是否存在；但列表不能做键。应该先把它转成元组或字符串：

    ```python
    j = tuple(sorted(i))
    # 或者
    j = "".join(sorted(i))
    ```

* **错误 2：`a.join(sorted(i))` 不是合法操作**

    `join()` 是字符串的方法，不是字典的方法。也就是说，应该是“胶水字符串”在前：

    ```python
    "".join(sorted(i))
    ```

    但在这道题里，`join()` 的作用只是生成 key，不是把结果存进字典。真正要把单词放进分组里，应该写：

    ```python
    a[j].append(i)
    ```

* **错误 3：用了 `defaultdict(list)` 后，不需要再写 `if j in a`**

    `defaultdict(list)` 的意义就是：当 `a[j]` 不存在时，自动创建一个空列表。所以可以直接追加：

    ```python
    a[j].append(i)
    ```

    不需要手动判断 `if j in a`。

* **错误 4：`return list(a.values)` 少了括号，而且缩进不对**

    `values` 是方法，必须调用：

    ```python
    return list(a.values())
    ```

    同时 `return` 必须缩进在 `groupAnagrams` 函数内部，否则就不是函数的返回值了。

==正确答案==

```python
import collections

class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        # 使用自带兜底功能的字典，键不存在时自动创建空列表
        mp = collections.defaultdict(list)

        for st in strs:
            # 寻找统一特征：排序并转为不可变的元组作为键
            key = tuple(sorted(st))
            # 将原始单词挂载到对应键的列表中
            mp[key].append(st)

        # 提取所有的分组列表并返回
        return list(mp.values())
```

==解析==

* **核心逻辑**：为所有打乱的单词寻找一个**统一的接头暗号**作为字典的键。如果两个单词是字母异位词，它们按字母表排序后的结果一定完全相同。
* **底层踩坑**：`sorted()` 函数返回的是**列表**（可变类型），而 Python 字典要求键必须是不可变的（拥有固定指纹）。因此必须使用 `tuple()` 或 `"".join()` 将其转换为不可变的元组或字符串，才能安全地作为键。
* **API 技巧**：`collections.defaultdict(list)` 省去了我们手动写 `if key not in mp:` 的判断逻辑，代码更加优雅。

---

### 题目 3：最长连续序列（LeetCode 128，中等）

==原题==

给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 **$O(n)$** 的算法解决此问题。

* **示例:** 输入: `nums = [100,4,200,1,3,2]` -> 输出: `4`
* **解释:** 最长数字连续序列是 `[1, 2, 3, 4]`。它的长度为 4。

==答案==

```python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        # 将列表转换为集合 (Set)，本质上是只有键没有值的哈希表，实现 O(1) 查找并去重
        num_set = set(nums)
        longest_streak = 0

        for num in num_set:
            # 核心剪枝逻辑：只有当 num - 1 不在集合中时，说明 num 是一个连续序列的“起点”
            if num - 1 not in num_set:
                current_num = num
                current_streak = 1

                # 顺藤摸瓜，不断去哈希表中寻找下一个连续数字
                while current_num + 1 in num_set:
                    current_num += 1
                    current_streak += 1

                # 更新历史最长记录
                longest_streak = max(longest_streak, current_streak)

        return longest_streak
```

==解析==

* **突破口**：题目要求时间复杂度 $O(n)$，直接封死了先用 `sort()` 排序（$O(n \log n)$）的路子。要想在乱序中快速寻找连续数字，必须借助哈希表的 $O(1)$ 查找能力。
* **数据结构选择**：这道题我们只需要判断数字“存不存在”，不需要记录它的索引，所以我们使用 **集合 (`set`)**。它是极简版的字典。
* **核心算法思维（剪枝）**：如果我们对集合里的每一个数都向上找一遍（比如遇到 `2` 找一遍，遇到 `3` 又找一遍），时间复杂度会退化。**绝妙的思路是找“序列的起点”**。
    * 怎么判断一个数是起点？只要 `它减去 1` 的那个数**不在**集合里，它就是龙头！
    * 我们只对“龙头”进行向上的循环计数。这样每个数字实际上最多只被遍历两次，完美达成 $O(n)$ 的时间复杂度要求。
* **为什么 `for` 里面套 `while` 不是 $O(n^2)$？**

    不是所有“循环套循环”都一定是 $O(n^2)$，关键要看内层 `while` **总共执行了多少次**，而不是只看代码长得像嵌套。

    以 `nums = [100, 4, 200, 1, 3, 2]` 为例，集合是 `{1, 2, 3, 4, 100, 200}`：
    * `num = 1` 时，`0` 不在集合里，所以 `1` 是起点，进入 `while`，一路检查 `2 -> 3 -> 4`。
    * `num = 2` 时，`1` 在集合里，说明 `2` 不是起点，直接跳过，不再从 `2` 开始重复数 `2 -> 3 -> 4`。
    * `num = 3` 时，`2` 在集合里，也跳过。
    * `num = 4` 时，`3` 在集合里，也跳过。

    所以最长连续段 `[1, 2, 3, 4]` 只会被 `1` 这个起点完整扫描一次，不会被 `1、2、3、4` 分别重复扫描。外层 `for` 负责判断每个数字是不是起点，总共 $n$ 次；内层 `while` 只沿着真正的连续链往后走，所有链加起来最多也就是 $n$ 个数字。因此总复杂度是：

    $$

    O(n) + O(n) = O(n)

    $$

    真正会退化成 $O(n^2)$ 的写法，是不判断起点，遇到每个数字都向后扫一遍。

# 双指针

## 1. 概念

**本质**：双指针并不是一种具体的数据结构，而是一种**用两个下标协同遍历数据**的算法技巧。两个指针通常分别记录不同的边界或状态，通过有规律地移动它们，减少无效枚举。

如果用两层循环枚举所有元素对，时间复杂度通常是 $O(n^2)$；如果能根据题目的单调性判断“移动哪一边不可能错过答案”，双指针往往可以把复杂度降到 $O(n)$。

常见的双指针可以分成两类：

| 类型              | 初始位置        | 移动方式                 | 常见用途             |
| :-------------- | :---------- | :------------------- | :--------------- |
| **同向双指针（快慢指针）** | 都从左侧出发      | 两个指针向同一方向移动，但职责或速度不同 | 原地删除、数组分区、链表问题   |
| **相向双指针（左右指针）** | 一个在最左，一个在最右 | 两个指针向中间靠拢            | 有序数组求和、盛水面积、回文判断 |

> **核心前提：为什么可以移动指针？**
> 双指针不是随便少算一些情况，而是利用题目中的**单调性、顺序或已知边界**，一次排除一批不可能成为答案的情况。写题时最重要的不是记住代码，而是能解释：当前为什么移动这个指针，以及被跳过的情况为什么不可能更优。

## 2. 常用方法

* **快慢指针：快指针负责探索，慢指针负责维护结果区间**

    ```python
    slow = 0
    for fast in range(len(nums)):
        if nums[fast] 满足保留条件:
            nums[slow] = nums[fast]
            slow += 1
    ```

    可以把数组想成两块：`[0, slow)` 是已经处理好的区域，`fast` 负责检查还没有处理的元素。

* **左右指针：根据当前状态舍弃一侧**

    ```python
    left, right = 0, len(nums) - 1
    while left < right:
        if 应该移动左边:
            left += 1
        else:
            right -= 1
    ```

    左右指针能成立的关键，是每次移动都必须有依据。例如在有序数组中，两数之和偏小就移动左指针，偏大就移动右指针。

* **原地修改不等于不能使用额外变量**

    “原地”通常表示不能再创建一个与输入规模相同的新数组，即额外空间应为 $O(1)$。使用 `left`、`right`、`count` 等少量变量仍然属于原地操作。

* **Python 交换语法**

    ```python
    nums[left], nums[right] = nums[right], nums[left]
    ```

    Python 可以直接交换两个位置的值，不需要额外写一个临时变量。

* **排序的复杂度不能忽略**

    ```python
    nums.sort()
    ```

    `list.sort()` 会原地修改列表，时间复杂度是 $O(n \log n)$。即使排序后的双指针只需要 $O(n)$，整个算法仍然是 $O(n \log n)$。

## 3. 题目

### 题目 1：移动零（LeetCode 283，简单）

==原题==

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

请注意，必须在不复制数组的情况下原地对数组进行操作。

* **示例：** 输入：`nums = [0,1,0,3,12]` -> 修改后：`[1,3,12,0,0]`

==错误答案==

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
	    slow=0
	    for fast in len(nums)：
		    if nums[fast]!=0:
			    nums[slow],nums[fast]=nums[fast],nums[slow]
			    slow=slow+1
```

写成 `for right in len(nums):`，Python 会直接报错：

`TypeError: 'int' object is not iterable`（整数对象不可迭代）。

==答案==

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        # slow 指向下一个应该放置非零元素的位置
        slow = 0

        # fast 负责寻找非零元素
        for fast in range(len(nums)):
            if nums[fast] != 0:
                nums[slow], nums[fast] = nums[fast], nums[slow]
                slow += 1
```

==解析==

* **指针分工**：`fast` 从左到右检查所有元素；`slow` 指向“下一个非零元素应该放到的位置”。
* **核心逻辑**：当 `fast` 找到非零元素时，就把它与 `slow` 位置的元素交换，然后让 `slow` 向右移动。处理过程中，`[0, slow)` 始终保存着已经整理好的非零元素。
* **为什么相对顺序不会改变**：`fast` 是从左到右依次发现非零元素的，它们也会按照被发现的先后顺序依次放到 `slow` 所指的位置，因此原有顺序不变。
* **自己和自己交换没问题**：如果数组开头本来就是非零元素，可能出现 `slow == fast`。此时交换不会改变数组，也不影响正确性。若特别在意这次操作，也可以先判断 `slow != fast`，但没有必要。
* **复杂度**：每个元素只检查一次，时间复杂度为 $O(n)$；只使用两个指针，空间复杂度为 $O(1)$。

---

### 题目 2：盛最多水的容器（LeetCode 11，中等）

==原题==

给定一个长度为 `n` 的整数数组 `height`。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])`。

找出其中的两条线，使它们与横轴共同构成的容器可以容纳最多的水，返回容器可以储存的最大水量。

* **示例：** 输入：`height = [1,8,6,2,5,4,8,3,7]` -> 输出：`49`

==答案==

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        left, right = 0, len(height) - 1
        max_area = 0

        while left < right:
            width = right - left
            current_height = min(height[left], height[right])
            max_area = max(max_area, width * current_height)

            # 面积受较短的木板限制，只能尝试换掉短板
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1

        return max_area
```

==解析==

容器面积为：

$$

S = (right - left) \times \min(height[left], height[right])

$$

* **为什么从两端开始**：最左和最右两条线能提供最大的初始宽度，之后指针不断向中间收缩。
* **为什么总是移动短板**：向内移动后，宽度一定变小。如果保留短板、移动长板，容器高度仍然不会超过原来的短板，高度没有变大的可能，面积就一定不会更大。因此只有换掉短板，才有机会用更高的线弥补宽度的损失。
* **两边一样高时移动哪边**：任意移动一边都可以。代码选择移动右指针，因为保留其中一块同样高度的板，并不会漏掉更优解。
* **复杂度**：左右指针最多各移动 `n - 1` 次，时间复杂度为 $O(n)$，空间复杂度为 $O(1)$。

---

### 题目 3：三数之和（LeetCode 15，中等）

==原题==

给你一个整数数组 `nums`，判断是否存在三个元素 `nums[i]`、`nums[j]`、`nums[k]`，满足下标互不相同且三数之和为 `0`。请返回所有和为 `0` 且不重复的三元组。

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
```

* **示例：** 输入：`nums = [-1,0,1,2,-1,-4]` -> 输出：`[[-1,-1,2],[-1,0,1]]`

==答案==

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        answer = []
        n = len(nums)

        # i 是三元组中的第一个数，后两个数用左右指针寻找
        for i in range(n - 2):
            # 排序后第一个数已经大于 0，后面不可能再凑出 0
            if nums[i] > 0:
                break

            # 跳过重复的第一个数
            if i > 0 and nums[i] == nums[i - 1]:
                continue

            left, right = i + 1, n - 1

            while left < right:
                total = nums[i] + nums[left] + nums[right]

                if total < 0:
                    # 和太小，需要更大的数
                    left += 1
                elif total > 0:
                    # 和太大，需要更小的数
                    right -= 1
                else:
                    answer.append([nums[i], nums[left], nums[right]])
                    left += 1
                    right -= 1

                    # 跳过重复的第二、第三个数
                    while left < right and nums[left] == nums[left - 1]:
                        left += 1
                    while left < right and nums[right] == nums[right + 1]:
                        right -= 1

        return answer
```

==解析==

* **为什么先排序**：排序后数组具有单调性。固定第一个数 `nums[i]` 后，如果三数之和太小，就让 `left` 右移以增大总和；如果总和太大，就让 `right` 左移以减小总和。
* **降维思路**：三数之和可以拆成“枚举第一个数 + 在剩余有序区间中寻找两数之和”。外层枚举需要 $O(n)$，内层双指针需要 $O(n)$。
* **本题最容易错的是去重**：
    * `i > 0 and nums[i] == nums[i - 1]`：避免使用相同的第一个数重复搜索。
    * 找到答案后先同时收缩左右指针，再跳过与刚刚使用过的值相同的元素，避免生成重复三元组。
    * 去重比较的是**数值**，不是禁止使用重复元素。例如 `[-1,-1,2]` 是合法答案，因为两个 `-1` 来自不同下标。
* **为什么不能直接用集合解决所有去重问题**：可以最后把答案转成集合去重，但三元组需要先转成元组，而且会保存很多重复结果。排序后在搜索过程中去重更直接，也能避免无效计算。
* **复杂度**：排序需要 $O(n \log n)$，外层循环配合双指针需要 $O(n^2)$，因此总时间复杂度为 $O(n^2)$；除返回结果和排序所需空间外，双指针本身只使用 $O(1)$ 额外空间。

---

### 题目 4：接雨水（LeetCode 42，困难）

==原题==

给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能够接多少雨水。

* **示例：** 输入：`height = [0,1,0,2,1,0,1,3,2,1,2,1]` -> 输出：`6`

==答案==

```python
class Solution:
    def trap(self, height: List[int]) -> int:
        left, right = 0, len(height) - 1
        left_max = right_max = 0
        water = 0

        while left < right:
            if height[left] < height[right]:
                # 右边存在一根更高的柱子，左侧水位只由 left_max 决定
                left_max = max(left_max, height[left])
                water += left_max - height[left]
                left += 1
            else:
                # 左边存在一根不矮于它的柱子，右侧水位只由 right_max 决定
                right_max = max(right_max, height[right])
                water += right_max - height[right]
                right -= 1

        return water
```

==解析==

对于位置 `i`，它能接到的雨水取决于左侧最高柱和右侧最高柱中较矮的那一个：

$$

water[i] = \min(left\_max[i], right\_max[i]) - height[i]

$$

* **双指针如何省空间**：常规做法可以分别创建两个数组，记录每个位置左侧和右侧的最大高度，但需要 $O(n)$ 额外空间。双指针只维护已经扫描过的 `left_max` 和 `right_max`，把空间降到 $O(1)$。
* **为什么比较当前两端高度**：当 `height[left] < height[right]` 时，右侧当前就已经有一根比左柱更高的柱子，所以左侧这一格的短板只可能来自左边；此时可以放心结算 `left`。反之则可以结算 `right`。
* **为什么先更新最大值再计算**：当前柱子如果刷新了这一侧的最高纪录，它本身不能接水，贡献应为 `0`。先执行 `max()` 后，`left_max - height[left]` 或 `right_max - height[right]` 自然就是 `0`，也避免出现负数。
* **与“盛最多水的容器”的区别**：容器题只选择两根柱子，计算它们之间能装多少水；接雨水题则要计算**每一个凹槽位置**上方的水量并累加。两题都移动短的一侧，但维护的状态和答案含义不同。
* **复杂度**：每个位置最多处理一次，时间复杂度为 $O(n)$；只使用左右指针、两侧最大值和累计水量，空间复杂度为 $O(1)$。

# 滑动窗口

## 1. 概念

**滑动窗口（Sliding Window）** 是一种维护连续区间的算法技巧。窗口通常由左右边界 `left` 和 `right` 表示，并随着遍历不断向右移动。

窗口可以理解为字符串或数组中的一个连续片段：

$$

[left, right]

$$

窗口长度为：

$$

right - left + 1

$$

滑动窗口的核心不是“真的截取一个新数组”，而是通过两个下标和额外的数据结构，维护当前区间内的信息。

滑动窗口通常分为两类：

| 类型 | 窗口长度 | 常见问题 |
| --- | --- | --- |
| **可变长度窗口** | 根据条件扩大或缩小 | 最长无重复子串、最小覆盖子串 |
| **固定长度窗口** | 始终保持指定长度 | 字母异位词、定长区间最大值 |

> **核心流程**
> 1. `right` 向右移动，把新元素加入窗口；
> 2. 窗口不满足条件时，移动 `left` 并移除左侧元素；
> 3. 窗口满足条件时，记录长度、下标或其他答案。

相比枚举所有子串的两层循环，滑动窗口不会反复扫描已经处理过的区间。左右指针通常都只向右移动，因此很多问题可以从 $O(n^2)$ 优化到 $O(n)$。

## 2. 常用方法

* **可变长度窗口模板**

    ```python
    left = 0

    for right, value in enumerate(data):
        # 将 value 加入窗口

        while 窗口不满足条件:
            # 移除 data[left]
            left += 1

        # 此时窗口满足条件，可以更新答案
    ```

    可变窗口中，`right` 负责扩大窗口，`left` 负责恢复窗口的合法性。

* **固定长度窗口模板**

    ```python
    window_size = k

    for right, value in enumerate(data):
        # 将 value 加入窗口

        if right >= window_size:
            # 移除刚刚离开窗口的元素
            remove_value = data[right - window_size]

        if right >= window_size - 1:
            # 当前窗口长度已经达到 k，可以检查或记录答案
    ```

* **`set`：维护窗口中不能重复的元素**

    ```python
    window = set()
    window.add(value)
    window.remove(value)
    value in window
    ```

    当题目只关心“某个元素是否已经出现”时，使用集合最直接。

* **字典或数组：维护窗口中的元素频率**

    ```python
    count = {}
    count[ch] = count.get(ch, 0) + 1
    ```

    如果字符范围固定为小写英文字母，也可以使用长度为 `26` 的数组：

    ```python
    index = ord(ch) - ord("a")
    count[index] += 1
    ```

* **为什么收缩窗口时经常使用 `while` 而不是 `if`**

    一次移动 `left` 不一定能让窗口重新合法。例如窗口中可能存在多个冲突元素，因此需要持续收缩，直到条件重新成立。

## 3. 题目

### 题目 1：无重复字符的最长子串（LeetCode 3，中等）

==原题==

给定一个字符串 `s`，请找出其中不含重复字符的最长子串的长度。

* **示例 1：** 输入：`s = "abcabcbb"` -> 输出：`3`，最长子串可以是 `"abc"`。
* **示例 2：** 输入：`s = "bbbbb"` -> 输出：`1`。
* **示例 3：** 输入：`s = "pwwkew"` -> 输出：`3`，最长子串可以是 `"wke"`。

> 题目要求的是**子串**，字符在原字符串中必须连续；不是可以跳过字符的子序列。

==错误答案==

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
	    window=set()
	    left=0
	    right=0
	    ans=0
	    n=len(s)
	    while right<n:
		    if s[right] not in window:
			    window.add(s[right])
			    right++
			else:
				left++
				right++
		ans=right-left+1
	return ans
```

==错误解析==

* Python 不支持 `left++`、`right++`，应写成 `left += 1`、`right += 1`；原代码的缩进也不统一，会产生语法错误。
* 遇到重复字符时，只移动指针却没有执行 `window.remove(s[left])`，导致集合与实际窗口不一致。
* 左边界可能需要连续移动多次，因此应使用 `while` 收缩窗口，直到重复字符被移除；此时不能同时移动 `right`，否则会跳过当前字符。
* `ans` 应在每次窗口合法后通过 `max()` 更新，而不是循环结束后只计算一次。
* 正确窗口为 `[left, right]` 时，长度才是 `right - left + 1`。

==答案==

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        # window 保存当前窗口中出现的字符
        window = set()
        left = 0
        longest = 0

        for right, ch in enumerate(s):
            # 新字符已经存在时，从左侧持续缩小窗口
            while ch in window:
                window.remove(s[left])
                left += 1

            # 此时 ch 与窗口中的字符不重复
            window.add(ch)
            longest = max(longest, right - left + 1)

        return longest
```

==解析==

* **窗口代表什么**：`[left, right]` 始终表示当前不含重复字符的连续子串，`window` 保存其中的字符。
* **为什么先处理重复，再加入字符**：如果 `ch` 已经在集合中，直接加入不会改变集合，而且窗口仍然包含重复字符。必须先移动左边界，把旧的 `ch` 移出窗口。
* **为什么使用 `while`**：重复字符不一定就在 `left` 位置。需要连续移除左侧字符，直到旧的重复字符真正离开窗口。
* **什么时候更新答案**：完成收缩并加入 `ch` 后，窗口重新合法，此时用 `right - left + 1` 更新最长长度。
* **复杂度**：每个字符最多进入集合一次、离开集合一次，时间复杂度为 $O(n)$；集合最多保存窗口中的字符，空间复杂度为 $O(k)$，其中 $k$ 是字符集或窗口大小。

---

### 题目 2：找到字符串中所有字母异位词（LeetCode 438，中等）

==原题==

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的字母异位词子串，返回这些子串的起始索引。答案可以按任意顺序返回。

* **示例 1：** 输入：`s = "cbaebabacd"`，`p = "abc"` -> 输出：`[0, 6]`。
* **示例 2：** 输入：`s = "abab"`，`p = "ab"` -> 输出：`[0, 1, 2]`。

字母异位词使用的字符及其出现次数完全相同，只是排列顺序不同。因此，本题不需要比较字符串顺序，只需要比较字符频率。

==答案==

```python
class Solution:
    def findAnagrams(self, s: str, p: str) -> List[int]:
        window_size = len(p)
        answer = []

        # p 比 s 长时，不可能存在对应子串
        if window_size > len(s):
            return answer

        # 题目限定为小写英文字母，用长度为 26 的数组计数
        need = [0] * 26
        window = [0] * 26

        for ch in p:
            need[ord(ch) - ord("a")] += 1

        for right, ch in enumerate(s):
            # 新字符进入窗口
            window[ord(ch) - ord("a")] += 1

            # 窗口超过 p 的长度时，移除最左侧字符
            if right >= window_size:
                left_ch = s[right - window_size]
                window[ord(left_ch) - ord("a")] -= 1

            # 窗口达到固定长度后，比较字符频率
            # 我觉得是为了代码可读性，其实直接写if window == need:是一样的
            if right >= window_size - 1 and window == need:
                answer.append(right - window_size + 1)

        return answer
```

==解析==

* **为什么是固定长度窗口**：`p` 的字母异位词一定与 `p` 长度相同，所以窗口大小始终保持为 `len(p)`。
* **窗口如何移动**：每轮先让 `s[right]` 进入窗口；当窗口长度超过 `len(p)` 时，再让下标 `right - len(p)` 对应的字符离开窗口。
* **为什么比较频率数组**：异位词不要求字符顺序相同，只要求每种字符出现次数相同。两个长度为 `26` 的频率数组相等，就说明当前窗口是 `p` 的异位词。
* **起始下标怎么算**：当前右边界是 `right`，窗口长度是 `window_size`，因此起点为 `right - window_size + 1`。
* **复杂度**：字符串只遍历一次。频率数组长度固定为 `26`，比较成本是常数，因此时间复杂度为 $O(n)$；两个计数数组的空间复杂度为 $O(1)$。

# 子串

## 1. 概念

子串或子数组是原数据中一段**连续**的区间：

- 字符串中的连续片段叫 **子串（substring）**；
- 数组中的连续片段叫 **子数组（subarray）**；
- 子序列可以跳过元素，而子串和子数组不可以。

例如，`"abc"` 是 `"abcd"` 的子串，`"ac"` 只能算子序列。

子串问题常用三类方法：

| 方法        | 适用场景             |
| --------- | ---------------- |
| 前缀和 + 哈希表 | 统计和满足条件的子数组      |
| 单调队列      | 快速获取每个窗口的最大值或最小值 |
| 滑动窗口      | 寻找满足条件的最短或最长连续区间 |

## 2. 常用方法

* **前缀和**

    `prefix[i]` 表示前 `i` 个元素之和，则区间 `[left, right]` 的和为：

    $$

    prefix[right + 1] - prefix[left]

    $$

    ```python
    prefix = 0
    for num in nums:
        prefix += num
    ```

* **前缀和 + 哈希表**

    哈希表可以记录“某个前缀和出现了几次”。如果当前前缀和为 `prefix`，要找到和为 `k` 的子数组，只需查询 `prefix - k` 是否出现过。

    Python 字典的 `get` 语法为：

    ```python
    dictionary.get(key, default_value)
    ```

    它会查询 `key` 对应的值；如果 `key` 不存在，则返回 `default_value`，而不是报 `KeyError`。例如：

    ```python
    count = {3: 2}

    count.get(3, 0)  # 3 存在，返回 2
    count.get(5, 0)  # 5 不存在，返回默认值 0
    ```

    因此，`prefix_count.get(prefix - k, 0)` 表示：查询“前缀和 `prefix - k` 之前出现过几次”；如果从未出现，就按 `0` 次计算。它等价于：

    ```python
    if prefix - k in prefix_count:
        answer += prefix_count[prefix - k]
    else:
        answer += 0
    ```

    同理，下面的 `prefix_count.get(prefix, 0) + 1` 表示：取出当前前缀和原来的出现次数（不存在时按 `0` 计），然后加 `1`。

* **单调队列**

    单调队列通常使用 `deque` 保存下标，并让对应数值保持单调。队首始终是当前窗口的最大值或最小值。

    ```python
    from collections import deque

    queue = deque()
    queue.append(index)
    queue.popleft()
    ```

* **堆（优先队列）**

    堆是一种能快速取出“优先级最高元素”的数据结构。在小根堆中，堆顶始终是最小元素；在大根堆中，堆顶始终是最大元素。

    Python 的 `heapq` 默认实现**小根堆**。如果想当作大根堆使用，可以将数值取负后放入堆中：原来越大的数，取负后反而越小，因此会位于小根堆的堆顶。

    ```python
    import heapq

    heap = []
    heapq.heappush(heap, value)  # 插入元素，O(log n)
    top = heap[0]                # 查看堆顶，O(1)
    value = heapq.heappop(heap)  # 删除并返回堆顶，O(log n)
    heapq.heapify(data)          # 将列表原地转换为堆，O(n)
    ```

    堆只保证堆顶元素的优先级最高，**不保证整个列表完全有序**。在滑动窗口问题中，通常将 `(值, 下标)` 一起存入堆，以便判断堆顶元素是否已经离开窗口。

## 3. 题目

### 题目 1：和为 K 的子数组（LeetCode 560，中等）

==原题==

给定一个整数数组 `nums` 和一个整数 `k`，请统计和等于 `k` 的连续非空子数组的数量。

* **示例 1：** 输入：`nums = [1,1,1]`，`k = 2` -> 输出：`2`。
* **示例 2：** 输入：`nums = [1,2,3]`，`k = 3` -> 输出：`2`。

==答案==

```python
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        # 前缀和 0 在遍历前出现过一次
        prefix_count = {0: 1}
        prefix = 0
        answer = 0

        for num in nums:
            prefix += num

            # prefix - old_prefix = k
            answer += prefix_count.get(prefix - k, 0)

            prefix_count[prefix] = prefix_count.get(prefix, 0) + 1

        return answer
```

==解析==

* **核心等式**：若 `当前前缀和 - 之前的前缀和 = k`，两者之间的子数组和就是 `k`。
* **为什么初始化 `{0: 1}`**：这样从数组开头开始、和恰好为 `k` 的子数组也能被统计。
* **为什么先查询再记录**：只能使用当前下标之前的前缀和，避免把当前位置与自己错误匹配。
* **为什么不用普通滑动窗口**：数组中允许出现负数，扩大窗口后总和不一定增大，无法判断应该移动哪一侧。
* **复杂度**：时间复杂度为 $O(n)$，空间复杂度为 $O(n)$。

---

### 题目 2：滑动窗口最大值（LeetCode 239，困难）

==原题==

给定整数数组 `nums` 和窗口大小 `k`，窗口从数组最左侧移动到最右侧，每次向右移动一格。请返回每个窗口中的最大值。

* **示例：** 输入：`nums = [1,3,-1,-3,5,3,6,7]`，`k = 3` -> 输出：`[3,3,5,5,6,7]`。

==我的答案==
```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        answer = []

        for left in range(len(nums) - k + 1):
            right = left + k
            m=max(nums[left:right])
            answer.append(m)

        return answer
```

==这样写为什么不好==

* **每次都重新遍历整个窗口**：`max(...)` 需要检查窗口中的 `k` 个元素，时间复杂度为 $O(k)$。一共有约 `n` 个窗口，因此总时间复杂度是 $O(nk)$，当 `n` 和 `k` 很大时容易超时。
* **切片还会复制数据**：`nums[left:right]` 会新建一个长度为 `k` 的列表，每轮都会产生额外的时间和临时空间开销。
* **没有利用相邻窗口的重叠部分**：窗口每次只移动一格，前后两个窗口有 `k - 1` 个相同元素，但这种写法每次都从头求最大值。

这个思路能得到正确结果（修复括号后），但效率不够好。下面先介绍优先队列，再进一步优化为单调队列。

==方法一：优先队列（堆）==

优先队列不会每次重新扫描整个窗口，而是将元素放入堆中，通过堆顶获取当前最大值。Python 只提供小根堆，因此代码存入 `-nums[i]`，用“最小的负数”间接表示“最大的原数”。

```python
import heapq


class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)

        # Python 默认是小根堆，所以存入数值的相反数
        # 元素格式：(-数值, 下标)
        queue = [(-nums[i], i) for i in range(k)]
        heapq.heapify(queue)

        answer = [-queue[0][0]]

        for i in range(k, n):
            heapq.heappush(queue, (-nums[i], i))

            # 堆顶元素已经离开当前窗口时，将它删除
            while queue[0][1] <= i - k:
                heapq.heappop(queue)

            answer.append(-queue[0][0])

        return answer
```

* `queue[0]` 是堆顶，`-queue[0][0]` 就是当前窗口的最大值。
* 元素必须同时保存下标，因为需要用 `queue[0][1] <= i - k` 判断堆顶是否已经离开窗口。
* 这里采用“惰性删除”：已过期但不在堆顶的元素暂时保留；只有当它到达堆顶、可能影响答案时才删除。
* 时间复杂度为 $O(n\log n)$，空间复杂度为 $O(n)$。

==从优先队列过渡到单调队列==

堆已经避免了对每个窗口做 $O(k)$ 的完整扫描，但每次插入和删除仍需要 $O(\log n)$。这道题其实只关心当前窗口的最大值：如果一个新元素大于队尾的旧元素，那么这个更小的旧元素比新元素更早离开窗口，以后永远不可能成为最大值，可以立即删除。

利用这一点，可以让队列中对应的数值始终从大到小排列，队首便始终是窗口最大值。这就是**单调递减队列**，它可以把时间复杂度进一步降为 $O(n)$。

==方法二：单调队列==

```python
from collections import deque

class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        queue = deque()  # 保存下标，对应值从大到小
        answer = []

        for right, num in enumerate(nums):
            # 移除已经离开窗口的下标
            if queue and queue[0] <= right - k:
                queue.popleft()

            # 当前值更大时，队尾元素不可能再成为最大值
            while queue and nums[queue[-1]] <= num:
                queue.pop()

            queue.append(right)

            if right >= k - 1:
                answer.append(nums[queue[0]])

        return answer
```

==解析==

* **为什么保存下标**：既可以通过 `nums[index]` 比较大小，也能判断元素是否已经离开窗口。
* **为什么队列单调递减**：比当前元素更小且更早出现的元素，以后不可能成为窗口最大值，可以直接删除。
* **为什么队首是答案**：队列中的值从大到小排列，队首下标对应当前窗口最大值。
* **复杂度**：每个下标最多入队、出队一次，时间复杂度为 $O(n)$，队列空间复杂度为 $O(k)$。

---

### 题目 3：最小覆盖子串（LeetCode 76，困难）

==原题==

给定字符串 `s` 和 `t`，返回 `s` 中包含 `t` 全部字符的最短子串。如果不存在，则返回空字符串 `""`。`t` 中的重复字符也必须满足对应次数。

* **示例 1：** 输入：`s = "ADOBECODEBANC"`，`t = "ABC"` -> 输出：`"BANC"`。
* **示例 2：** 输入：`s = "a"`，`t = "aa"` -> 输出：`""`。

==答案==

```python
from collections import Counter, defaultdict


class Solution:
    def minWindow(self, s: str, t: str) -> str:
        need = Counter(t)
        window = defaultdict(int)

        required = len(need)  # 需要满足的字符种类数
        formed = 0            # 已满足的字符种类数
        left = 0
        best_start = 0
        best_length = float("inf")

        for right, ch in enumerate(s):
            window[ch] += 1

            if ch in need and window[ch] == need[ch]:
                formed += 1

            # 当前窗口已经覆盖 t，尝试从左侧缩小
            while formed == required:
                current_length = right - left + 1
                if current_length < best_length:
                    best_start = left
                    best_length = current_length

                left_ch = s[left]
                if left_ch in need and window[left_ch] == need[left_ch]:
                    formed -= 1

                window[left_ch] -= 1
                left += 1

        if best_length == float("inf"):
            return ""

        return s[best_start:best_start + best_length]
```

==解析==

* **窗口扩大**：右指针不断加入字符，直到窗口包含 `t` 所需的全部字符和数量。
* **窗口收缩**：条件满足后移动左指针，寻找更短的合法窗口；一旦某个必要字符数量不足，就停止收缩。
* **`formed` 的作用**：记录已经满足数量要求的字符种类，避免每次都完整比较两个字典。
* **为什么先判断再减少**：如果移出的字符数量原本刚好达到要求，移除后该字符就不再满足，需要让 `formed -= 1`。
* **复杂度**：左右指针都只向右移动，时间复杂度为 $O(|s|+|t|)$，空间复杂度为 $O(|s|+|t|)$；若只考虑有限字符集，也可视为 $O(1)$。
