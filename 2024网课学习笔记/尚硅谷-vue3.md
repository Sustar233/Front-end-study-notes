创建vue3工程
npm create vue@latest

webpack先分析处理所有路由，再bundle，server ready
vite先server ready 根据需求分析路由

setup函数中this是undefined，vue3中开始弱化this了

ref底层使用的是reactive
ref和reactive区别

- ref创建的变量必须使用 .value（可以用volar插件自动添加.value
- reactive重新分配一个新对象，会失去响应式（可以使用Object.assign去整体替换

推荐
响应式对象层级较深，表单相关，推荐使用reactive，其余用ref

let person = reactive({
name: 'qwq',
age: 18
})
let {name, age} = toRefs(person)
let nl = toRef(person, 'age')
要解构person中的key-value并保留响应式，需要加toRefs

computed 计算属性，可以缓存结果，默认只读（不可直接修改）

watch监视ref数据时，默认监视的地址值，如果想监视内部属性变化，需加入 {deep: true}
监视reactive数据，默认开启深度监视
监视ref或reactive定义的对象类型数据中的某个属性时（建议都函数）

- 若该属性值不是对象类型，需要写成函数形式
- 若该属性值是对象类型，可直接编，也可写成函数（可能需要开启deep）

监视多个数据时，放入一个数组，数组中的数据遵守前面的规则

watchEffect 不需要明确指出监控的数据，响应式地追踪其依赖，在依赖更改时重新再次执行该函数

ref 可写入html标签用来获取dom元素
<h2 ref="title2"><h2/>
let title2 = ref()
console.log(title2.value)
ref加到子组件标签时，默认无法获取子组件数据（出于保护）
可以在子组件添加 defineExpose({a,b,c})来暴露数据

ts示例
```typescript
export interface HelloInter {
    id: string,
    name: string,
    age: number
}
```

```vue
import { type HelloInter } from '@/types'
let person: HelloInter = { id: 'abc', name: 'zhang', age: 555 }

let personList: Array<HelloInter> = [
{ id: 'abc', name: 'zhang', age: 555 },
{ id: 'abc', name: 'zhang', age: 555 }
]
```

也可以
export type Persons = Array<HelloInter>
export type Persons = HelloInter[]
两种写法均可
let personList: Persons = [...]


父传子
<Person a="haha">
以下两种都能接收

- difineProps(['a'])
- let x = defineProps(['a'])

console.log(x.a)

接收时限制类型
defineProps<{list: Persons}>()
list可不传
defineProps<{list?: Persons}>()
接收list + 限制类型 + 限制必要性 + 指定默认值
withDefaults(defineProps<list?:Persons>(),{
list: [{id: '111', name: 'qwq', age: 19}]
})

