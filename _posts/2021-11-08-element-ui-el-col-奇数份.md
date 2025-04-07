---
title: element-ui el-col 奇数份
categories:
  - 技术
tags:
  - Element
  - Layout 布局
date: 2021-11-08 09:35:51
---

---

Element Layout 布局设置奇数份分栏：

```javascript 1.8
<el-col :xs="12" :sm="12" :lg="lgSpan" class="card-panel-col" v-for="item in panelData">
    <div class="card-panel">
        <div class="num">{{item.num}}</div>
        <div class="name">{{item.name}}</div>
    </div>
</el-col>

<script>
props: {
    lgSpan: {
        type: Object,
        default: function() {
          return { span: '4-8' }
        }
    }
}
</script>

<style lang="scss" scoped>
    .el-col-lg-4-8 {
        width: 20%;
    }
</style>
```

[参考文档](https://blog.csdn.net/qq_36262395/article/details/114674476)
