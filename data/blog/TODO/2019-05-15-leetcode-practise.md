---
title: LeetCode 刷题总结
description: 算法深似海，是祸躲不过
toc: false
hide: true
categories: [algorithm]
author: BjChacha
---

<!--Format:
##
* **Description**:
  *
* **Analysis**:
  *
  * 时间复杂度：$O()$
  * 空间复杂度：$O()$
* **Analysis**:
    ```cpp
    // Runtime:  ms, beats  %
    // Memory:  MB, beats  %
    code here
    ```

---
-->

## 0. Preface

- 刷`Leetcode`过程中记录所做过题目的答案，偏向归档属性。
- `Leetcode`许多题目有多种算法解，以下一般只采用其中一种，优先选择`runtime`较低、复杂程度较低的方案。
- `Leetcode`的`runtime`一般情况下并不代表时间复杂度，有许多情况下时间复杂度较高的算法`runtime`却很低。所以不要本文盲目追求低`runtime`，理解并熟练运用多种算法来处理问题才是王道。

## 1. Two Sum

- **Description**:
  - 给定一个`int`数组，输出其两数之和的最大值。
- **Analysis**:
  - 遍历一次数组，用`unordered_map`（哈希表）记录每个出现过的值及其索引值。若在表中查找出目标与当前值的差值，则返回两数的索引值。
  - 时间复杂度：$O(n)$
  - 空间复杂度：$O(n)$
- **Solution**:
  ```cpp
  // Runtime: 8 ms, beats 99.94 %
  // Memory: 10.2 MB, beats 51.82 %
  class Solution {
  public:
      vector<int> twoSum(vector<int>& nums, int target) {
          unordered_map<int, int>m;
          for(auto i = 0; i < nums.size(); ++i){
              if(m.count(target - nums[i])) return {m[target - nums[i]], i};
              m[nums[i]] = i;
          }
          return {};
      }
  };
  ```

---

## 2. Add Two Numbers

- **Description**:
  - 给定两个链表，将两个链表表示的非负整数相加，并输出其链表形式（从低位指向高位）。
- **Analysis**:
  - 由于整数链表由低位指向高位，可以直接同时遍历两个链表，对应项相加并带进位。注意最高位进位时会增加一个节点。
  - 链表使用细节比较多，考验 C++基础。
  - 时间复杂度：$O(max(m,n))$
  - 空间复杂度：$O(max(m,n))$
- **Solution**:
  ```cpp
  // Runtime: 28 ms, beats 97.91 %
  // Memory: 10.4 MB, beats 99.21 %
  class Solution {
  public:
      ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
          ListNode head(0);
          ListNode *p1 = l1, *p2 = l2, *p = &head;
          int ans = 0;
          while (p1 && p2){
              ans = ans / 10  + p1->val + p2->val;
              p->next = new ListNode(ans%10);
              p = p->next;
              p1 = p1->next;
              p2 = p2->next;
          }
          while (p1){
              ans = ans / 10 + p1->val;
              p->next = new ListNode(ans%10);
              p = p->next;
              p1 = p1->next;
          }
          while (p2){
              ans = ans / 10 + p2->val;
              p->next = new ListNode(ans%10);
              p = p->next;
              p2 = p2->next;
          }
          if (ans / 10 > 0) p->next = new ListNode(1);
          return head.next;
      }
  };
  ```

---

## 3. Longest Substring Without Repeating Characters

- **Description**:
  - 给定一个字符串，输出其中最大不重复子串。
- **Analysis**:
  - 用`unordered_map`记录出现过的字符，若出现重复，则从上一个重复字符的索引开始重新遍历。
  - 优化：使用`start`标记子串的开始位置，则不需要索引倒退，实现“一遍过”。
  - 时间复杂度：$O(n)$
  - 空间复杂度：$O(min(m,n))$，其中$m$是字母集大小。
- **Solution**:
  ```cpp
  // Runtime: 20 ms, beats 90.42 %
  // Memory Usage: 10.9 MB, beats 98.81 %
  class Solution {
  public:
      int lengthOfLongestSubstring(string s) {
          unordered_map<int, int> m;
          const int n = s.size();
          int max_len = 0, i = 0, start = 0;
          while (i < n){
              if (m.count(s[i])) start = max(m[s[i]] + 1, start);
              max_len = max(max_len, i- start + 1);
              m[s[i++]] = i;
          }
          return max_len;
      }
  };
  ```

---

## 4. Median of Two Sorted Arrays

- **Description**:
  - 给定两个有序数列，输出两个数列并集的中值。
- **Analysis**:

  - 先确定中值的定义：将数列分隔成等长两个子集，其中左子集始终小于右子集。
  - 如果将两个数列分别分隔成左、右两子集，左两子集的并集始终小于右两子集的并集，且两并集等长，则分隔点就是中值。
  - 假设用`i`和`j`分别将两个数列分隔成两部分，如下：

              left_A             |        right_A
        A[0], A[1], ..., A[i-1]  |  A[i], A[i+1], ..., A[m-1]
              left_B             |        right_B
        B[0], B[1], ..., B[j-1]  |  B[j], B[j+1], ..., B[n-1]

  - 将`left_A`和`left_B`、`right_A`和`right_B`合并，如下：

              left               |        right
        A[0], A[1], ..., A[i-1]  |  A[i], A[i+1], ..., A[m-1]
        B[0], B[1], ..., B[j-1]  |  B[j], B[j+1], ..., B[n-1]

  - 要使得`left`始终小于`right`，只需满足两个条件：
    - A[i-1] < B[j]
    - B[j-1] < A[i]
  - 要使得`left`与`right`等长，即$i+j=m−i+n−j$，这里使：
    - $i = \frac{m+n}{2}$
    - $j = \frac{m+n+1}{2}-i$ （注意：这里+1 是为了确保 $j$ 的位置意义与 $i$ 一致，同为右部分的初始位置）
  - 接下来只需寻找满足条件的 $i$ 值即可，可采用二分查找。
  - 时间复杂度：$O(log(min(m,n))$， 首先 $i$ 的查找范围为$[0, m]$，使用二分查找，时间复杂度为$O(log(m))$；另外该算法前提是$m<n$，若$m>n$则符号对调，故时间复杂度为$O(log(min(m,n)))$。
  - 空间复杂度：$O(1)$

- **Solution**:
  ```cpp
  // Runtime: 24 ms, beats 99.11 %
  // Memory Usage: 9.6 MB, beats 99.57 %
  class Solution {
  public:
      double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
          int m = nums1.size(), n = nums2.size();
          if (nums1.size() > nums2.size()) return findMedianSortedArrays(nums2, nums1);  // 确保m < n,否则j有可能为负数
          int imin = 0, imax = m, f = (m + n + 1) / 2;
          while (imin <= imax){                                                          // 二分查找i值
              int i = (imin + imax) / 2;
              int j = f - i;
              if (i > imin && nums1[i-1] > nums2[j]) imax = i - 1;
              else if (i < imax && nums2[j-1] > nums1[i]) imin = i + 1;
              else{                                                                      // 边界处理,比较繁琐
                  int max_left;
                  if (i == 0) max_left = nums2[j-1];
                  else if (j == 0) max_left = nums1[i-1];
                  else max_left = max(nums1[i-1], nums2[j-1]);
                  if ((m + n) % 2) return max_left;
                  int min_right;
                  if (i == m) min_right = nums2[j];
                  else if (j == n) min_right = nums1[i];
                  else min_right = (min(nums1[i], nums2[j]));
                  return (max_left + min_right) / 2.0;
              }
          }
          return .0;
      }
  };
  ```

---

## 5. Longest Palindromic Substring

- **Description**:
  - 给定一个字符串，输出其中最长回文子串。
- **Analysis**:
  - 回文有两种：单核心（如`aba`）和双核心（如`abba`）。
  - 方法一：中心扩张法。遍历每个字符，以该字符为中心向两边扩张检测回文性，其中要包括的单核心扩张和双核心扩张。
    - 优化：可将遍历范围缩小到$[0,n-maxlen/2]$，其中$maxlen$为目前最大回文子串的长度。
    - 优化：由于双核心的回文核心必然是两个相同字符，即使不是双核心回文，如`abbba`也是回文。故可用`left`和`right`标记扩张位置，且遍历核心时`right`可跳过所有相同字符，直接到达回文右端。无需分类讨论。
    - 时间复杂度：$O(n^{2})$
    - 空间复杂度：$O(1)$
  - 方法二：动态规划。若字符串`s[i:j]`为回文串，则子串`s[i-1:j+1]`肯定也是回文串，故`dp(i,j) = (dp(i-1,j-1) && s[i] == s[j])`
    - 时间复杂度：$O(n^{2})$
    - 空间复杂度：$O(n^{2})$
  - 方法三：Manacher's Algorithm（略）
- **Solution**:
  - Solution 1: Expand Around Center
    ```cpp
    // Runtime: 4 ms, beats 100.00 %
    // Memory Usage: 8.8 MB, beats 99.48 %
    class Solution {
    public:
        string longestPalindrome(string s) {
            int n = s.size(), max_len = 0, start = 0;
            int i = 0;
            /* 使用for循环感觉更好看，但runtime从4ms变成16ms
            for (int i = 0, l = 0, r = 0; i <= n - max_len/2; r = l = ++i){
                while (r < n - 1 && s[r] == s[r+1]) r++;
                while (l > 0 && r < n - 1 && s[l-1] == s[r+1]) l--, r++;
                int len = r - l + 1;
                if(max_len < len) max_len = len, start = l;
            }
            */
            while(i < n - max_len/2){
                int l = i, r = i;                                           // 左标记和右标记，各从i开始
                while (r < n - 1 && s[r] == s[r+1]) ++r;                    // 右标记到连续相同字符的最右，同时解决双核心回文串
                i = r + 1;                                                  // i从本次回文串核心的下个字符开始，而不是逐个检索。另外不能从本次回文串的下个字符开始，比如ababa
                while (l > 0 && r < n - 1 && s[l-1] == s[r+1]) --l, ++r;    // 类似Expand Around Center，从核心向两侧检测回文特性
                int len = r - l + 1;
                if (max_len < len) max_len = len, start = l;
            }
            return s.substr(start, max_len);
        }
    };
    ```
  - Solution 2: Dynamic Programming
    ```cpp
    // Runtime: 108 ms, beats 41.70 %
    // Memory Usage: 9.9 MB, beats 64.72 %
    class Solution {
    public:
        string longestPalindrome(string s) {
            const int n = s.size();
            if (n < 1) return "";
            int max_len = 1, start = 0;
            bool f[n][n];                                                    // f(i,j) 代表子串s[j,i]是否为回文串
            for (int i = 0; i < n; ++i){
                f[i][i] = true;
                for (int j = 0; j < i; ++j){
                    f[j][i] = (s[i] == s[j] && (i - j < 2 || f[j+1][i-1]));  // 若子串s[j,i]是回文串，且s[i-1] == s[j+1]， 则子串s[j-1,i+1]是回文串
                    if (f[j][i] && max_len < (i - j + 1)){
                        max_len = i - j + 1;
                        start = j;
                    }
                }
            }
            return s.substr(start, max_len);
        }
    };
    ```

---

## 6. ZigZag Conversion

- **Description**:

  - 给定一个字符串和行数，按行输出其锯齿形状。
  - 如输入`s = "PAYPALISHIRING", numRows = 3`，输出`"PAHNAPLSIIGYIR"`，如下：

        P   A   H   N
        A P L S I I G
        Y   I   R

- **Analysis**:

  - 这是个找数学规律的算法。锯齿文字中，每个纵列之间的间隔`(max_setp)`是一定的,而位于斜列上的字符，都按规律将这个间隔分成两部分。
  - 时间复杂度：$O(n)$
  - 空间复杂度：$O(n)$

- **Solution**:
  ```cpp
  // Runtime: 12 ms, beats 99.49 %
  // Memory Usage: 10.2 MB, beats 99.79 %
  class Solution {
  public:
      string convert(string s, int numRows) {
          if (numRows < 2 or s.size() < numRows) return s;
          const int max_step = (numRows - 1) * 2, n = s.size();
          int row = 0, step;
          string ans;
          while (row < numRows){
              int index = row;
              if (row == 0 or row == numRows - 1) {
                  step = max_step;
                  while (index < n) ans += s[index], index += step;
              }
              else{
                  bool even = true;
                  while (index < n){
                      step = even ? max_step - 2 * row : 2 * row;
                      ans += s[index];
                      index += step;
                      even = !even;
                  }
              }
              ++row;
          }
          return ans;
      }
  };
  ```

---

## 7. Reverse Integer

- **Description**:
  - 给定一个整数，输出其反转后的整数。注意不可溢出($[-2^{32}, 2^{32}-1]$)。
- **Analysis**:
  - 从低位逐位取出数字，从高位逐位放置数字。
  - 时间复杂度：$O(n)$，`n`为输入整数的位数。
  - 空间复杂度：$O(1)$
- **Solution**:
  ```cpp
  // Runtime: 4 ms, beats 100.00 %
  // Memory Usage: 8.1 MB, beats 99.80 %
  class Solution {
  public:
      int reverse(int x) {
          int ans = 0, b;
          while (x != 0){
              b = x % 10;
              if (x > 0 && ans > (INT_MAX - b)/10) return 0;
              else if (x < 0 && ans < (INT_MIN - b)/10) return 0;
              ans = ans * 10 + b;
              x /= 10;
          }
          return ans;
      }
  };
  ```

---

## 8. String to Integer (atoi)

- **Description**:
  - 给定一个数字字符串，输出其整型。
- **Analysis**:
  - 细节题，逐位处理即可。
  - 时间复杂度：$O(n)$
  - 空间复杂度：$O(1)$
- **Solution**:
  ```cpp
  // Runtime: 8 ms, beats 99.69 %
  // Memory Usage: 8.4 MB, beats 100.00 %
  class Solution {
  public:
      int myAtoi(string str) {
          const int n = str.size(), ascii_0 = '0';
          int i = 0, ans = 0, sign = 1, a;
          // 处理前端空白字符
          while (i < n && str[i] == ' ') ++i;

          // 处理符号字符
          if (i < n && str[i] == '-') sign = -1, ++i;
          else if (i < n && str[i] == '+') ++i;

          // 处理数字字符
          while (i < n){
              if (isdigit(str[i])){
                  a = ((int)str[i] - ascii_0) * sign;
                  if (sign > 0 && ans > (INT_MAX - a)/10) return INT_MAX;
                  else if (sign < 0 && ans < (INT_MIN - a)/10) return INT_MIN;
                  else ans = ans * 10 + a;
              }
              else break;
              ++i;
          }
          return ans;
      }
  };
  ```

---

## 9. Palindrome Number

- **Description**:
  - 输出一个整数，判断其是否回文数。
- **Analysis**:
  - 判断是否回文比判断是否存在回文要简单。直接头尾进行判断即可。
  - 时间复杂度：$O(n)$，`n`为输入整数的位数。
  - 空间复杂度：$O(1)$
- **Solution**:

  ```cpp
  // Runtime: 32 ms, beats 99.54 %
  // Memory Usage: 7.9 MB, beats 100.00 %
  class Solution {
  public:
      bool isPalindrome(int x) {
          if (x < 0) return false;
          int n = 0, temp = x;
          while (temp > 0){
              temp /= 10;
              ++n;
          }
          int i = 0, end = (n + 1)/2;
          while (i < end){
              if (pickNumIn(x, i) != pickNumIn(x, n - i - 1)) return false;
              ++i;
          }
          return true;
      }

  private:
      int pickNumIn(int x, int i){         // start with 0
          while (i-- > 0) x /= 10;
          return x % 10;
      }
  };
  ```

---

## 10. Regular Expression Matching

- **Description**:
  - 给定一个字符串和正则表达式，判断正则表达式是否可以完全表示该字符串。
- **Analysis**:

  - （后记：刚开始为了统一才硬是用英语写分析，毫无语法，以后有空再改写成中文）
  - Using Dynamic Programming to solve this problem.
  - While itering, ther are 3 possible situations in p: `a-z`, `'.'`, `'*'`. (assuming `i` = current substring index in `s`)
    - 1. `a-z` : if char is matched and dp[i-1] is true, then dp[i] is true.
    - 2. `'.'` : always matched.
    - 3. `'*'` :
      - (1) if the preceding char is matched, then the `'*'` combo can means 0, 1 or more this char.
      - (2) otherwise, the `'*'` combo can only means 0 (in other word, don't match) this char.
  - So we can use a matrix called dp to represent how pattern matching the string, initializing with false.
  - For an example, `s` = `"aaaa"`, `p` = `"a*b*"`:

             ""  a   a   a   a
         ""  0   0   0   0   0
         a   0   0   0   0   0
         *   0   0   0   0   0
         b   0   0   0   0   0
         *   0   0   0   0   0

  - step 1.1: preprocessing, set `dp[0][0]` true because `""` do match with `""`

             ""  a   a   a   a
         ""  1   0   0   0   0
         a   0   0   0   0   0
         *   0   0   0   0   0
         b   0   0   0   0   0
         *   0   0   0   0   0

  - step 1.2: `'*'` can match zero element, so `dp[i][0]` = `dp[i-2][0]` meaning `'*'` and preceding element match nothing.

             ""  a   a   a   a
         ""  1   0   0   0   0
         a   0   0   0   0   0
         *   1   0   0   0   0
         b   0   0   0   0   0
         *   1   0   0   0   0

  - step 2.1: start itering, if element matches without `'*'`, then `dp[i][j]` = `dp[i-1][j-1]`

             ""  a   a   a   a
         ""  1   0   0   0   0
         a   0   1   0   0   0
         *   1   0   0   0   0
         b   0   0   0   0   0
         *   1   0   0   0   0

  - step 2.2: if `'*'` and the preceding element matched, there are 3 meaning about `'*'`: doesn't match (`dp[i-2][j]`), matches one element (`dp[i-1][j]`), matches several elements (`dp[i][j-1]`).

    - case 1: match one element:

                ""  a   a   a   a
            ""  1   0   0   0   0
            a   0   1   0   0   0
            *   1   1   0   0   0
            b   0   0   0   0   0
            *   1   0   0   0   0

    - case 2: match several elements:

                ""  a   a   a   a
            ""  1   0   0   0   0
            a   0   1   0   0   0
            *   1   1   1   1   1
            b   0   0   0   0   0
            *   1   0   0   0   0

    - case 3: don't match (in this case do nothing):

                ""  a   a   a   a
            ""  1   0   0   0   0
            a   0   1   0   0   0
            *   1   1   1   1   1
            b   0   0   0   0   0
            *   1   0   0   0   0

  - step 2.3: if `'*'` and the preceding element doesn't match, like step 1.2, `dp[i][0]` = `dp[i-2][0]` meaning `'*'` and preceding element match nothing.

             ""  a   a   a   a
         ""  1   0   0   0   0
         a   0   1   0   0   0
         *   1   1   1   1   1
         b   0   0   0   0   0
         *   1   1   1   1   1

  - Finally `dp[n][m]` is the result, in this case dp[4][4] is true, while `"a*b*"` matches `"aaaa"`

  - For another example, `s` = `"aab"`, `p` = `"c*a*b"`. at last the result is true:

             ""  a   a   b
         ""  1   0   0   0
         c   0   0   0   0
         *   1   0   0   0
         a   0   1   0   0
         *   1   1   1   0
         b   0   0   0   1

  - Notes: the `i` and `j` above are not the same with below, which I use dp[j][i].
  - Time Complexity: $O(TP)$, `T` is the length of the text, `P` is the length of the pattern.
  - Space Complexity: $O(TP)$

- **Solution**:
  ```cpp
  // Runtime: 4 ms, beats 100.00 %
  // Memory Usage: 8.2 MB, beats 100.00 %
  class Solution {
  public:
      bool isMatch(string s, string p) {
          const int m = s.size(), n = p.size();
          int i = 0, j = 0;
          bool dp[n+1][m+1];                                       // i - n - p, j - m - s
          memset(dp, false, sizeof(bool)*(m+1)*(n+1));             // i at string corresponds with i+1 at dp matrix
          dp[0][0] = true;
          while (j++ < n){                                         // scan * while i = 0
              if (j > 1 && p[j-1] == '*') dp[j][0] = dp[j-2][0];
          }
          while (i++ < m){
              j = 0;
              while (j++ < n){
                  if (p[j-1] == s[i-1] || p[j-1] == '.') dp[j][i] = dp[j-1][i-1];       // if not * but match
                  else if (p[j-1] == '*'){                                              // if * |
                      if (p[j-2] != s[i-1] && p[j-2] != '.') dp[j][i] = dp[j-2][i];     //      | not match
                      else dp[j][i] = (dp[j-2][i] || dp[j-1][i] || dp[j][i-1]);         //      | match
                  }
              }
          }
          return dp[n][m];
      }
  };
  ```

---

## 11. Container With Most Water

- **Description**:

  - 给定一个整型数组，输出其柱状图能容纳水的最大体积。
  - 例子：`[1,8,6,2,5,4,8,3,7]`，输出如图：

        9 |         8   area:(8-1)x7=49      8
        8 |        |_|______________________|_|________7_
        7 |        | |   6                  | |       | |
        6 |        | |  | |        5        | |       | |
        5 |        | |  | |       | |   4   | |       | |
        4 |        | |  | |       | |  | |  | |       | |
        3 |        | |  | |   2   | |  | |  | |  | |  | |
        2 |    1   | |  | |  | |  | |  | |  | |  | |  | |
        1 |   | |  | |  | |  | |  | |  | |  | |  | |  | |
        0 |———| |——| |——| |——| |——| |——| |——| |——| |——| |—————
          0    1    2    3    4    5    6    7    8    9

- **Analysis**:
  - 可以从两边向中间靠拢遍历，两边较小值向中间移动（若值相等可同时向中间移动），并计算并更新最大值。
  - 时间复杂度：$O(n)$
  - 空间复杂度：$O(1)$
- **Solution**:
  ```cpp
  // Runtime: 20 ms, beats 98.50 %
  // Memory Usage: 9.7 MB, beats 99.86 %
  class Solution {
  public:
      int maxArea(vector<int>& height) {
          int l = 0, r = height.size() - 1, ans = 0;
          while (l < r){
              if (height[l] > height[r]) ans = max(ans, height[r] * (r-- - l));
              else ans = max(ans, height[l] * (r - l++));
          }
          return ans;
      }
  };
  ```

---

## 12. Integer to Roman

- **Description**:
  - 给定一个整数，输出其罗马数字形式。
- **Analysis**:
  - 不想动脑系列，直接用两个数组储存对应的数字和罗马字符串，然后贪心算法换完。
  - 优化：用两个整型数组比用`unordered_map`表现要好。
  - 时间复杂度：$O(num)$
  - 空间复杂度：$O(1)$
- **Solution**:
  ```cpp
  // Runtime: 16 ms, beats 93.89 %
  // Memory Usage: 8.3 MB, beats 100.00 %
  class Solution {
  public:
      string intToRoman(int num) {
          const int integers[] = {1000, 900, 500, 400,  100,  90,  50,  40,   10,   9,    5,   4,   1};
          const string romans[] =   {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
          const int n = sizeof(integers)/sizeof(int);
          int i = 0, count;
          string ans = "";
          while (i < n){
              if (num >= integers[i]){
                  count = num / integers[i];
                  num %= integers[i];
                  while (count-- > 0) ans += romans[i];
              }
              ++i;
          }
          return ans;
      }
  };
  ```

---

## 13. Roman to Integer

- **Description**:
  - 给定一个罗马数字字符串，输出其整型形式。
- **Analysis**:
  - 罗马数字一般由大到小，只需留意例如`IX`的特殊形式即可。
  - 时间复杂度：$O(n)$
  - 空间复杂度：$O(1)$
- **Analysis**:
  ```cpp
  // Runtime: 16 ms, beats 99.47 %
  // Memory: 8.3 MB, beats 99.85 %
  class Solution {
  public:
      int romanToInt(string s) {
          int m[256];
          m['I'] = 1, m['V'] = 5, m['X'] = 10, m['L'] = 50, m['C'] = 100, m['D'] = 500, m['M'] = 1000;
          const int n = s.size();
          int i = 0, ans = 0;
          while (i < n - 1){
              if (m[s[i]] < m[s[i+1]]) ans -= m[s[i++]];
              else ans += m[s[i++]];
          }
          return ans + m[s[n-1]];
      }
  };
  ```

---

## 14. Longest Common Prefix

- **Description**:
  - 给定一个字符串数组，输出其共有最大前缀。
- **Analysis**:
  - 可采用纵向扫描、横向扫描、分治法、二叉树查找。这里使用纵向扫描。
  - 时间复杂度：$O(m)$，这里 $m$ 指所有字符串的总长度。
  - 空间复杂度：$O(1)$
- **Analysis**:
  ```cpp
  // Runtime: 8 ms, beats 98.46 %
  // Memory: 8.9 MB MB, beats 99.27 %
  class Solution {
  public:
      string longestCommonPrefix(vector<string>& strs) {
          const int n = strs.size();
          if (n == 0) return "";
          if (n == 1) return strs[0];
          const int m = strs[0].size();
          int j = 0, i;
          string ans = "";
          while (j < m){
              i = 0;
              char tmp = strs[i][j];
              while (++i < n){
                  // if (j > strs[i].size()) return ans;
                  if (tmp != strs[i][j]) return ans;
              }
              ans += tmp;
              ++j;
          }
          return ans;
      }
  };
  ```

---

## 15. 3Sum

- **Description**:
  - 给定一个数组，输出其中三数之和为零的所有组合。
- **Analysis**:
  - 先对数组排序，然后遍历先固定一个数字，然后剩下两数用左右夹逼找出。遍历时只需遍历前$n-2$个元素。
  - 优化：遍历时可只遍历非正数，因为有序序列正数后面必然都是正数，不可能存在之和为零。
  - 时间复杂度：$O(n^{2})$
  - 空间复杂度：$O(1)$
- **Analysis**:
  ```cpp
  // Runtime: 96 ms, beats 98.42 %
  // Memory: 14.7 MB, beats 99.30 %
  class Solution {
  public:
      vector<vector<int>> threeSum(vector<int>& nums) {
          const int n = nums.size();
          vector<vector<int>> ans;
          if (n < 3) return ans;

          sort(nums.begin(), nums.end());

          const int target = 0;
          int i = 0, l, r, sum;

          while (nums[i] <= 0 && i < n - 2){
              if (i > 0 && nums[i] == nums[i-1]) {
                  ++i;
                  continue;
              }
              l = i + 1, r = n - 1;
              while (l < r){
                  sum = nums[i] + nums[l] + nums[r];
                  if (sum < target) ++l;
                  else if (sum > target) --r;
                  else {
                      ans.push_back({nums[i], nums[l], nums[r]});
                      do {++l;} while (nums[l] == nums[l-1] && l < r);
                      do {--r;} while (nums[r] == nums[r+1] && l < r);
                  }
              }
              ++i;
          }
          return ans;

      }
  };
  ```

---

## 16. 3Sum Closest

- **Description**:
  - 给定一个数组和一个目标数`target`，输出其中三数之和最接近于`target`的值。
- **Analysis**:
  - 先对数组排序，然后遍历先固定一个数字，然后剩下两数用左右夹逼找出。遍历时只需遍历前$n-2$个元素。
  - 时间复杂度：$O(n^{2})$
  - 空间复杂度：$O(1)$
- **Analysis**:
  ```cpp
  // Runtime: 8 ms, beats  99.98 %
  // Memory: 8.5 MB, beats 100 %
  class Solution {
  public:
      int threeSumClosest(vector<int>& nums, int target) {
          const int n = nums.size();
          int i = 0, max_offset = INT_MAX, sum, ans, l, r;

          sort(nums.begin(), nums.end());

          while (i < n - 2){
              if (i > 0 && nums[i] == nums[i-1]){
                  ++i;
                  continue;
              }
              l = i + 1, r = n - 1;
              while (l < r){
                  sum = nums[i] + nums[l] + nums[r];
                  if (abs(sum - target) < max_offset) {
                      ans = sum;
                      max_offset = abs(sum - target);
                      if (max_offset == 0) return ans;
                  }
                  if (sum > target) --r;
                  else if (sum < target) ++l;
              }
              ++i;
          }
          return ans;
      }
  };
  ```

---

## 17. Letter Combinations of a Phone Number

- **Description**:

  - 给定一个数字字符串，输出其在座机 9 键对应的字符串输出的所有可能。
  - 如图：

        _______________________________
        |    1    |    2    |    3    |
        |    /    |  a b c  |  d e f  |
        |_________|_________|_________|
        |    4    |    5    |    6    |
        |  g h i  |  j k l  |  m n o  |
        |_________|_________|_________|
        |    7    |    8    |    9    |
        | p q r s |  t u v  | w s y z |
        |_________|_________|_________|
        |    *    |    0    |   #     |
        |    /    |    /    |   /     |
        |_________|_________|_________|

- **Analysis**:
  - 使用 DFS(深度优先搜索)递归解决，每递归一次处理一个数字。
  - 时间复杂度：$O(3^{M} \times 4^{N})$，$M$ 是对应字母数为`3`的数字的个数，$N$ 是对应字母数为`4`的数字的个数。
  - 空间复杂度：$O(3^{M} \times 4^{N})$
- **Analysis**:

  ```cpp
  // Runtime: 4 ms, beats 100.00 %
  // Memory: 8.5 MB, beats 93.83 %
  class Solution {
  public:
      vector<string> letterCombinations(string digits) {
          const int n = digits.size();
          vector<string> ans;
          string cal[8] = {"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};

          if (n > 0) digits2letter(digits, "", ans, cal);

          return ans;

      }
  private:
      void digits2letter(string digits, string tmp, vector<string> &ans, string cal[]) {
          if (digits == "") {
              ans.push_back(tmp);
              return;
          }
          else{
              string s = cal[int(digits[0]) - '2'];
              for (int i = 0; i < s.size(); ++i){
                  string t = tmp;
                  t += s[i];
                  digits2letter(digits.substr(1), t, ans, cal);
              }
          }
      }
  };
  ```

---

## 18. 4Sum

- **Description**:
  - 给定一个数组和一个目标数`target`，输出其中四数之和等于`target`的所有组合。
- **Analysis**:
  - 同`3sum`，先遍历固定两个数，然后剩下两数用左右夹逼找出。
  - 优化：可用`unordered_map`储存两数之和，剩下只需遍历剩余两数，时间复杂度为$O(n^{2})$。但去重比较复杂。
  - 时间复杂度：$O(n^{3})$
  - 空间复杂度：$O(1)$
- **Analysis**:
  ```cpp
  // Runtime: 8 ms, beats 99.87 %
  // Memory: 9.2 MB, beats 76.04 %
  class Solution {
  public:
      vector<vector<int>> fourSum(vector<int>& nums, int target) {
          const int n = nums.size();
          vector<vector<int>> ans;
          if (n < 4) return ans;

          sort(nums.begin(), nums.end());

          int l, r, sum;
          for (int i = 0; i < n - 3; ++i){
              if (i > 0 && nums[i] == nums[i-1]) continue;
              if (nums[i] + nums[n-1] + nums[n-2] + nums[n-3] < target) continue;
              if (nums[i] + nums[i+1] + nums[i+2] + nums[i+3] > target) break;
              for (int j = i + 1; j < n - 2; ++j){
                  if (j > i + 1 && nums[j] == nums[j-1]) continue;
                  l = j + 1;
                  r = n - 1;
                  while (l < r){
                      sum = nums[i] + nums[j] + nums[l] + nums[r];
                      if (sum < target) ++l;
                      else if (sum > target) --r;
                      else {
                          ans.push_back({nums[i], nums[j], nums[l], nums[r]});
                          do {++l;} while (nums[l] == nums[l-1] && l < r);
                          do {--r;} while (nums[r] == nums[r+1] && l < r);
                      }
                  }
              }
          }
          return ans;

      }
  };
  ```

---

## 19. Remove Nth Node From End of List

- **Description**:
  - 给定一个链表和`n`，返回去掉倒数第`n`个结点的链表。
- **Analysis**:
  - 采用双指针`p1`、`p2`，指针`p1`先走`n`步，然后两个指针一起走。当指针`p1`到链表尾，去掉指针`p2`所指元素即可。
  - 时间复杂度：$O(n)$
  - 空间复杂度：$O(1)$
- **Analysis**:

  ```cpp
  // Runtime: 4 ms, beats 99.26 %
  // Memory: 8.5 MB, beats 81.15 %
  class Solution {
  public:
      ListNode* removeNthFromEnd(ListNode* head, int n) {
          ListNode dummy(-1);
          dummy.next = head;
          ListNode *p1 = &dummy, *p2 = p1;
          int i = 0;
          while (i++ < n) p1 = p1->next;

          while (p1->next) {
              p1 = p1->next;
              p2 = p2->next;
          }
          p2 ->next = p2->next->next;

          return dummy.next;
      }
  };
  ```

---

## 20. Valid Parentheses

- **Description**:
  - 实现括号匹配。
- **Analysis**:
  - 简单的栈问题。
  - 时间复杂度：$O(n)$
  - 空间复杂度：$O(n)$
- **Analysis**:
  ```cpp
  // Runtime: 0 ms, beats 100.00 %
  // Memory: 8.7 MB, beats 55.72 %
  class Solution {
  public:
      bool isValid(string s) {
          const int n = s.size();
          if (n < 2) return n&0x01 == 1 ? false : true;

          stack<char> stack;
          int i = 0;
          while (i < n){
              if (s[i] == '[' || s[i] =='{' || s[i] == '(') {
                  stack.push(s[i]);
              }
              else {
                  if (stack.empty()) return false;
                  else if (s[i] == ']' && stack.top() != '[') return false;
                  else if (s[i] == ')' && stack.top() != '(') return false;
                  else if (s[i] == '}' && stack.top() != '{') return false;
                  stack.pop();
              }
              ++i;
          }
          return stack.empty() ? true : false;
      }
  };
  ```

---
