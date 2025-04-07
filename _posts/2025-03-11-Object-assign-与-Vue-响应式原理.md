---
title: Object.assign 与 Vue 响应式原理
categories:
  - 技术
tags:
  - Object.assign
  - Vue 响应式原理
date: 2025-03-11 16:36:08
---

---

# Vue 响应式系统的工作原理

Vue  在初始化时会将  data  中的属性转换为  getter/setter.

```Vue
export default {
  data() {
    return {
      // Vue 会递归遍历这个对象
      user: {
        name: "John", // 每个属性都会被转换为 getter/setter
        age: 25
      }
    }
  }
}
```

<!-- more -->

当 Vue 实例创建时：

- Vue 会遍历 data  中的所有属性
- 使用 Object.defineProperty(Vue 2) 或 Proxy(Vue 3) 将这些属性转换为 getter/setter
- 这些 getter/setter 允许 Vue 追踪依赖和更新视图

# 不同赋值方式的影响

```Vue
//  方式 1：直接替换整个对象  -  破坏响应式
this.user = { name: 'Jane', age: 30 };  // ❌  创建了新对象，丢失了响应式

//  方式 2：单独更新属性  -  保持响应式
this.user.name = 'Jane';  // ✅  通过  setter  更新，保持响应式
this.user.age = 30;

//  方式 3：使用  Object.assign -  保持响应式
Object.assign(this.user, { name: 'Jane', age: 30 });  // ✅  通过  setter  更新，保持响应式
```

# Object.assign 如何保持响应式

```Vue
// 假设有这样一个组件
export default {
  data() {
    return {
      networkBond: {
        isAuto: true,
        ip: '',
        mask: 24
      }
    }
  },
  methods: {
    updateNetwork(newData) {
      // Object.assign 会遍历 newData 的所有属性
      // 通过现有对象的 setter 方法更新每个属性
      Object.assign(this.networkBond, newData);
      // 等效于：
      // this.networkBond.isAuto = newData.isAuto;
      // this.networkBond.ip = newData.ip;
      // this.networkBond.mask = newData.mask;
    }
  }
}
```

# 对比不同方式的内部工作流程

```Vue
// 示例1：直接对象赋值
this.networkBond = newData;

/* 内部发生了什么：
1. 创建新对象 newData
2. 将 this.networkBond 指向新对象
3. 原有的响应式对象被丢弃
4. 新对象没有 getter/setter
*/

// 示例2：Object.assign
Object.assign(this.networkBond, newData);

/* 内部发生了什么：
1. 保持原有响应式对象的引用
2. 通过现有的 getter/setter 更新每个属性
3. 响应式系统可以检测到变化
4. 视图会相应更新
*/
```

# 实际应用示例

```Vue
export default {
  data() {
    return {
      form: {
        name: '',
        email: '',
        age: 0
      }
    }
  },
  methods: {
    // 不好的方式
    updateFormBad(data) {
      this.form = data; // ❌ 破坏响应式
    },

    // 好的方式
    updateFormGood(data) {
      Object.assign(this.form, data); // ✅ 保持响应式
    },

    // 如果需要重置表单
    resetForm() {
      Object.assign(this.form, {
        name: '',
        email: '',
        age: 0
      });
    }
  }
}
```

# 注意事项

```Vue
// 1. 添加新属性仍需要 Vue.set
export default {
  methods: {
    addNewProperty() {
      // 如果是新属性，Object.assign 也不能使其具有响应式
      Object.assign(this.form, { newProp: 'value' }); // ❌ 新属性不是响应式的

      // 正确方式
      this.$set(this.form, 'newProp', 'value'); // ✅
    }
  }
}

// 2. 深层对象更新
const deepObject = {
  nested: {
    very: {
      deep: 'value'
    }
  }
};

// Object.assign 是浅拷贝，深层对象需要特殊处理
// 可以考虑使用深度合并库或递归合并
```

理解这些原理后，你就能更好地在 Vue 项目中使用  Object.assign  来维护数据的响应式特性了。
