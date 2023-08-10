# Vue3

## 前提准备

- 安装nodejs

  [nodejs下载地址](https://nodejs.org/en)

- 安装 `vue` 脚手架 `vue-cli`

  ```
  npm install -g @vue/cli
  ```

- 使用脚手架创建 `vue`项目

  ```
  vue create <project-name>
  
  # vue create personal_page
  ```

- 进入项目目录安装依赖、启动项目

  ```
  cd personal_page
  npm install
  npm run serve
  ```



## 组合API：setup

### 简单使用

`setup()` 钩子是在组件中使用组合式 API 的入口，通常只在以下情况下使用：

> - 需要在非单文件组件中使用组合式 API 时
> - 需要在基于选项式 API 的组件中集成基于组合式 API 的代码时

```
对于结合单文件组件使用的组合式 API，推荐通过 <script setup> 以获得更加简洁及符合人体工程学的语法。
```

```vue
<template>
  <div>{{ name }}</div>
  <div v-for="(item, index) in arr">
    {{ index }} {{ item }}
  </div>
  <div>
    <button @click="btn">点击</button>
  </div>
</template>

<script>
export default {
  // 定义的变量、常量和其他方法（函数）都要放在setup函数中
  setup() {
    console.log('10010')
    const name = '张三'
    const arr = [1, 2, 3, 4, 5]
    console.log(name)

    // 函数的第一种用法
    // function btn() {
    //   console.log('+++')
    // }

    // 函数的第二种用法
    const btn = () => {
      console.log('12121212121')
    }

    // 定义以上变量、常量以及方法后，最后需要返回
    return { name, arr, btn }
  }
}
</script>

<style scoped></style>

```



## 组合API：ref

​	接受一个内部值，返回一个响应式的、可更改的 ref 对象，此对象只有一个指向其内部值的属性 `.value`。

- 可操作基本数据类型、也可操作复杂数据类型（对象）



```vue
<template>
  <div>
    <h1>姓名：{{ name }}</h1>
    <h1>年龄：{{ age }}</h1>
    <h1>公司：{{ company }}</h1>
    <h1>产品：{{ obj.car }}</h1>
    <span v-for="(item, index) in arr">{{ item.zican }}</span>
    <hr>
    <button @click="changeName">点击更新姓名</button>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  // 定义的变量、常量和其他方法（函数）都要放在setup函数中
  setup() {
    const name = ref('张三')		// 要响应式改变哪个值，就用ref包装哪个
    const age = 23
    const company = 'NIO'

    // 对象类型
    const obj = ref({ car: 'es5', phone: 'NIO-Phone' })

    // 数组类型
    const arr = ref([
      {
        zican: 1000
      }
    ])

    function changeName() {
      name.value = '小巴'
      obj.value.car = '大Q吧'
      arr.value[0].zican = '1090000'
    }

    return { name, age, company, obj, changeName, arr }
  }
}
</script>

<style scoped></style>
```

