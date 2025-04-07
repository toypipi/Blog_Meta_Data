---
title: Range Extraction
categories:
  - 技术
tags:
  - 每日一练
date: 2023-10-30 15:49:38
---

---

# DESCRIPTION

# SOLUTION

- 方法一：

```js
function solution(list) {
  // 检查是否为空
  if (list.length === 0) {
    return "";
  }
  // 定义 result 保存最终结果的数组
  let result = [];
  // 记录连续数字开始值
  let start = list[0];
  // 记录连续数字结束值
  let end = list[0];
  // 从第二个元素开始遍历 list 中的元素
  for (let num of list.slice(1)) {
    // 如果 num 等于 end + 1,则 num 与前一个元素连续，将 end 更新为 num
    if (num === end + 1) {
      end = num;
    } else {
      // 处理连续的 3 个数字或以上，如果 end - start >= 2,则将 `${start}-${end}` 添加到 result 中
      if (end - start >= 2) {
        result.push(`${start}-${end}`);
      } else {
        // 连个数字以下
        for (let j = start; j <= end; j++) {
          result.push(j.toString());
        }
      }
      // 更新 start 和 end 值
      start = end = num;
    }
  }

  // 处理循环结束的最后一组连续数字
  if (end - start >= 2) {
    result.push(`${start}-${end}`);
  } else {
    for (let j = start; j <= end; j++) {
      result.push(j.toString());
    }
  }

  return result.join(",");
}
```

<!-- more -->

- 方法二：

```js
function solution(list) {
  for (var i = 0; i < list.length; i++) {
    var j = i;
    while (list[j] - list[j + 1] == -1) j++;
    if (j != i && j - i > 1) list.splice(i, j - i + 1, list[i] + "-" + list[j]);
  }
  return list.join();
}
```

# Reference

[Range Extraction](https://www.codewars.com/kata/51ba717bb08c1cd60f00002f/solutions/javascript)
