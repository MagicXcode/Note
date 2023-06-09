## 】全局注册组件

>  项目的`main.js函数`中 声明

```JavaScript
import { createApp } from 'vue'
import './style.css'
import App from './App.vue'

import HelloWorld from './components/HelloWorld.vue'

const vm=createApp(App)
    .component('Hello-World',HelloWorld) //对应的html标签是``Hello-World
    .mount('#app')
```

注册以后可以直接再其他页面上以`标签`的形式使用组件 `HelloWorld`,对应的html标签是``'HelloWorld'``

## 样式穿透

```html
<style scoped>
:deep(h1){
    color:red;
}
</style>
```

`:deep(h1)`**:子组件中所有`h1标签`样式都与父组件相同**,应该将:deep写入到父组件中

## 传递`props`

> **子组件**中显式声明 传入的`props` 名称,注意`foo是一个对象`

```vue
<script setup>
const props=defineProps({
    foo: Number //指定类型
})
</script>
<template>
    <div>
        <h1>
            {{props.foo+1}}
        </h1>
    </div>
</template>
```

> **父组件**在组件标签中`传递props`过去
> **非string类型的props值,要使用v-bind命令传过去****

```vue
<hello-world :foo=347 />
```

>  显式指定props必须传递

```html
<script setup>
defineProps({
    msg: {
        type: String,
        required: true, //父组件中一定需要传递
         default: 'hello' //设定默认值
    }
})
</script>
```

## 动态绑定props,响应式

```vue
<script setup>
import HelloWorld from "./components/HelloWorld.vue";
import {ref} from 'vue'
let a=ref({'b':34545567,'c':'rtewertwer'})
</script>

<template>
    <div>
        <el-button type="success">success</el-button>
        <h1>fasdfasgas</h1>
        <hello-world :foo='a.b' :title="a.c"/>
    </div>
</template>
<style scoped>
:deep(h1) {
    color: red
}
</style>
```

**修改父组件中`a.b的值`,子组件中`<h1>{{props.foo}}</h1>` 的值会响应改变**

## element plus组件 (标签都是组件)

在单文件组件中直接使用全局 CSS 样式规则，即不加 `scoped` 属性的样式，可以对组件产生未知的影响。因为这样的样式会影响到所有同名标签，不论它们在哪个 Vue 组件内部，这就可能导致样式冲突和覆盖等问题，影响应用程序的正确性和可维护性。

因此，我们通常不建议在 Element Plus 中使用全局 CSS 样式来修改组件的外观，而是建议使用局部作用域 CSS 或自定义类名来实现组件定制化的样式需求。

如果您一定需要在全局 CSS 中直接修改 Element Plus 组件的样式，可以使用 `!important` 关键字来强制应用样式规则，例如：

```css
.el-container {
  color: red !important;
}
```

上面的代码在全局 CSS 样式表中直接选中了 `<el-container>` 组件，并且使用 `!important` 声明了 `color` 样式属性的优先级，这样就可以将颜色强制设置为红色。然而，这种方法通常不够灵活和可维护，因此请谨慎使用。


## 组件之间的数据传递

### 父组件向子组件传递值`v-bind(单向数据传递)`

> **使用v-bind定义一个属性传值,到子组件**

```html
<script setup>
import { ref, computed, defineProps, defineComponent } from 'vue'
import wind from './components/windCard.vue'
defineComponent({
    name: 'app',
})
let va=ref(0)
function add(){
   va.value++;
}
	


</script>

<template>
    <div class="div1">
        <el-text >  count:  {{ va }}</el-text>
        <el-button @click="add">+1</el-button>
        <hr/>
        <wind v-bind:number="va" ></wind>     //在这里,重点v-bind命令
    </div>
</template>
```

> 子组件通过宏定义``defineProps`创建一个json对象,显式声明接到的子组件信号

```html
<script setup>
import { ref, computed, defineProps, defineComponent } from 'vue'
defineComponent({
    name: 'MyComponent',
})
let va = ref(0)
const emit=defineEmits(['inFocus', 'update:number'])


const props=defineProps({
    number: {
        type: Number,
        default: 0
    }
})

function add() {
 emit('update:number',props.number+1)
}
</script>

<template>
    <div>
        <el-text > count:{{ props.number}}</el-text> //重点:一定要通过这种方式来访问接收到属性值,json对象
        <el-button @click="add">+1</el-button>
    </div>
</template>
```

### 父组件与子组件值双向传递(``v-model`)

> 父组件`v-model`传递一个属性给子组件

```html
<script setup>
import { ref, computed, defineProps, defineComponent } from 'vue'
import wind from './components/windCard.vue'
defineComponent({
    name: 'app',
})
let va=ref(0)
function add(){
   va.value++;
}

</script>

<template>
    <div class="div1">
        <el-text >  count:  {{ va }}</el-text>
        <el-button @click="add">+1</el-button>
        <hr/>
        <wind v-model:number="va" ></wind>
    </div>
</template>
```

> `重点:子组件 接受 父组件中的数据,并通过 (自定义事件,再触发) 返回子组件中  更新后的属性值  给父组件`

1. 先定义:自定义事件,更新父组件中的自定义事件名字模板`'update:属性名字'`
   
   ```html
   const emit=defineEmits(['inFocus', 'update:number']) //自定义事件
   ```

2. 函数中触发事件,`emit(事件名称:string,新的属性值)`

```html
<script setup>
import { ref, computed, defineProps, defineComponent } from 'vue'
defineComponent({
    name: 'MyComponent',
})
let va = ref(0)
const emit=defineEmits(['inFocus', 'update:number']) //自定义事件


const props=defineProps({
    number: {
        type: Number,
        default: 0
    }
})

function add() {
 emit('update:number',props.number+1)  //函数中触发自定义事件
}
</script>
```

**注意点:子组件中不可以` 直接操作 `  来自父组件中   `传递的属性`,`一定不可以`**

```html
<template>
    <div>
        <el-text > count:{{ props.number}}</el-text> //json对象的访问
        <el-button @click="add">+1</el-button> 
    </div>
</template>
```

### 注意事项

#### - 组合式api中需要在子组件中  显式指定  组件接收到来自父组件的值(是一个json对象))

#### - 子组件中不能 直接更改   父组件中传递的属性

#### - 更新父组件传递的属性,采用的触发自定义格式相同

`emit('update:number',props.number+1)`: **emit('update:名字',新的属性值)**

## miit实现兄弟组件传递数值

综上所述，无论是多个子组件还是单个组件中，都可以使用 `mitt` 库来实现组件之间的通信。如果在多个子组件之间传递数据，则需要在它们的父组件中创建一个单一的事件总线对象。

> 数据总线一定要相同,才能监听同一个事件

### 导出一份`mitt实例`

```JavaScript
import mitt from 'mitt'
const bus = mitt()
export default bus
```

### 全局导入[`main.js中`]

```JavaScript
import mitt from 'mitt'
 const bus = mitt()
 app.config.globalProperties.$EventBus = bus
```

### 在组件中导入

#### 兄弟组件A监听事件

```html
<template>
  <div>
    <p>{{ receivedMessage }}</p>
  </div>
</template>
<script setup>
import { ref, onMounted } from 'vue'
import bus from '../bus'



// 监听事件
let receivedMessage=ref('')
bus.on('jisho', (message) => {
  receivedMessage.value = message
})
</script>
```

#### 兄弟组件B中触发事件

```html
<template>
  <el-input v-model="input">
  </el-input>
  <el-button @click="fa">send</el-button>
</template>
<script setup>
import { ref, onMounted } from 'vue'
import  bus  from '../bus';




let input=ref('fasdfas')
function fa(){

    //触发事件
    bus.emit('jisho', input.value)
}
</script>
```

## 组件向后代传递值

### 根组件`provide`

> `provide` 注意传值,不需要`.value`

```html
<script setup >
import { ref,defineComponent, provide } from 'vue'
import {
  Check,
  Delete,
  Edit,
  Message,
  Search,
  Star,
  Plus
} from '@element-plus/icons-vue'

import Com from './comp.vue'

defineComponent({
  name: 'comp',
})

let input = ref(0)
function add()
{
  input.value++
}

provide('count', input) //这个函数
</script>


<template>
  <div>
    <div>
      <div>发送 count:{{ input }}</div>
      <el-button @click="add">+1</el-button>
    </div>
    <Com />
  </div>
</template>
```

### 子代组件`inject`

```html
<template>
    <div>
        <p>count:{{zhi}}</p>
    </div>
</template>

<script setup>
import { ref, inject } from 'vue'
let zhi = inject('count') //这里
</script>
```

### 如果传递的数组

```html
<!-- ChildComponent.vue -->

<template>
  <ul>
    <li v-for="item in items" :key="item">{{ item }}</li>
  </ul>
</template>

<script>
  import { inject, reactive } from 'vue';

  export default {
    setup() {
      const array = inject('array');
      const items = reactive({ value: array }); // 将原始数据转换为响应式数据

      return { items };
    }
  };
</script>
```

## `vuex`多组件状态管理

