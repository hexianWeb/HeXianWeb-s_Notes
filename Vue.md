# Vue入门笔记

> 作者：何贤

##  初始化Vue



```html
  <body>
    <div id="root">
      <h1>你好！{{name}}</h1>
    </div>
    <script>
      Vue.config.productionTip = false;
      //   创建Vue实例
      const app = new Vue({
        el: "#root", // el用于指定当前的Vue实例为哪个容器服务
        data: {
          name: "何贤",
        },
      });
    </script>
  </body>
```



- 注意容器和实例只能一对一
- {{}}里可以放置表达式或者代码
- 真实开发中只能有一个Vue实例

## 模板语法

###  模板语法

| 名称     | 内容     | 作用                                     |
| -------- | -------- | ---------------------------------------- |
| 数据绑定 | {{data}} | 在绑定处为对应数据上的property值进行更新 |
|          |          |                                          |



### 指令解析

> 一些常用指令



| 指令               | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| v-once指令         | 仅仅会更新一次插值内容                                       |
| v-html指令(不推荐) | 将数据绑定内容解析为HTML XSS攻击 永远不要相信用户的输入      |
| v-bind 指令        | 可以作用在HTML attribute上，动态的为一个属性绑定值 *tips:单向数据绑定* |
| v-mode:value指令   | 双向数据绑定 仅对有输入功能的标签有用                        |
| v-if指令           | 条件渲染 懒加载                                              |
| v-show指令         | 条件渲染 （视觉消失） 节点任然存在                           |
| v-cloak指令        | 本质是一个特殊属性 Vue实例创建完毕后自动删除v-cloak属性 使用css配合使用避免出现渲染问题 |
| v-once指令         | v-once节点在初始动态渲染后 就视为静态内容 后续数据改变不会引起v-once所在的结构的更新 |
| v-pre指令          | 跳过解析编译过程 快速渲染静态数据                            |
| 自定义指令         | 用于定向解析标签                                             |



#### 自定义指令解析

> 在Vue实例下的directives下定义自定义指令

自定义指令拥有两个参数（element,binding），

```js
       big(element, binding) {
          // 调用该指令模板的元素
          console.dir(element);
          //你可能需要操作的对象属性的全部枚举
          console.log(binding);
          console.log("big", this); //注意此处的this是window
          // console.log('big')
          element.innerText = binding.value * 10;
        },
        fbind: {
          //指令与元素成功绑定时（一上来）
          bind(element, binding) {
            element.value = binding.value;
          },
          //指令所在元素被插入页面时
          inserted(element, binding) {
            element.focus();
          },
          //指令所在的模板被重新解析时
          update(element, binding) {
            element.value = binding.value;
          },
        },
      },
```



**element参数**

> element参数是你调用该指令模板时的元素

如

```html
<span v-big= "n"></span>
```

此时element会给予span标签。

**binding参数**

> binding会给于你你可能要操纵的元素值

```bash
{name: 'big', 
rawName: 'v-big',
value: 3, 
expression: 'n',
modifiers: {…}, …}
def: {bind: ƒ, update: ƒ}
expression: "n"
modifiers: {}
name: "big"
oldArg: undefined
oldValue: 2
rawName: "v-big"
value: 3[[Prototype]]: Object

```



### data的写入

**一：对象式**

```js
   // data第一种写法 ：对象式
        // data: {
        //   name: "你好",
        //   msg: "信息",
        //   link: {
        //     url: "#",
        //   },
        // },
```

**二：函数式** 推荐使用

> 不要用箭头函数 this会指向windows的

```js
  // data第二种写法 ：函数式 推荐使用
        data: function () {
          return {
            name: "双向数据绑定",
            msg: "信息",
            link: {
              url: "#",
            },
          };
        },
```

*tips:现在可能会觉得有点麻烦，但这是为组件化变成服务的*



##  MVVM

```bash
			MVVM模型
						1. M：模型(Model) ：data中的数据
						2. V：视图(View) ：模板代码
						3. VM：视图模型(ViewModel)：Vue实例
```



##  属性该如何添加

> 属性需要被的限制，用户不应该被赋予太高级的权限



#### 正常定义一个对象时

```js
			let person = {
				name:'张三',
				sex:'男',
				}
```

这里面的属性是可以被修改的，**我们可以通过Object.defineProperty来设置不能被修改的属性**

例如：

```js
      Object.defineProperty(person, "age", {
        value: 18,
        }
```



但是此时的属性**不可枚举**

> ​      console.log(Object.keys(person));

![](https://i.imgur.com/kW2ZKF8.png)

且**不可以被修改**



当然我们可以手动设置可以删除

```js
      Object.defineProperty(person, "age", {
        value: 18,
        enumerable:true, //控制属性是否可以枚举，默认值是false
        writable:true, //控制属性是否可以被修改，默认值是false
        configurable:true //控制属性是否可以被删除，默认值是false
        }
```



#### API Object.defineProperty中的get和set函数

> 当然，如果只是展示数据，止步于此应该足够了，但有时候我们希望在属性被展示或修改时可以获取到对应的事件。。。

例如：

```js
     Object.defineProperty(person, "age", {
        //当有人读取person的age属性时，get函数(getter)就会被调用，且返回值就是age的值
        get() {
          console.log("有人读取age属性了");
          return number;
        },

        //当有人修改person的age属性时，set函数(setter)就会被调用，且会收到修改的具体值
        set(value){
        	console.log('有人修改了age属性，且值是',value)
        	number = value
        }
      });
```



效果：

![](https://i.imgur.com/shiHQ8l.png)

#### 

#### Vue中的数据代理

> 新建一个这样的Vue实例

```js
    const vm = new Vue({
      el: "#root",
      data: {
        name: "ECJTU",
        address: "双港东大街",
      },
    });
```



**我们在控制台输出后可以看到每个data里的属性都自创建了get与set方法**

![](https://i.imgur.com/Ss2ZU9d.png)





![](https://i.imgur.com/rtfnHam.png)



- 我们编码了一个含有数据的Vue的实例对象vm
- 随后vm就会把我们提供的数据给与_data.
- 随后调用get方法读取_data里的key并且创建data（绿）
- 随后若是有人更改了data（绿）的值，便会触发set方法更改_data里的数据

**底层原理是基于上面刚刚提到的API**



##   事件处理

> 事件的基本使用

- 使用v-on或者 @做事件绑定
- 事件的回调函数需要在methods对象中配置，最终也会出现在Vue的实例化对象上
- methods里定义的回调函数严禁箭头函数
- methods中配置的函数都被Vue所管理 this指向实例对象
- 回调函数中的参数最好添加$event，效果一致但是后者可以传递参数

```vue
		<div id="root">
			<h2>欢迎来到{{name}}学习</h2>
			<!-- <button v-on:click="showInfo">点我提示信息</button> -->
			<button @click="showInfo1">点我提示信息1（不传参）</button>
			<button @click="showInfo2($event,66)">点我提示信息2（传参）</button>
		</div>

		<script type="text/javascript">
		Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

		const vm = new Vue({
			el:'#root',
			data:{
				name:'何贤',
			},
			methods:{
				showInfo1(event){
					// console.log(event.target.innerText)
					// console.log(this) //此处的this是vm
					alert('同学你好！')
				},
				showInfo2(event,number){
					console.log(event,number)
					// console.log(event.target.innerText)
					// console.log(this) //此处的this是vm
					alert('同学你好！！')
				}
			}
		})
	</script>
```



### 事件修饰符

#### 事件冒泡

> tips:**当一个事件发生在一个元素上，它会首先运行在该元素上的处理程序，然后运行其父元素上的处理程序，然后一直向上到其他祖先上的处理程序。**

![](https://i.imgur.com/i1ikYu9.png)

| 关键字  | 作用                                     |
| ------- | ---------------------------------------- |
| prevent | 阻止默认事件                             |
| stop    | 阻止事件冒泡                             |
| once    | 事件只触发一次                           |
| capture | 事件的捕获方式                           |
| self    | 只有当前event,target操作的元素才触发事件 |
| passive | 事件默认行为立即执行，无需等待回调执行   |



### 常用的按键别名

```html
      <input
        type="text"
        placeholder="按下回车提示输入"
        @keydown.enter="事件名"
      />
```



|      |        |                          |
| ---- | ------ | ------------------------ |
| 回车 | enter  |                          |
| 删除 | delete |                          |
| 退出 | esc    |                          |
| 空格 | space  |                          |
| 上   | up     |                          |
| 下   | down   |                          |
| 左   | left   |                          |
| 右   | right  |                          |
| 换行 | tab    | 特殊 需要配合keydown使用 |



**tips：系统修饰键（用法特殊）：ctrl、alt、shift、meta**

1. 配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发。
2. 配合keydown使用：正常触发事件



### 计算属性

> **计算属性将基于它们的响应依赖关系缓存**



#### 例子：完整写法

```js
// ...
// this的指向已经在底层被改写为指向Vue当前所创建的实例对象 但请注意 不能写成箭头函数
computed: {
    //类似于Object.defineProperty中的get与set方法
  fullName: {
    // getter	
    get() {
      return this.firstName + ' ' + this.lastName
    },
    // setter	很遗憾，这种方法数据双向绑定要比v-model来的麻烦的多	，拿到数据后需要对数据进行逆向解析
        
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...

```



 

*tips:我们当然可以使用method来代替这种“麻烦的方法”，但无法取代computed带来的缓存机制的便利*



现象一个场景，中我们返回给模版引擎 一个“全名”，如果用method方法来求得这个全名，

```js
// 在组件中
methods: {
  calculateBooksMessage() {
    return this.firstName + ' ' + this.lastname
  }
}
```



当我们需要多次展示时



例如：

```html
			全名：<span>{{fullName}}</span>			全名：<span>{{fullName}}</span>			全名：<span>{{fullName}}</span>			全名：<span>{{fullName}}</span>
```

*tips:不要加括号，仔细想想，computed的本质是将数据计算完成之后赋予vm的_data,*

*详情请看**Vue的数据代理***



|          | method | computed    |
| -------- | ------ | ----------- |
| 调用次数 | 4次    | 1次（缓存） |



*当我们需要反复去展示和维护的是一个拥有大量数据的list时，我们不希望反复去遍历list的所有数据，所以缓存机制的合理利用非常重要*



#### 例子：简写写法

> 当数据不需要双向绑定时（正经人双向绑定谁用computed？）

```js
 computed: {
    // a computed getter
    publishedBooksMessage() {
      // `this` points to the vm instance
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
```

###  监听器

> 虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。

- 当被监视属性变化时，回调函数自动调用，进行相关操作
- 监视的属性必须存在，才能进行监视！！
- 监视的两种写法
  - 通过 new Vue时传入watch配置
  - 通过$watch监视

```js
 watch:{
		isHot:{
			immediate:true, //初始化时让handler调用一下
			//handler什么时候调用？当isHot发生改变时。
			handler(newValue,oldValue){
			console.log('isHot被修改了',newValue,oldValue)
					}
				}
			} 
```

```js
		vm.$watch('isHot',{
			immediate:true, //初始化时让handler调用一下
			//handler什么时候调用？当isHot发生改变时。
			handler(newValue,oldValue){
				console.log('isHot被修改了',newValue,oldValue)
			}
		})
```

#### 深度监测

						(1).Vue中的watch默认不监测对象内部值的改变（一层）。
						(2).配置deep:true可以监测对象内部值改变（多层）。

> 有时候，我们想去监测具有多级结构的数据的变化 

```js
				//监视多级结构中所有属性的变化
				"要检测的属性名":{
					deep:true,
					handler(){
                        //处理函数
						console.log('改变了')
					}
				}
```



*tips:监测多级结构数据中某一个数据的属性是否变化*

```js
				//监视多级结构中某个属性的变化
				'属性.XX':{
					handler(){
						console.log('XX被改变了')
					}
				} 
```

##  样式绑定

>在Vue里 动态的切换样式不再需要操作DOM

**HTML**

```html
<div id="root">
			<!-- 绑定class样式--字符串写法，适用于：样式的类名不确定，需要动态指定 -->
			<div class="basic" :class="mood" @click="changeMood">{{name}}</div> <br/><br/>

			<!-- 绑定class样式--数组写法，适用于：要绑定的样式个数不确定、名字也不确定 -->
			<div class="basic" :class="classArr">{{name}}</div> <br/><br/>

			<!-- 绑定class样式--对象写法，适用于：要绑定的样式个数确定、名字也确定，但要动态决定用不用 -->
			<div class="basic" :class="classObj">{{name}}</div> <br/><br/>

			<!-- 绑定style样式--对象写法 -->
			<div class="basic" :style="styleObj">{{name}}</div> <br/><br/>
			<!-- 绑定style样式--数组写法 -->
			<div class="basic" :style="styleArr">{{name}}</div>
		</div>
```



**Script**

```js
	<script type="text/javascript">
		Vue.config.productionTip = false
		
		const vm = new Vue({
			el:'#root',
			data:{
				name:'尚硅谷',
				mood:'normal',
				classArr:['atguigu1','atguigu2','atguigu3'],
				classObj:{
					atguigu1:false,
					atguigu2:false,
				},
				styleObj:{
					fontSize: '40px',
					color:'red',
				},
				styleObj2:{
					backgroundColor:'orange'
				},
				styleArr:[
					{
						fontSize: '40px',
						color:'blue',
					},
					{
						backgroundColor:'gray'
					}
				]
			},
			methods: {
				changeMood(){
					const arr = ['happy','sad','normal']
					const index = Math.floor(Math.random()*3)
					this.mood = arr[index]
				}
			},
		}) 
```

-------------





##  条件渲染

> 有时候我们会希望操纵页面元素

**HTML**

```html
 	  <h2>当前的n值是:{{n}}</h2>
      <button @click="n++">点我n+1</button>      
	  <div v-if="n === 1">Angular</div>
      <div v-else-if="n === 2">React</div>
      <div v-else-if="n === 3">Vue</div>
      <div v-else>哈哈</div>
```

**JS**

```js
    const vm = new Vue({
      el: "#root",
      data: {
        name: "尚硅谷",
        n: 0,
      },
    });
```



### `v-if` vs `v-show`

```
`v-if` 是“真正”的条件渲染，因为它会确保在切换过程中，条件块内的事件监听器和子组件适当地被销毁和重建。

`v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。
```

### 一个判断 显示多个元素

```html
     		<template v-if="n === 1">
				<h2>你好</h2>
				<h2>尚硅谷</h2>
				<h2>北京</h2>
			</template>
```



## 列表渲染

> 列表渲染与展示 前端码农的饭



### 基本用法

**指令：V-for**

1. 作用：用于展示列表数据
2. 语法：v-for:"(item,index) in <data>"  :key="唯一标示的属性"

**HTML**

```html
			<ul>
				<!-- key 应该被赋予唯一标示的内容，不然可能会有错误更新的可能 -->
				<!-- index 作为v-for的第二个参数 表达 的是当前列表的索引值  -->
				<li v-for="(p,index) of persons" :key="index">
					{{p.name}}-{{p.age}}
				</li>
			</ul>
```

**JS**

```json

					persons:[
						{id:'001',name:'张三',age:18},
						{id:'002',name:'李四',age:19},
						{id:'003',name:'王五',age:20}
					],
```



![](https://i.imgur.com/4O074wV.png)



![](https://i.imgur.com/gojjrgL.png)



### 虚拟DOM 和 diff 算法

> 日了狗了，谁搞的地府算法？？！！

#### 什么是虚拟DOM？

> Virtual dom 即虚拟DOM节点。通过JS的Object对象虚拟DOM节点 在通过特定的render方法渲染成DOM节点

![](https://segmentfault.com/img/remote/1460000022277669)

从上图可见，真实的 DOM 元素是非常庞大的，因为浏览器的标准就把 DOM 设计的非常复杂。当我们频繁的去做 DOM 更新，会产生一定的性能问题。而 Virtual DOM 就是用一个原生的 JS 对象去描述一个 DOM 节点，所以它比创建一个 DOM 的代价要小很多。

> 真实DOM转换成虚拟DOM

虚拟DOM就是一个普通的JavaScript对象，包含了`tag`、`props`、`children`三个属性。

```angelscript
<div id="app">
  <p class="text">何贤</p>
</div>
```

上面的HTML元素转换为虚拟DOM：

```css
{
  tag: 'div',
  props: {
    id: 'app'
  },
  chidren: [
    {
      tag: 'p',
      props: {
        className: 'text'
      },
      chidren: [
        '何贤'
      ]
    }
  ]
}
```



#### 这不是脱裤子放屁？diff算法登场

> 确实不是脱裤子放屁

这就是咱们平常说的`新旧两个虚拟DOM`，这个时候的`新虚拟DOM`是数据的最新状态，那么我们直接拿`新虚拟DOM`去渲染成`真实DOM`的话，效率真的会比直接操作真实DOM高吗？那肯定是不会的，看下图：

![截屏2021-08-07 下午10.24.17.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0089a781a80244308472447b86cad21e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

由上图，一看便知，肯定是第2种方式比较快，因为第1种方式中间还夹着一个`虚拟DOM`的步骤，所以**虚拟DOM比真实DOM快**这句话其实是错的，或者说是不严谨的。那正确的说法是什么呢？**虚拟DOM算法操作真实DOM，性能高于直接操作真实DOM**，`虚拟DOM`和`虚拟DOM算法`是两种概念。`虚拟DOM算法 = 虚拟DOM + Diff算法`

------

上面咱们说了`虚拟DOM`，也知道了只有`虚拟DOM + Diff算法`才能真正的提高性能，那讲完`虚拟DOM`，我们再来讲讲`Diff算法`吧，还是上面的例子(这张图被压缩的有点小，大家可以打开看，比较清晰)：

![截屏2021-08-07 下午10.59.31.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1c57a7b4e91a4a359474fb4c281f6d8e~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

上图中，其实只有一个li标签修改了文本，其他都是不变的，所以没必要所有的节点都要更新，只更新这个li标签就行，Diff算法就是查出这个li标签的算法。

总结：**Diff算法是一种对比算法**。对比两者是`旧虚拟DOM和新虚拟DOM`，对比出是哪个`虚拟节点`更改了，找出这个`虚拟节点`，并只更新这个虚拟节点所对应的`真实节点`，而不用更新其他数据没发生改变的节点，实现`精准`地更新真实DOM，进而`提高效率`。

`使用虚拟DOM算法的损耗计算`： 总损耗 = 虚拟DOM增删改+（与Diff算法效率有关）真实DOM差异增删改+（较少的节点）排版与重绘

`直接操作真实DOM的损耗计算`： 总损耗 = 真实DOM完全增删改+（可能较多的节点）排版与重绘



------------------------

#### Diff对比流程

当数据改变时，会触发`setter`，并且通过`Dep.notify`去通知所有`订阅者Watcher`，订阅者们就会调用`patch方法`，给真实DOM打补丁，更新相应的视图。对于这一步不太了解的可以看一下我之前写[Vue源码系列](https://juejin.cn/column/6969563635194527758)

`newVnode和oldVnode`：同层的新旧虚拟节点 ![截屏2021-08-08 上午11.49.38.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1db54647698e4c76b6fc38a02067ad72~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

#### patch方法

这个方法作用就是，对比当前同层的虚拟节点是否为同一种类型的标签`(同一类型的标准，下面会讲)`：

- 是：继续执行`patchVnode方法`进行深层比对
- 否：没必要比对了，直接整个节点替换成`新虚拟节点`

来看看`patch`的核心原理代码

```js
function patch(oldVnode, newVnode) {
  // 比较是否为一个类型的节点
  if (sameVnode(oldVnode, newVnode)) {
    // 是：继续进行深层比较
    patchVnode(oldVnode, newVnode)
  } else {
    // 否
    const oldEl = oldVnode.el // 旧虚拟节点的真实DOM节点
    const parentEle = api.parentNode(oldEl) // 获取父节点
    createEle(newVnode) // 创建新虚拟节点对应的真实DOM节点
    if (parentEle !== null) {
      api.insertBefore(parentEle, vnode.el, api.nextSibling(oEl)) // 将新元素添加进父元素
      api.removeChild(parentEle, oldVnode.el)  // 移除以前的旧元素节点
      // 设置null，释放内存
      oldVnode = null
    }
  }

  return newVnode
}
复制代码
```

#### sameVnode方法

patch关键的一步就是`sameVnode方法判断是否为同一类型节点`，那问题来了，怎么才算是同一类型节点呢？这个`类型`的标准是什么呢？

咱们来看看sameVnode方法的核心原理代码，就一目了然了

```js
function sameVnode(oldVnode, newVnode) {
  return (
    oldVnode.key === newVnode.key && // key值是否一样
    oldVnode.tagName === newVnode.tagName && // 标签名是否一样
    oldVnode.isComment === newVnode.isComment && // 是否都为注释节点
    isDef(oldVnode.data) === isDef(newVnode.data) && // 是否都定义了data
    sameInputType(oldVnode, newVnode) // 当标签为input时，type必须是否相同
  )
}
复制代码
```

#### patchVnode方法

这个函数做了以下事情：

- 找到对应的`真实DOM`，称为`el`
- 判断`newVnode`和`oldVnode`是否指向同一个对象，如果是，那么直接`return`
- 如果他们都有文本节点并且不相等，那么将`el`的文本节点设置为`newVnode`的文本节点。
- 如果`oldVnode`有子节点而`newVnode`没有，则删除`el`的子节点
- 如果`oldVnode`没有子节点而`newVnode`有，则将`newVnode`的子节点真实化之后添加到`el`
- 如果两者都有子节点，则执行`updateChildren`函数比较子节点，这一步很重要

```js
function patchVnode(oldVnode, newVnode) {
  const el = newVnode.el = oldVnode.el // 获取真实DOM对象
  // 获取新旧虚拟节点的子节点数组
  const oldCh = oldVnode.children, newCh = newVnode.children
  // 如果新旧虚拟节点是同一个对象，则终止
  if (oldVnode === newVnode) return
  // 如果新旧虚拟节点是文本节点，且文本不一样
  if (oldVnode.text !== null && newVnode.text !== null && oldVnode.text !== newVnode.text) {
    // 则直接将真实DOM中文本更新为新虚拟节点的文本
    api.setTextContent(el, newVnode.text)
  } else {
    // 否则

    if (oldCh && newCh && oldCh !== newCh) {
      // 新旧虚拟节点都有子节点，且子节点不一样

      // 对比子节点，并更新
      updateChildren(el, oldCh, newCh)
    } else if (newCh) {
      // 新虚拟节点有子节点，旧虚拟节点没有

      // 创建新虚拟节点的子节点，并更新到真实DOM上去
      createEle(newVnode)
    } else if (oldCh) {
      // 旧虚拟节点有子节点，新虚拟节点没有

      //直接删除真实DOM里对应的子节点
      api.removeChild(el)
    }
  }
}
```


作者：Sunshine_Lin
链接：https://juejin.cn/post/6994959998283907102
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

-------------

### 列表过滤

> 有时，更新一个数组但是不通过实际改变原始数据，这对我们很重要

例如：

```html
<li v-for="n in evenNumbers" :key="n">{{ n }}</li>
```

```js
data() {
  return {
    numbers: [ 1, 2, 3, 4, 5 ]
  }
},
computed: {
  evenNumbers() {
    return this.numbers.filter(number => number % 2 === 0)
  }
}
```

在计算属性不适用的情况下 (例如，在嵌套的 `v-for` 循环中) 你可以使用一个方法：

```html
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)" :key="n">{{ n }}</li>
</ul>
```



```js
data() {
  return {
    sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
  }
},
methods: {
  even(numbers) {
    return numbers.filter(number => number % 2 === 0)
  }
}
```

### 列表排序

> 排序对于列表来说很重要，即使我们前端拿到的数据很可能已经排序完成，但是永远没有再来一遍保险

*场景：我拿到了以下一组数据，调用某个事件后让其根据其中一条属性排序（这里是年龄）*

```json
persons:[
						{id:'001',name:'马冬梅',age:30,sex:'女'},
						{id:'002',name:'周冬雨',age:31,sex:'女'},
						{id:'003',name:'周杰伦',age:18,sex:'男'},
						{id:'004',name:'温兆伦',age:19,sex:'男'}
					]
```

------------

其实在列表过滤中我说了，computed有着比watch更好的效果，这里我依旧选择计算属性

```js
				computed:{
					filPerons(){
						const arr = this.persons.filter((p)=>{
							return p.name.indexOf(this.keyWord) !== -1
						})
						//判断一下是否需要排序
						if(this.sortType){
							arr.sort((p1,p2)=>{
								return this.sortType === 1 ? p2.age-p1.age : p1.age-p2.age
							})
						}
						return arr
					}
				}
```

**这里用了一个指定排序算法**

> Array.sort(compareFunction)  compareFunction`接受两个值，假设为`a`和`b`，`compareFunction(a, b)

- `compareFunction(a, b)` < 0，`a`将会被排到`b`的前面。
- `compareFunction(a, b)` = 0，二者相对位置不发生改变。
- `compareFunction(a, b)` > 0，`a`将会被排到`b`的后面。

希望你对此还熟悉，这是比较基础的内容

### 列表数据监测

> 引用自：https://segmentfault.com/a/1190000011328301 

#### 设计思想：观察者模式

Vue的双向数据绑定的设计思想为`观察者模式`，为了方便，下文中将被观察的对象称为观察者，将观察者对象触发更新的称为订阅者。主要涉及到的概念有：

1. Dep对象：Dependency依赖的简写，包含有三个主要属性`id, subs, target`和四个主要函数`addSub, removeSub, depend, notify`，是观察者的依赖集合，负责在数据发生改变时，使用`notify()`触发保存在`subs`下的订阅列表，依次更新数据和DOM。

   ```stylus
   id: 每个观察者(依赖对象)的唯一标识。
   subs: 观察者对象的订阅者列表。
   target: 全局唯一的订阅者对象，因为只能同时计算和更新一个订阅者的值。
   addSub(): 使用`push()`方法添加一个订阅者。
   removeSub(): 使用`splice()`方法移除一个订阅者。
   depend(): 将自己添加到当前订阅者对象的依赖列表。
   notify(): 在数据被更新时，会遍历subs对象，触发每一个订阅者的更新。
   ```

2. Observer对象：即观察者，包含两个主要属性`value, dep`。做法是使用getter/setter方法覆盖默认的取值和赋值操作，将对象封装为响应式对象，每一次调用时更新依赖列表，更新值时触发订阅者。绑定在对象的`__ob__`原型链属性上。

   ```avrasm
   value: 原始值。
   dep: 依赖列表。
   ```

//TODO: 我们需要更多的知识

#### Vue.set()的使用

> 没有人能面面俱到，总有属性后知后觉的赶来

*Tips：有时，我们需要在已经定义好的数据添加属性，而属性添加之后发现他并没有做到响应式的双向绑定效果*

**你需要Vue.set()方法**

```js
			methods: {
				addSex(){
					// Vue.set(对象,属性名,属性值)
					this.$set(对象,属性名,属性值)
				}
			}
```

---------------

*似乎所有问题都迎刃而解了，但是很遗憾，set()方法不允许在vm._data的本身上添加任何属性*

```
console:
Vue.set(vm._data,"name","hexian")
//报错
vue.js:634 [Vue warn]: Avoid adding reactive properties to a Vue instance or its root $data at runtime - declare it upfront in the data option.
```

### 数组更新检测

> Vue的mvvm模型并不会给与数组元素get与set方法

**所以我们需要使用Vue提供的变更方法来修改数组**

#### [#](https://v3.cn.vuejs.org/guide/list.html#变更方法)变更方法

Vue 将被侦听的数组的变更方法进行了包裹，所以它们也将会触发视图更新。这些被包裹过的方法包括：

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

你可以打开控制台，然后对前面例子的 `items` 数组尝试调用变更方法。比如 `example1.items.push({ message: 'Baz' })`。

#### 替换数组

变更方法，顾名思义，会变更调用了这些方法的原始数组。相比之下，也有非变更方法，例如 `filter()`、`concat()` 和 `slice()`。它们不会变更原始数组，而**总是返回一个新数组**。当使用非变更方法时，可以用新数组替换旧数组：

```js
example1.items = example1.items.filter(item => item.message.match(/Foo/))
```

1

你可能认为这将导致 Vue 丢弃现有 DOM 并重新渲染整个列表。幸运的是，事实并非如此。Vue 为了使得 DOM 元素得到最大范围的重用而实现了一些智能的启发式方法，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。



### 表单展示

> 前端功能之二就是表单的数据收集，Vue中的数据双向绑定该如何使用呢



**Tips:首先，你会注意到你的checked失效了，事实上`v-model` 会忽略所有表单元素的 `value`、`checked`、`selected` attribute 的初始值。它将始终将当前活动实例的数据作为数据来源。你应该通过 JavaScript 在组件的 `data` 选项中声明初始值。**



#### 收集数据：

```html
   <!-- 准备好一个容器-->
    <div id="root">
      <form @submit.prevent="demo">
        <label for="demo">账号：
        <input type="text" v-model.trim="userInfo.account" id="demo" /></label>
        <br /><br />
     </form>
    </div>
```



     密码：<input type="password" v-model="userInfo.password" /> <br /><br />
        <!-- 年龄限制 数字schema -->
        年龄：<input type="number" v-model.number="userInfo.age" /> <br /><br />
        性别： 男<input
          type="radio"
          name="sex"
          v-model="userInfo.sex"
          value="male"
        />
        女<input
          type="radio"
          name="sex"
          v-model="userInfo.sex"
          value="female"
        />
        <br /><br />
        爱好： 学习<input
          type="checkbox"
          v-model="userInfo.hobby"
          value="study"
        />
        打游戏<input type="checkbox" v-model="userInfo.hobby" value="game" />
        吃饭<input type="checkbox" v-model="userInfo.hobby" value="eat" />
        <br /><br />
        所属校区
        <select v-model="userInfo.city">
          <option value="">请选择校区</option>
          <option value="beijing">北京</option>
          <option value="shanghai">上海</option>
          <option value="shenzhen">深圳</option>
          <option value="wuhan">武汉</option>
        </select>
        <br /><br />
        其他信息：
        <!-- lazy 懒惰收集 -->
        <textarea v-model.lazy="userInfo.other"></textarea> <br /><br />
        <input type="checkbox" v-model="userInfo.agree" />阅读并接受<a
          href="http://www.atguigu.com"
          >《用户协议》</a
        >
        <button>提交</button>

//TODO: 需要补充一些知识

### 生命周期 小程序 VS Vue



#### Vue生命周期函数

<img src="/home/hexian/笔记/Vue/资料（含课件）/02_原理图/生命周期.png" style="zoom:150%;" />



### Vue 组件化编程

#### 非单文件组件

1. 模版编写无提示
2. 没有构建过程
3. 不支持组件的CSS
4. 真正开发几乎不用

#### 单文件组件

> 复用才是硬道理

##### 组件的基本使用

###### 模板页面

```html
<template>
	页面模板
</template>
```

###### JS模块对象

```js
<script>
    export default {
    data() {return {}},
    methods: {},
    computed: {},
    components: {}
    }
</script>
```

###### 样式

```css
<style>
	样式定义
</style>
```

###### 使用组件的三大步骤

1. 定义组件（创建组件）

   **使用Vue.extend(options)创建**

   ```vue
       const school = Vue.extend({
         template: `
         	<div class="demo">
         		<h2>学校名称：{{schoolName}}</h2>
         		<h2>学校地址：{{address}}</h2>
         		<button @click="showName">点我提示学校名</button>
         	</div>
         `,
         data() {
           return {
             schoolName: "尚硅谷",
             address: "北京昌平",
           };
         },
         methods: {
           showName() {
             alert(this.schoolName);
           },
         },
       });
   ```

   *tips:*

   *1. 不能使用el指定某个容器，组件应该被vm管理，让vm来决定服务哪个容器2. 不能用对象式写入data属性，对象时写入会产生引用关系，应该使用函数式*

2. 如何注册组件

   

3. 如何使用组件

   1. 局部注册：new Vue时传入components

      ```vue
         //创建vm
          new Vue({
            el: "#root",
            data: {
              msg: "你好啊！",
            },
            //第二步：注册组件（局部注册）
            components: {
              school,
              student,
            },
          });
      ```

      

   2. 全局注册

      ```
        Vue.component("hello", hello);
      ```

      

   



###### 组件的嵌套

> 组件的嵌套十分常用

我们在其中一个组件中使用另外一个组件时，它们会形成一个包含关系，这会使得我们无需再注册两个组件一起，只要注册最外层的主键即可

例如：

```js
		//定义student组件
		const student = Vue.extend({
			name:'student',
			template:`
				<div>
					<h2>学生姓名：{{name}}</h2>	
					<h2>学生年龄：{{age}}</h2>	
				</div>
			`,
			data(){
				return {
					name:'尚硅谷',
					age:18
				}
			}
		})
		
		//定义school组件
		const school = Vue.extend({
			name:'school',
			template:`
				<div>
					<h2>学校名称：{{name}}</h2>	
					<h2>学校地址：{{address}}</h2>	
					<student></student>
				</div>
			`,
			data(){
				return {
					name:'尚硅谷',
					address:'北京'
				}
			},
			//注册组件（局部）
			components:{
				student
			}
		})
```



**而在最外层最好包括一个根元素，这是常规写法**

```js
		//定义app组件
		const app = Vue.extend({
			template:`
				<div>	
					<hello></hello>
					<school></school>
				</div>
			`,
			components:{
				school,
				hello
			}
		})

		//创建vm
		new Vue({
			template:'<app></app>',
			el:'#root',
			//注册组件（局部）
			components:{app}//无需注册所有需要使用的组件
		})
```

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-03-17_15-07.png)

   

##### VueComponent的构造函数

> 组件为何如此好使？

*在上面我们实例中存在<school></school>标签在页面中变成了彻彻底底的实例对象，但我们其实并没有干任何事情，只是做了vue.extend(options),并将其赋予了school外加注册该组件*

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-03-17_15-28.png)

我们可以在Vue.js源码中查看到，每一次我们使用Vue.extend(Options)时，都会初始化并返回一个Sub对象， 

每次调用Vue.extend()方法时，这个闭包函数都会被调用，返回一个全新的Vue.component。

这里或许应该有所总结

- 我们所定义的组件，实际上是一个名为VueComponent的构造函数，并且由extend生成

- 每次编写<组件名>标签时，Vue.component都会执行，并帮我们生成一个实例对象

- 实例化组件过程中，我们不需要去担心this会指向火星，它总是指向Vue.component

  ![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-03-17_15-39.png)

**最后，我们如何知道Vue.component在完成注册后一定在Vue的实例对象之下，并为此实例对象所用呢**

例子：我在一个Vue的实例对象中实例化了两个组件，其一是普通组件，其二是嵌套化组件



![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-03-17_15-45.png)

*tips：我们可以看到vm下出现了0与1VueComponent*

##### 一条低调的隐式原型链

> 我们更希望组件实例化对象能够访问到Vue的原型上的属性以及方法

![](/home/hexian/笔记/笔记_全/笔记截图/ByHexian_2022-03-17_16-15.png)

我们都知道，一个函数构造函数的显示原型与其实例对象的隐式原型指向其构造函数的原型对象，**但VueComponent为了能够让组件实例对象（vc）可以访问到 Vue原型上的属性、方法。将VueCompoent的原型对象指向了Vue的原型对象**

# Vue CLI来了



## 脚手架文件结构

	├── node_modules 
	├── public
	│   ├── favicon.ico: 页签图标
	│   └── index.html: 主页面
	├── src
	│   ├── assets: 存放静态资源
	│   │   └── logo.png
	│   │── component: 存放组件
	│   │   └── HelloWorld.vue
	│   │── App.vue: 汇总所有组件
	│   │── main.js: 入口文件
	├── .gitignore: git版本管制忽略的配置
	├── babel.config.js: babel的配置文件
	├── package.json: 应用包配置文件 
	├── README.md: 应用描述文件
	├── package-lock.json：包版本控制文件

## 关于不同版本的Vue

1. vue.js与vue.runtime.xxx.js的区别：
   1. vue.js是完整版的Vue，包含：核心功能 + 模板解析器。
   2. vue.runtime.xxx.js是运行版的Vue，只包含：核心功能；没有模板解析器。
2. 因为vue.runtime.xxx.js没有模板解析器，所以不能使用template这个配置项，需要使用render函数接收到的createElement函数去指定具体内容。

## vue.config.js配置文件

1. 使用vue inspect > output.js可以查看到Vue脚手架的默认配置。
2. 使用vue.config.js可以对脚手架进行个性化定制，详情见：https://cli.vuejs.org/zh

## ref属性

> 这对于组件通信很重要

1. 被用来给元素或子组件注册引用信息（id的替代者）
2. 应用在html标签上获取的是真实DOM元素，应用在组件标签上是组件实例对象（vc）
3. 使用方式：
   1. 打标识：```<h1 ref="xxx">.....</h1>``` 或 ```<School ref="xxx"></School>```
   2. 获取：```this.$refs.xxx```

```vue

<template>
  <div>
    <h1 v-text="msg" ref="title"></h1>
    <button ref= "btn" @click="showDOM">获取DOM</button>
    <SchoolName ref="school_Component"/>
  </div>
</template>

<script>

import SchoolName from './components/School'

export default {
  name:'App',
  components:{SchoolName},
  data() {
    return {
      msg:"傻逼毕设指导老师"
    }
  },
  methods: {
    showDOM(){
      console.log(this.$refs);
    }
  },
}
</script>

<style>

</style>
```

![](https://i.imgur.com/1Yu1Mi8.png)

## props配置项

1. 功能：让组件接收外部传过来的数据

2. 传递数据：```<Demo name="xxx"/>```

3. 接收数据：

   1. 第一种方式（只接收）：```props:['name'] ```

   2. 第二种方式（限制类型）：```props:{name:String}```

   3. 第三种方式（限制类型、限制必要性、指定默认值）：

      ```js
      props:{
      	name:{
      	type:String, //类型
      	required:true, //必要性
      	default:'老王' //默认值
      	}
      }
      ```

   > 备注：props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。

## mixin(混入)

1. 功能：可以把多个组件共用的配置提取成一个混入对象

2. 使用方式：

   第一步定义混合：

   ```
   {
       data(){....},
       methods:{....}
       ....
   }
   ```

   第二步使用混入：

   ​	全局混入：```Vue.mixin(xxx)```
   ​	局部混入：```mixins:['xxx']	```

## 插件

1. 功能：用于增强Vue

2. 本质：包含install方法的一个对象，install的第一个参数是Vue，第二个以后的参数是插件使用者传递的数据。

3. 定义插件：

   ```js
   对象.install = function (Vue, options) {
       // 1. 添加全局过滤器
       Vue.filter(....)
   
       // 2. 添加全局指令
       Vue.directive(....)
   
       // 3. 配置全局混入(合)
       Vue.mixin(....)
   
       // 4. 添加实例方法
       Vue.prototype.$myMethod = function () {...}
       Vue.prototype.$myProperty = xxxx
   }
   ```

4. 使用插件：```Vue.use()```

## scoped样式

1. 作用：让样式在局部生效，防止冲突。
2. 写法：```<style scoped>```

## 总结TodoList案例

1. 组件化编码流程：

   ​	(1).拆分静态组件：组件要按照功能点拆分，命名不要与html元素冲突。

   ​	(2).实现动态组件：考虑好数据的存放位置，数据是一个组件在用，还是一些组件在用：

   ​			1).一个组件在用：放在组件自身即可。

   ​			2). 一些组件在用：放在他们共同的父组件上（<span style="color:red">状态提升</span>）。

   ​	(3).实现交互：从绑定事件开始。

2. props适用于：

   ​	(1).父组件 ==> 子组件 通信

   ​	(2).子组件 ==> 父组件 通信（要求父先给子一个函数）

3. 使用v-model时要切记：v-model绑定的值不能是props传过来的值，因为props是不可以修改的！

4. props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错，但不推荐这样做。

## webStorage

1. 存储内容大小一般支持5MB左右（不同浏览器可能还不一样）

2. 浏览器端通过 Window.sessionStorage 和 Window.localStorage 属性来实现本地存储机制。

3. 相关API：

   1. ```xxxxxStorage.setItem('key', 'value');```
      	该方法接受一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应的值。

   2. ```xxxxxStorage.getItem('person');```

      ​		该方法接受一个键名作为参数，返回键名对应的值。

   3. ```xxxxxStorage.removeItem('key');```

      ​		该方法接受一个键名作为参数，并把该键名从存储中删除。

   4. ``` xxxxxStorage.clear()```

      ​		该方法会清空存储中的所有数据。

4. 备注：

   1. SessionStorage存储的内容会随着浏览器窗口关闭而消失。
   2. LocalStorage存储的内容，需要手动清除才会消失。
   3. ```xxxxxStorage.getItem(xxx)```如果xxx对应的value获取不到，那么getItem的返回值是null。
   4. ```JSON.parse(null)```的结果依然是null。

## 组件的自定义事件

1. 一种组件间通信的方式，适用于：<strong style="color:red">子组件 ===> 父组件</strong>

2. 使用场景：A是父组件，B是子组件，B想给A传数据，那么就要在A中给B绑定自定义事件（<span style="color:red">事件的回调在A中</span>）。

3. 绑定自定义事件：

   1. 第一种方式，在父组件中：```<Demo @atguigu="test"/>```  或 ```<Demo v-on:atguigu="test"/>```

   2. 第二种方式，在父组件中：

      ```js
      <Demo ref="demo"/>
      ......
      mounted(){
         this.$refs.xxx.$on('atguigu',this.test)
      }
      ```

   3. 若想让自定义事件只能触发一次，可以使用```once```修饰符，或```$once```方法。

4. 触发自定义事件：```this.$emit('atguigu',数据)```		

5. 解绑自定义事件```this.$off('atguigu')```

6. 组件上也可以绑定原生DOM事件，需要使用```native```修饰符。

7. 注意：通过```this.$refs.xxx.$on('atguigu',回调)```绑定自定义事件时，回调<span style="color:red">要么配置在methods中</span>，<span style="color:red">要么用箭头函数</span>，否则this指向会出问题！

## 全局事件总线（GlobalEventBus）

1. 一种组件间通信的方式，适用于<span style="color:red">任意组件间通信</span>。

2. 安装全局事件总线：

   ```js
   new Vue({
   	......
   	beforeCreate() {
   		Vue.prototype.$bus = this //安装全局事件总线，$bus就是当前应用的vm
   	},
       ......
   }) 
   ```

3. 使用事件总线：

   1. 接收数据：A组件想接收数据，则在A组件中给$bus绑定自定义事件，事件的<span style="color:red">回调留在A组件自身。</span>

      ```js
      methods(){
        demo(data){......}
      }
      ......
      mounted() {
        this.$bus.$on('xxxx',this.demo)
      }
      ```

   2. 提供数据：```this.$bus.$emit('xxxx',数据)```

4. 最好在beforeDestroy钩子中，用$off去解绑<span style="color:red">当前组件所用到的</span>事件。

## 消息订阅与发布（pubsub）少用

1. 一种组件间通信的方式，适用于<span style="color:red">任意组件间通信</span>。

2. 使用步骤：

   1. 安装pubsub：```npm i pubsub-js```

   2. 引入: ```import pubsub from 'pubsub-js'```

   3. 接收数据：A组件想接收数据，则在A组件中订阅消息，订阅的<span style="color:red">回调留在A组件自身。</span>

      ```js
      methods(){
        demo(data){......}
      }
      ......
      mounted() {
        this.pid = pubsub.subscribe('xxx',this.demo) //订阅消息
      }
      ```

   4. 提供数据：```pubsub.publish('xxx',数据)```

   5. 最好在beforeDestroy钩子中，用```PubSub.unsubscribe(pid)```去<span style="color:red">取消订阅。</span>

## nextTick

> `nextTick` 是 `vue` 中的更新策略，也是性能优化手段，基于`JS`执行机制实现

Vue中改变数据不会立即触发视图，

1. 语法：```this.$nextTick(回调函数)```
2. 作用：在下一次 DOM 更新结束后执行其指定的回调。
3. 什么时候用：当改变数据后，要基于更新后的新DOM进行某些操作时，要在nextTick所指定的回调函数中执行。

## Vue封装的过度与动画

1. 作用：在插入、更新或移除 DOM元素时，在合适的时候给元素添加样式类名。

2. 图示：<img src="https://img04.sogoucdn.com/app/a/100520146/5990c1dff7dc7a8fb3b34b4462bd0105" style="width:60%" />

3. 写法：

   1. 准备好样式：

      - 元素进入的样式：
        1. v-enter：进入的起点
        2. v-enter-active：进入过程中
        3. v-enter-to：进入的终点
      - 元素离开的样式：
        1. v-leave：离开的起点
        2. v-leave-active：离开过程中
        3. v-leave-to：离开的终点

   2. 使用```<transition>```包裹要过度的元素，并配置name属性：

      ```vue
      <transition name="hello">	<h1 v-show="isShow">你好啊！</h1></transition>
      ```

   3. 备注：若有多个元素需要过度，则需要使用：```<transition-group>```，且每个元素都要指定```key```值。
   
   **第三方动画**
   
   - 需要先安装animation.css
   
     ```bash
     $ npm install animate.css --save
     ```
   
     
   
   - 以下格式仅供参考 需要name enter-ac leave-ac
   
   ```html
   <transition  appear name="animate__animated animate__bounce" enter-active-class="animate__swing" leave-active-class="animate__bounceOutDown">
            、   <h1 v-show="isShow" >你好</h1>
   </transition>
   ```
   
   **参照https://animate.style/**
   
   

## vue脚手架配置代理

### 方法一

​	在vue.config.js中添加如下配置：

```js
devServer:{  proxy:"http://localhost:5000"}
```

说明：

1. 优点：配置简单，请求资源时直接发给前端（8080）即可。
2. 缺点：不能配置多个代理，不能灵活的控制请求是否走代理。
3. 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求会转发给服务器 （优先匹配前端资源 请注意public中静态资源与后台数据接口名称 切记不要重名）

### 方法二

​	编写vue.config.js配置具体代理规则：

```js
module.exports = {	
    devServer: {      
        proxy: {      
            '/api1': 
            {// 匹配所有以 '/api1'开头的请求路径        
                target: 'http://localhost:5000',// 代理目标的基础路径  
                changeOrigin: true,    //是否开启跨域    
                pathRewrite: {'^/api1': ''}     
            },      
            '/api2': {// 匹配所有以 '/api2'开头的请求路径 
                target: 'http://localhost:5001',// 代理目标的基础路径
                changeOrigin: true,
                pathRewrite: {'^/api2': ''}
            }
        }
    }
}/*   changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000   changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:8080   changeOrigin默认值为true*/
```

说明：

1. 优点：可以配置多个代理，且可以灵活的控制请求是否走代理。
2. 缺点：配置略微繁琐，请求资源时必须加前缀。

## 插槽

1. 作用：让父组件可以向子组件指定位置插入html结构，也是一种组件间通信的方式，适用于 <strong style="color:red">父组件 ===> 子组件</strong> 。

2. 分类：默认插槽、具名插槽、作用域插槽

3. 使用方式：

   1. 默认插槽：

      ```vue
      父组件中：
      <Category>
          <div>html结构1</div>
      </Category>
      子组件中：
      <template>
          <div>              
              <!-- 定义插槽 -->
              <slot>插槽默认内容...</slot> 
           </div>        
      </template>
      ```

   2. 具名插槽：

      ```vue
      父组件中：
      <Category>
          
          <template slot="center">
              <div>html结构1</div>
          </template>
          
          <template v-slot:footer>
              <div>html结构2</div>
          </template>
          
      </Category>
      
      子组件中：
      <template> 
      	<div>               
              <!-- 定义插槽 -->
              <slot name="center">插槽默认内容...</slot>
              <slot name="footer">插槽默认内容...</slot>
          </div>        
      </template>
      ```
   
   3. 作用域插槽：
   
      1. 理解：<span style="color:red">数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定。</span>（games数据在Category组件中，但使用数据所遍历出来的结构由App组件决定）
   
      2. 具体编码：
   
         ```vue
         父组件中：
         <Category>
             <template scope="scopeData">
         <!-- 生成的是ul列表 -->
         <ul>
             <li v-for="g in scopeData.games" :key="g">{{g}}</li>				</ul>
             </template>	
         </Category>		
         <Category>	
             <template slot-scope="scopeData">
         <!-- 生成的是h4标题 -->	
         <h4 v-for="g in scopeData.games" :key="g">{{g}}</h4>
             </template>		
         </Category>
         子组件中：      
         <template>     
         <div>               
             <slot :games="games"></slot>      
             </div>        
         </template>		       
         <script>            
             export default {               
                 name:'Category',          
                 props:['title'],            
                 //数据在子组件自身            
                 data() {               
                     return {                
                         games:['红色警戒','穿越火线','劲舞团','超级玛丽']   
                     }               
                 },           
             }       
         </script>
         ```
   
   ```
   
   ```

## Vuex

### 1.概念

​		在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

### 2.何时使用？

​		多个组件需要共享数据时

### 3.搭建vuex环境

1. 创建文件：```src/store/index.js```

   ```js
   //引入Vue核心库
   import Vue from 'vue'
   //引入Vuex
   import Vuex from 'vuex'
   //应用Vuex插件
   Vue.use(Vuex)
   //准备actions对象——响应组件中用户的动作
   const actions = {}
   //准备mutations对象——修改state中的数据
   const mutations = {}
   //准备state对象——保存具体的数据
   const state = {}
   //创建并暴露store
   export default new Vuex.Store({	actions,	mutations,	state})
   ```

2. 在```main.js```中创建vm时传入```store```配置项

   ```js
   
   //引入store
   import store from './store'
   //创建vm
   new Vue({	
       el:'#app',	
       render: h => h(App),	
       store})
   ```

###    4.基本使用

1. 初始化数据、配置```actions```、配置```mutations```，操作文件```store.js```

   ```js
   //引入Vue核心库
   import Vue from 'vue'
   //引入Vuex
   import Vuex from 'vuex'
   //引用Vuex
   Vue.use(Vuex)
   const actions = {    
       //响应组件中加的动作	
       jia(context,value){		
           // console.log('actions中的jia被调用了',miniStore,value)		
           context.commit('JIA',value)	
       },
   }
   const mutations = {    
         //执行加	
         JIA(state,value){		
             // console.log('mutations中的JIA被调用了',state,value)			
             state.sum += value	
               }
          }
               //初始化数据
               const state = {   sum:0}
           //创建并暴露store
           export default new Vuex.Store({	actions,	mutations,	state,})
   ```

2. 组件中读取vuex中的数据：```$store.state.sum```

3. 组件中修改vuex中的数据：```$store.dispatch('action中的方法名',数据)``` 或 ```$store.commit('mutations中的方法名',数据)```

   >  备注：若没有网络请求或其他业务逻辑，组件中也可以越过actions，即不写```dispatch```，直接编写```commit```

### 5.getters的使用

1. 概念：当state中的数据需要经过加工后再使用时，可以使用getters加工。

2. 在```store.js```中追加```getters```配置

   ```js
   ......
   const getters = 
       {	bigSum(state)
        {		return state.sum * 10	
        }
       }
   //创建并暴露store
   export default new Vuex.Store(
       {	......	getters})
   ```

3. 组件中读取数据：```$store.getters.bigSum```

### 6.四个map方法的使用

> key 永远在页面上 value在js里

1. <strong>mapState方法：</strong>用于帮助我们映射```state```中的数据为计算属性

   ```js
   computed: {    
           //借助mapState生成计算属性：sum、school、subject（对象写法）     
       ...mapState({sum:'sum',school:'school',subject:'subject'}),             
           //借助mapState生成计算属性：sum、school、subject（数组写法）   
       ...mapState(['sum','school','subject']),},
   ```

2. <strong>mapGetters方法：</strong>用于帮助我们映射```getters```中的数据为计算属性

   ```js
   computed: {    
       	//借助mapGetters生成计算属性：bigSum（对象写法）    ...
       	mapGetters({bigSum:'bigSum'}),    
           //借助mapGetters生成计算属性：bigSum（数组写法）    ...
           mapGetters(['bigSum'])},
   ```

3. <strong>mapActions方法：</strong>用于帮助我们生成与```actions```对话的方法，即：包含```$store.dispatch(xxx)```的函数

   ```js
   methods:{    
       //靠mapActions生成：incrementOdd、incrementWait（对象形式）    
       ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})   
       //靠mapActions生成：incrementOdd、incrementWait（数组形式）    
       ...mapActions(['jiaOdd','jiaWait'])}
   ```

4. <strong>mapMutations方法：</strong>用于帮助我们生成与```mutations```对话的方法，即：包含```$store.commit(xxx)```的函数

   ```js
   methods:{    
       //靠mapActions生成：increment、decrement（对象形式）    
       ...mapMutations({increment:'JIA',decrement:'JIAN'}),        
        //靠mapMutations生成：JIA、JIAN（对象形式）    
       ...mapMutations(['JIA','JIAN']),}
   ```

> 备注：mapActions与mapMutations使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。

### 7.模块化+命名空间

1. 目的：让代码更好维护，让多种数据分类更加明确。

2. 修改```store.js```

   ```javascript
   const countAbout = {  
       namespaced:true,
       //开启命名空间  
       state:{x:1},  
       mutations: { ... },  
       actions: { ... },  
       getters: {    
          bigSum(state){       
              return state.sum * 10    
          }  
       }
   }
                   
      const personAbout = 
                   {  namespaced:true,
                       //开启命名空间  
                       state:{ ... },  
                       mutations: { ... },  
                       actions: { ... }
                   }
                               
        const store = new Vuex.Store({  modules: {    countAbout,    personAbout  }})
   ```

3. 开启命名空间后，组件中读取state数据：

   ```js
   //方式一：自己直接读取
   this.$store.state.personAbout.list
   //方式二：借助mapState读取：
       	...mapState('countAbout',['sum','school','subject']),
   ```

4. 开启命名空间后，组件中读取getters数据：(分类名/getter名)

   ```js
   //方式一：自己直接读取
   this.$store.getters['personAbout/firstPersonName']
   //方式二：借助mapGetters读取：
       ...mapGetters('countAbout',['bigSum'])
   ```

5.  开启命名空间后，组件中调用dispatch （分类名/action名）

   ```js
   //方式一：自己直接dispatch
   this.$store.dispatch('personAbout/addPersonWang',person)
   //方式二：借助mapActions：
       ...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   ```

6. 开启命名空间后，组件中调用commit （分类名 /mustations名）

   ```js
   //方式一：自己直接commit
   this.$store.commit('personAbout/ADD_PERSON',person)
   //方式二：借助mapMutations：
       ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
   ```

## Pinia

> 一个官方推荐的状态共享组件

### 安装与启用

Pinia 目前还没有被广泛的默认集成在各种脚手架里，所以如果你原来创建的项目没有 Pinia ，则需要手动安装它。

```js
# 需要 cd 到你的项目目录下
npm install pinia
```

查看你的 package.json ，看看里面的 `dependencies` 是否成功加入了 Pinia 和它的版本号（下方是示例代码，以实际安装的最新版本号为准）：

```json
{
  "dependencies": {
    "pinia": "^2.0.11",
  },
}
```

然后打开 `src/main.js` 文件，添加下面那两行有注释的新代码：

```js
import { createApp } from 'vue'
import { createPinia } from 'pinia' // 导入 Pinia
import App from '@/App.vue'

createApp(App)
  .use(createPinia()) // 启用 Pinia
  .mount('#app')
```

### 与Vuex的区别

|   作用   | Vue Component |        Vuex         |  Pinia  |
| :------: | :-----------: | :-----------------: | :-----: |
| 数据管理 |     data      |        state        |  state  |
| 数据计算 |   computed    |       getters       | getters |
| 行为方法 |    methods    | mutations / actions | actions |

可以看到 Pinia 的结构和用途都和 Vuex 与 Component 非常相似，并且 Pinia 相对于 Vuex ，在行为方法部分去掉了 mutations （同步操作）和 actions （异步操作）的区分，更接近组件的结构，入门成本会更低一些。

下面我们来创建一个简单的 Store ，开始用 Pinia 来进行状态管理。

### 创建Store

> Store 是通过 `defineStore` 方法来创建的，它有两种入参形式：

#### 形式1：接受两个参数

```js
// 接受两个参数
接受两个参数 一个是Store的唯一ID，第二个参数是Store的选项
import { defineStore } from 'pinia'

export const useStore = defineStore('main', {
  // Store 选项...
})
```

#### 形式2：接受一个参数

```js
// src/stores/index.ts
import { defineStore } from 'pinia'

export const useStore = defineStore({
  id: 'main',
  // Store 选项...
})
```

**TIPS:必须指定唯一ID 不论哪种形式**



 ## 路由



1. 理解： 一个路由（route）就是一组映射关系（key - value），多个路由需要路由器（router）进行管理。
2. 前端路由：key是路径，value是组件。

### 1.基本使用

1. 安装vue-router，命令：```npm i vue-router```

2. 应用插件：```Vue.use(VueRouter)```

3. 编写router配置项:

   ```js
   //引入VueRouter
   import VueRouter from 'vue-router'
   //引入Luyou 组件
   import About from '../components/About'
   import Home from '../components/Home'
   //创建router实例对象，去管理一组一组的路由规则
   const router = 
         new VueRouter({
             routes:[		
                 {			
                     path:'/about',			
                     component:About		
                 },		
                 {		path:'/home',
                  		component:Home		
                 }
             ]
         })//暴露routerexport default router
   ```

4. 实现切换（active-class可配置高亮样式）

   ```vue
   <router-link active-class="active" to="/about">About</router-link>
   ```

5. 指定展示位置

   ```vue
   <router-view></router-view>
   ```

### 2.几个注意点

1. 路由组件通常存放在```pages```文件夹，一般组件通常存放在```components```文件夹。
2. 通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载。
3. 每个组件都有自己的```$route```属性，里面存储着自己的路由信息。
4. 整个应用只有一个router，可以通过组件的```$route```属性获取到。

### 3.多级路由（多级路由）

1. 配置路由规则，使用children配置项：

   ```js
   routes:[	
       {		
           path:'/about',
           component:About,
       },	
       {		
           path:'/home',
           component:Home,
           children:[ 
               //通过children配置子级路由
               {				
                   path:'news', 
                   //此处一定不要写：/news	
                   component:News
               },			
               {				
                   path:'message',
                   //此处一定不要写：/message	
                   component:Message
               }		
           ]	
       }]
   ```

2. 跳转（要写完整路径）：

   ```vue
   <router-link to="/home/news">News</router-link>
   ```

### 4.路由的query参数

1. 传递参数

   ```vue
   <!-- 跳转并携带query参数，to的字符串写法 -->
   <router-link :to="/home/message/detail?id=666&title=你好">跳转</router-link><!-- 跳转并携带query参数，to的对象写法 -->
   <router-link 	
                :to="{		path:'/home/message/detail',
                     query:{
                         id:666,          
                         title:'你好'		
                         }	
                     }">跳转</router-link>
   ```

2. 接收参数：

   ```js
   $route.query.id
   $route.query.title
   ```

### 5.命名路由

1. 作用：可以简化路由的跳转。

2. 如何使用

   1. 给路由命名：

      ```js
      {	
          path:'/demo',
          component:Demo,
             children:[		
                 {			
                     path:'test',			
                     component:Test,			
                     children:[				
                         {                      
                             name:'hello' 
                             //给路由命名					
                             path:'welcome',				
                             component:Hello,				
                         }			
                     ]		
                 }	
             ]
      }
      ```
   
   2. 简化跳转：
   
      ```vue
      <!--简化前，需要写完整的路径 -->
      <router-link to="/demo/test/welcome">跳转</router-link>
      <!--简化后，直接通过名字跳转 -->
      <router-link :to="{name:'hello'}">跳转</router-link>
      <!--简化写法配合传递参数 -->
      <router-link 	:to="{		
                           name:'hello',		
                           query:{id:666,itle:'你好'}	
                           }">跳转</router-link>
      ```

### 6.路由的params参数

> 需要占位

1. 配置路由，声明接收params参数

   ```js
   {	
       path:'/home',
       component:Home,	
           children:[
               		{			
                           path:'news',			
                           component:News		
                       },		
               		{			
                           component:Message,			
                           children:[				
                               {																			name:'xiangqing',														path:'detail/:id/:title',
                                    //使用占位符声明接收params参数
                                    component:Detail				
                               }			
                           ]		
                       }	
           ]}
   ```

2. 传递参数

   ```vue
   <!-- 跳转并携带params参数，to的字符串写法 -->
   <router-link :to="/home/message/detail/666/你好">跳转</router-link>			<!-- 跳转并携带params参数，to的对象写法 -->
   <router-link 	:to="{		
                        name:'xiangqing',		
                        params:{		  
                        id:666,            
                        title:'你好'		}	
                        }">跳转</router-link>
   ```

   > 特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置！！！！

3. 接收参数：

   ```js
   $route.params.id
   $route.params.title
   ```
   
   **如何指定params参数可传可不传**
   
   配置路由的时候，在占位路由后面加上一个问号【params可以为空】
   
   ```
       name: "search",
       path: "/search/:keyword?",<=
       component: Search,
       meta: {
         showFooter: true,
       },
   ```
   
   如果传递的是空串 如何解决？
   
   使用undefined解决问题
   
   ```js
       methods:{
           goSearch(){
               this.$router.push({
                   name:"search",
                   params:{
                       keyword:this.keyword||undefined
                   },
                   query:{
                       k:this.keyword.toUpperCase()
                   }
               })
           }
       }
   ```
   
   

### 7.路由的props配置

​	作用：让路由组件更方便的收到参数

```js
{	name:'xiangqing',	
    path:'detail/:id',	
    component:Detail,	
            //第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件	//
            props:{a:900}	
 //第二种写法：props值为布尔值，布尔值为true，则把路由收到的所有params参数通过props传给Detail组件	(很方便)
 // props:true		
 //第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件 (很常用)
 props(route){		
     return {			
         id:route.query.id,			
         title:route.query.title		
     }	
 }}
```

### 8.```<router-link>```的replace属性(声明式导航)

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别为```push```和```replace```，```push```是追加历史记录，```replace```是替换当前记录。路由跳转时候默认为```push```
3. 如何开启```replace```模式：```<router-link replace .......>News</router-link>```

### 9.编程式路由导航

1. 作用：不借助```<router-link> ```实现路由跳转，让路由跳转更加灵活

2. 具体编码：

   ```js
   //$router的两个API
   this.$router.push({	name:'xiangqing',		
                      params:{			id:xxx,			title:xxx		}})
   this.$router.replace({	name:'xiangqing',		
                         params:{			id:xxx,			title:xxx	}})
   this.$router.forward() //前进
   this.$router.back() //后退
   this.$router.go() //可前进也可后退
   ```

### 10.缓存路由组件

1. 作用：让不展示的路由组件保持挂载，不被销毁。

2. 具体编码：

3. > **include 组件名称**

   ```vue
   <keep-alive include="News">     <router-view></router-view></keep-alive>
   ```

### 11.两个新的生命周期钩子

1. 作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态。
2. 具体名字：
   1. ```activated```路由组件被激活时触发。
   2. ```deactivated```路由组件失活时触发。

### 12.路由守卫

1. 作用：对路由进行权限控制

2. 分类：全局守卫、独享守卫、组件内守卫

3. 全局守卫:

   ```js
   //全局前置守卫：初始化时执行、每次路由切换前执行
   router.beforeEach((to,from,next)=>
                     {	console.log('beforeEach',to,from)	
                      if(to.meta.isAuth){ 
                          //判断当前路由是否需要进行权限控制
                          if(localStorage.getItem('school') === 'atguigu')
                          { //权限控制的具体规则			
                              next() //放行		
                          }else{			
                              alert('暂无权限查看')			
                              // next({name:'guanyu'}
                              )		
                          }	
                      }else{		
                          next() //放行	
                      }
                     })
   //全局后置守卫：初始化时执行、每次路由切换后执行
   router.afterEach((to,from)=>{	
       console.log('afterEach',to,from)	
       if(to.meta.title){ 		
           document.title = to.meta.title //修改网页的title
       }else{		
           document.title = 'vue_test'	}})
   ```

4. 独享守卫:

   ```js
   beforeEnter(to,from,next)
   {	
       console.log('beforeEnter',to,from)	
    	if(to.meta.isAuth){ 
                   //判断当前路由是否需要进行权限控制		
                   if(localStorage.getItem('school') === 'atguigu')
                   {			
                       next()		
                   }
           		else
                   {			
                       alert('暂无权限查看')			
                       // next({name:'guanyu'})		
                   }	
        }
       else
        {		
            next()	
        }
   }
   ```

5. 组件内守卫：

   ```js
   //进入守卫：通过路由规则，进入该组件时被调用
   beforeRouteEnter (to, from, next) {},
   //离开守卫：通过路由规则，离开该组件时被调用
   beforeRouteLeave (to, from, next) {}
   ```

### 13.路由器的两种工作模式



1. 对于一个url来说，什么是hash值？—— #及其后面的内容就是hash值。

2. hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。

3. hash模式：

   1. 地址中永远带着#号，不美观 。
   2. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。
   3. 兼容性较好。

4. history模式：

   1. 地址干净，美观 。
   2. 兼容性和hash模式相比略差。
   3. 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。

    

# Vue3快速上手

<img src="https://user-images.githubusercontent.com/499550/93624428-53932780-f9ae-11ea-8d16-af949e16a09f.png" style="width:200px" />



## 1.Vue3简介

- 2020年9月18日，Vue.js发布3.0版本，代号：One Piece（海贼王）
- 耗时2年多、[2600+次提交](https://github.com/vuejs/vue-next/graphs/commit-activity)、[30+个RFC](https://github.com/vuejs/rfcs/tree/master/active-rfcs)、[600+次PR](https://github.com/vuejs/vue-next/pulls?q=is%3Apr+is%3Amerged+-author%3Aapp%2Fdependabot-preview+)、[99位贡献者](https://github.com/vuejs/vue-next/graphs/contributors) 
- github上的tags地址：https://github.com/vuejs/vue-next/releases/tag/v3.0.0

## 2.Vue3带来了什么

### 1.性能的提升

- 打包大小减少41%

- 初次渲染快55%, 更新渲染快133%

- 内存减少54%

  ......

### 2.源码的升级

- 使用Proxy代替defineProperty实现响应式

- 重写虚拟DOM的实现和Tree-Shaking

  ......

### 3.拥抱TypeScript

- Vue3可以更好的支持TypeScript

### 4.新的特性

1. Composition API（组合API）

   - setup配置
   - ref与reactive
   - watch与watchEffect
   - provide与inject
   - ......
2. 新的内置组件
   - Fragment 
   - Teleport
   - Suspense
3. 其他改变

   - 新的生命周期钩子
   - data 选项应始终被声明为一个函数
   - 移除keyCode支持作为 v-on 的修饰符
   - ......

# 一、创建Vue3.0工程

## 1.使用 vue-cli 创建

官方文档：https://cli.vuejs.org/zh/guide/creating-a-project.html#vue-create

```bash
## 查看@vue/cli版本，确保@vue/cli版本在4.5.0以上
vue --version
## 安装或者升级你的@vue/cli
npm install -g @vue/cli
## 创建
vue create vue_test
## 启动
cd vue_test
npm run serve
```

## 2.使用 vite 创建

官方文档：https://v3.cn.vuejs.org/guide/installation.html#vite

vite官网：https://vitejs.cn

- 什么是vite？—— 新一代前端构建工具。
- 优势如下：
  - 开发环境中，无需打包操作，可快速的冷启动。
  - 轻量快速的热重载（HMR）。
  - 真正的按需编译，不再等待整个应用编译完成。
- 传统构建 与 vite构建对比图

<img src="https://cn.vitejs.dev/assets/bundler.37740380.png" style="width:500px;height:280px;float:left" /><img src="https://cn.vitejs.dev/assets/esm.3070012d.png" style="width:480px;height:280px" />

```bash
## 创建工程
npm init vite-app <project-name>
## 进入工程目录
cd <project-name>
## 安装依赖
npm install
## 运行
npm run dev
```

# 二、常用 Composition API

官方文档: https://v3.cn.vuejs.org/guide/composition-api-introduction.html

## 0.一些常识

## 1.拉开序幕的setup

1. 理解：Vue3.0中一个新的配置项，值为一个函数。
2. setup是所有<strong style="color:#DD5145">Composition API（组合API）</strong><i style="color:gray;font-weight:bold">“ 表演的舞台 ”</i>。
3. 组件中所用到的：数据、方法等等，均要配置在setup中。
4. setup函数的两种返回值：
   1. 若返回一个对象，则对象中的属性、方法, 在模板中均可以直接使用。（重点关注！）
   2. <span style="color:#aad">若返回一个渲染函数：则可以自定义渲染内容。（了解）</span>
5. 注意点：
   1. 尽量不要与Vue2.x配置混用
      - Vue2.x配置（data、methos、computed...）中<strong style="color:#DD5145">可以访问到</strong>setup中的属性、方法。
      - 但在setup中<strong style="color:#DD5145">不能访问到</strong>Vue2.x配置（data、methos、computed...）。
      - 如果有重名, setup优先。
   2. setup不能是一个async函数，因为返回值不再是return的对象, 而是promise, 模板看不到return对象中的属性。（后期也可以返回一个Promise实例，但需要Suspense和异步组件的配合）

##  2.ref函数

- 作用: 定义一个响应式的数据

- 语法: ```const xxx = ref(initValue)``` 
  - 创建一个包含响应式数据的<strong style="color:#DD5145">引用对象（reference对象，简称ref对象）</strong>。
  - JS中操作数据： ```xxx.value```
  - 模板中读取数据: 不需要.value，直接：```<div>{{xxx}}</div>```
  
- 备注：
  - 接收的数据可以是：基本类型、也可以是对象类型。
  - 基本类型的数据：响应式依然是靠``Object.defineProperty()``的```get```与```set```完成的。
  - 对象类型的数据：内部 <i style="color:gray;font-weight:bold">“ 求助 ”</i> 了Vue3.0中的一个新函数—— ```reactive```函数。
  
  **isRef**
  
  判断是不是一个ref对象
  
  ```js
  import { ref, Ref,isRef } from 'vue'
  let message: Ref<string | number> = ref("我是message")
  let notRef:number = 123
  const changeMsg = () => {
    message.value = "change msg"
    console.log(isRef(message)); //true
    console.log(isRef(notRef)); //false
    
  }
  ```
  
  
  
  ### shallowRef
  
  
  
  > 创建一个跟踪自身 `.value` 变化的 ref，但不会使其值也变成响应式的
  
  ```vue
  <template>
    <div>
      <button @click="changeMsg">change</button>
      <div>{{ message }}</div>
    </div>
  </template>
   
   
   
  <script setup lang="ts">
  import { Ref, shallowRef } from 'vue'
  type Obj = {
    name: string
  }
  let message: Ref<Obj> = shallowRef({
    name: "小满"
  })
   
  const changeMsg = () => {
    message.value.name = '大满'
  }
  </script>
   
   
  <style>
  </style>
  ```
  
  ### triggerRef 
  
  强制更新页面DOM
  
  ```vue
  <template>
    <div>
      <button @click="changeMsg">change</button>
      <div>{{ message }}</div>
    </div>
  </template>
   
   
   
  <script setup lang="ts">
  import { Ref, shallowRef,triggerRef } from 'vue'
  type Obj = {
    name: string
  }
  let message: Ref<Obj> = shallowRef({
    name: "小满"
  })
   
  const changeMsg = () => {
    message.value.name = '大满'
   triggerRef(message)
  }
  </script> 
   
   
  <style>
  </style>
  ```

## 附加 ：toRef toRefs toRaw

### toRef

> 可以将对象的某一个属性抽离出来 转换成响应式数据

*更改响应式数据 原始数据也会更改 但是会不会更新视图需要根据原始数据本身是否为响应式数据*



```vue
<template>
   <div>
      <button @click="change">按钮</button>
      {{state}}
   </div>
</template>
 
<script setup lang="ts">
import { reactive, toRef } from 'vue'
 
const obj = {
   foo: 1,
   bar: 1
}
 
 
const state = toRef(obj, 'bar')
// bar 转化为响应式对象
 
const change = () => {
   state.value++
   console.log(obj, state);
 
}
</script>
```

### toRefs

> 一次解构 多个数据

用法与toRef大差不差

````js
import { reactive, toRefs } from 'vue'
const obj = reactive({
   foo: 1,
   bar: 1
})
 
let { foo, bar } = toRefs(obj)
 
foo.value++
console.log(foo, bar);
````

### toRaw

> 将响应式对象转化为普通对象

```js
import { reactive, toRaw } from 'vue'
 
const obj = reactive({
   foo: 1,
   bar: 1
})
 
 
const state = toRaw(obj)
// 响应式对象转化为普通对象
 
const change = () => {
 
   console.log(obj, state);
 
}
```



## 3.reactive函数

- 作用: 定义一个<strong style="color:#DD5145">对象类型</strong>的响应式数据（基本类型不要用它，要用```ref```函数，因为就算用了ref绑定，在refImpl中也会调用toReactive最后使用reactive函数）
- 语法：```const 代理对象= reactive(源对象)```接收一个对象（或数组），返回一个<strong style="color:#DD5145">代理对象（Proxy的实例对象，简称proxy对象）</strong>
- reactive定义的响应式数据是“深层次的”。
- 内部基于 ES6 的 Proxy 实现，通过代理对象操作源对象内部数据进行操作。

<img src="https://img-blog.csdnimg.cn/4bea460f515a479d82e650c29ee00a99.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP5ruhenM=,size_20,color_FFFFFF,t_70,g_se,x_16" style="zoom:150%;" />

reactive源码对类型存在约束

当他绑定普通数据类型时 会报警

### 基础用法

使用reactive去修改值 无需 .value

```js
import { reactive } from 'vue'
let person = reactive({
   name:"小满"
})
person.name = "大满"
```



但是对于数组的等赋值时

```js
let person = reactive<number[]>([])
setTimeout(() => {
    //直接覆盖对象会使其失去响应式
  person = [1, 2, 3]
  console.log(person);
  
},1000)
```

我们可以使用push或者外面包裹一层对象来解决这类问题

```js
import { reactive } from 'vue'
let person = reactive<number[]>([])
setTimeout(() => {
  const arr = [1, 2, 3]
  person.push(...arr)
  console.log(person);
  
},1000)
```



```js
type Person = {
  list?:Array<number>
}
let person = reactive<Person>({
   list:[]
})
setTimeout(() => {
  const arr = [1, 2, 3]
  person.list = arr;
  console.log(person);
  
},1000)
```



### readonly

> 拷贝一份proxy对象将其设置为只读

```js
import { reactive ,readonly} from 'vue'
const person = reactive({count:1})
const copy = readonly(person)
 
 //person.count++
 //IDE和browser中都会有所警报
 copy.count++
```



### shallowReactive

只能对浅层数据有效 = =

```vue
<template>
  <div>
    <div>{{ state }}</div>
    <button @click="change1">test1</button>
    <button @click="change2">test2</button>
  </div>
</template>
 
 
 
<script setup lang="ts">
import { shallowReactive } from 'vue'
 
 
const obj = {
  a: 1,
  first: {
    b: 2,
    second: {
      c: 3
    }
  }
}
 
const state = shallowReactive(obj)
 
//页面视图会及时更新
function change1() {
  state.a = 7
}
  // 深层 视图不会更新 = =
function change2() {
  state.first.b = 8
  state.first.second.c = 9
  console.log(state);
}
</script> 
<style>
</style>
```

​	

## 4.Vue3.0中的响应式原理

### vue2.x的响应式

- 实现原理：

  - 对象类型：通过```Object.defineProperty()```对属性的读取、修改进行拦截（数据劫持）。

  - 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹）。

    ```js
    Object.defineProperty(data, 'count', {
        get () {}, 
        set () {}
    })
    ```

- 存在问题：

  - 新增属性、删除属性, 界面不会更新。
  - 直接通过下标修改数组, 界面不会自动更新。

### Vue3.0的响应式

- 实现原理: 

  - 通过Proxy（代理）:  拦截对象中任意属性的变化, 包括：属性值的读写、属性的添加、属性的删除等。

  - 通过Reflect（反射）:  对源对象的属性进行操作。

  - MDN文档中描述的Proxy与Reflect：

    - Proxy：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy

    - Reflect：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

      ```js
      new Proxy(data, {
      	// 拦截读取属性值
          get (target, prop) {
          	return Reflect.get(target, prop)
          },
          // 拦截设置属性值或添加新属性
          set (target, prop, value) {
          	return Reflect.set(target, prop, value)
          },
          // 拦截删除属性
          deleteProperty (target, prop) {
          	return Reflect.deleteProperty(target, prop)
          }
      })
      
      proxy.name = 'tom'   
      ```
      
    - handler.get()
    
      ```js
      var p = new Proxy({}, {
        get: function(target, prop, receiver) {
          console.log("called: " + prop);
          return 10;
        }
      });
      
      console.log(p.a); // "called: a"
                        // 10
      ```
    
    - handler.set()
    
      ```js
      var p = new Proxy({}, {
        set: function(target, prop, value, receiver) {
          target[prop] = value;
          console.log('property set: ' + prop + ' = ' + value);
          return true;
        }
      })
      
      console.log('a' in p);  // false
      
      p.a = 10;               // "property set: a = 10"
      console.log('a' in p);  // true
      console.log(p.a);       // 10
      ```
    
    - handler.deleteProperty()
    
    ```js
    var p = new Proxy({}, {
      deleteProperty: function(target, prop) {
        console.log('called: ' + prop);
        return true;
      }
    });
    
    delete p.a; // "called: a"
    ```
    
    

## 5.reactive对比ref

-  从定义数据角度对比：
   -  ref用来定义：<strong style="color:#DD5145">基本类型数据</strong>。
   -  reactive用来定义：<strong style="color:#DD5145">对象（或数组）类型数据</strong>。
   -  备注：ref也可以用来定义<strong style="color:#DD5145">对象（或数组）类型数据</strong>, 它内部会自动通过```reactive```转为<strong style="color:#DD5145">代理对象</strong>。
-  从原理角度对比：
   -  ref通过``Object.defineProperty()``的```get```与```set```来实现响应式（数据劫持）。
   -  reactive通过使用<strong style="color:#DD5145">Proxy</strong>来实现响应式（数据劫持）, 并通过<strong style="color:#DD5145">Reflect</strong>操作<strong style="color:orange">源对象</strong>内部的数据。
-  从使用角度对比：
   -  ref定义的数据：操作数据<strong style="color:#DD5145">需要</strong>```.value```，读取数据时模板中直接读取<strong style="color:#DD5145">不需要</strong>```.value```。
   -  reactive定义的数据：操作数据与读取数据：<strong style="color:#DD5145">均不需要</strong>```.value```。

## 6.setup的两个注意点

- setup执行的时机
  - 在beforeCreate之前执行一次，this是undefined。

- setup的参数
  - props：值为对象，包含：组件外部传递过来，且组件内部声明接收了的属性。
  - context：上下文对象
    - attrs: 值为对象，包含：组件外部传递过来，但没有在props配置中声明的属性, 相当于 ```this.$attrs```。
    - slots: 收到的插槽内容, 相当于 ```this.$slots```。
    - emit: 分发自定义事件的函数, 相当于 ```this.$emit```。


## 7.计算属性与监视

### 1.computed函数

- 与Vue2.x中computed配置功能一致

- 写法

  ```js
  	xxxxxxxxxx 
      import {computed} from 'vue'setup()
  {    ...    
  //计算属性——简写   
  let fullName = computed(()=>{        
      return person.firstName + '-' + person.lastName  
  }) 
   //计算属性——完整  
   let fullName = computed({        
       get(){         
           return person.firstName + '-' + person.lastName    
       },       
       set(value){           
           const nameArr = value.split('-')            
         
      person.firstName = nameArr[0]        
           person.lastName = nameArr[1]  
       }  
   })}
  ```

### 2.watch函数

- 与Vue2.x中watch配置功能一致

- 两个小“坑”：

  - 监视reactive定义的响应式数据时：oldValue无法正确获取、强制开启了深度监视（deep配置失效）。
  - 监视reactive定义的响应式数据中某个属性时：deep配置有效。

  ```js
  //情况一：监视ref定义的响应式数据
  watch(sum,(newValue,oldValue)=>{
  	console.log('sum变化了',newValue,oldValue)
  },{immediate:true})
  
  //情况二：监视多个ref定义的响应式数据
  watch([sum,msg],(newValue,oldValue)=>{
  	console.log('sum或msg变化了',newValue,oldValue)
  }) 
  
  /* 情况三：监视reactive定义的响应式数据
  			若watch监视的是reactive定义的响应式数据，则无法正确获得oldValue！！
  			若watch监视的是reactive定义的响应式数据，则强制开启了深度监视 
  */
  watch(person,(newValue,oldValue)=>{
  	console.log('person变化了',newValue,oldValue)
  },{immediate:true,deep:false}) //此处的deep配置不再奏效
  
  //情况四：监视reactive定义的响应式数据中的某个属性
  watch(()=>person.job,(newValue,oldValue)=>{
  	console.log('person的job变化了',newValue,oldValue)
  },{immediate:true,deep:true}) 
  
  //情况五：监视reactive定义的响应式数据中的某些属性
  watch([()=>person.job,()=>person.name],(newValue,oldValue)=>{
  	console.log('person的job变化了',newValue,oldValue)
  },{immediate:true,deep:true})
  
  //特殊情况
  watch(()=>person.job,(newValue,oldValue)=>{
      console.log('person的job变化了',newValue,oldValue)
  },{deep:true}) //此处由于监视的是reactive所定义的对象中的某个属性，所以deep配置有效
  ```

### 3.watchEffect函数

- watch的套路是：既要指明监视的属性，也要指明监视的回调。

- watchEffect的套路是：不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性。

  - watchEffect有点像computed：
  - 但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值。
  - 而watchEffect更注重的是过程（回调函数的函数体），所以不用写返回值。

  ```js
  //watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
  watchEffect(()=>{
      const x1 = sum.value
      const x2 = person.age
      console.log('watchEffect配置的回调执行了')
  })
  ```
  
  ### oninvalidation函数
  
  > 他会在监听事件出发前执行 您可以在这里进行一些减少监听资源频繁触发的操作
  
  ```js
  import { watchEffect, ref } from 'vue'
  let message = ref<string>('')
  let message2 = ref<string>('')
   watchEffect((oninvalidate) => {
      //console.log('message', message.value);
       //在此处进行一些清除操作 或者防抖 节流操作 防止频繁监听消耗资源
      oninvalidate(()=>{
          
      })
      console.log('message2', message2.value);
  })
  ```
  
  ### 显式调用返回值以停止侦听
  
  > 通常来说，当 `watchEffect` 在组件的 [setup()](https://v3.cn.vuejs.org/guide/composition-api-setup.html) 函数或[生命周期钩子](https://v3.cn.vuejs.org/guide/composition-api-lifecycle-hooks.html)被调用时，侦听器会被链接到该组件的生命周期，并在组件卸载时自动停止。
  
  *但这并不是唯一选择，我们可以通过显式调用返回值停止侦听*
  
  ```js
  const stop = watchEffect(() => {
    /* ... */
  })
  
  // later
  stop()
  ```
  
  
  
  

### 4.onTrack与onTrigger函数

> 帮助我们调试



```js
const plusOne = computed(() => count.value + 1, {
  onTrack(e) {
    // 当 count.value 作为依赖被追踪时触发
    debugger
  },
  onTrigger(e) {
    // 当 count.value 被修改时触发
    debugger
  }
})
// 访问 plusOne，应该触发 onTrack
console.log(plusOne.value)
// 修改 count.value，应该触发 onTrigger
count.value++
```

**`onTrack` 和 `onTrigger` 仅在开发模式下生效。**



## 8.生命周期

https://v3.cn.vuejs.org/images/lifecycle.svg

<div style="border:1px solid black;width:380px;float:left;margin-right:20px;"><strong>vue2.x的生命周期</strong><img src="https://cn.vuejs.org/images/lifecycle.png" alt="lifecycle_2" style="zoom:33%;width:1200px" /></div><div style="border:1px solid black;width:510px;height:985px;float:left"><strong>vue3.0的生命周期</strong><img src="https://v3.cn.vuejs.org/images/lifecycle.svg" alt="lifecycle_2" style="zoom:33%;width:2500px" /></div>





































1

- Vue3.0中可以继续使用Vue2.x中的生命周期钩子，但有有两个被更名：
  - ```beforeDestroy```改名为 ```beforeUnmount```
  - ```destroyed```改名为 ```unmounted```
- Vue3.0也提供了 Composition API 形式的生命周期钩子，与Vue2.x中钩子对应关系如下：
  - `beforeCreate`===>`setup()`
  - `created`=======>`setup()`
  - `beforeMount` ===>`onBeforeMount`
  - `mounted`=======>`onMounted`
  - `beforeUpdate`===>`onBeforeUpdate`
  - `updated` =======>`onUpdated`
  - `beforeUnmount` ==>`onBeforeUnmount`
  - `unmounted` =====>`onUnmounted`

## 9.自定义hook函数

- 什么是hook？—— 本质是一个函数，把setup函数中使用的Composition API进行了封装。

- 类似于vue2.x中的mixin。

- 自定义hook的优势: 复用代码, 让setup中的逻辑更清楚易懂。



## 10.toRef

- 作用：创建一个 ref 对象，其value值指向另一个对象中的某个属性。
- 语法：```const name = toRef(person,'name')```
- 应用:   要将响应式对象中的某个属性单独提供给外部使用时。


- 扩展：```toRefs``` 与```toRef```功能一致，但可以批量创建多个 ref 对象，语法：```toRefs(person)```


# 三、其它 Composition API

## 1.shallowReactive 与 shallowRef

- shallowReactive：只处理对象最外层属性的响应式（浅响应式）。
- shallowRef：只处理基本数据类型的响应式, 不进行对象的响应式处理。

- 什么时候使用?
  -  如果有一个对象数据，结构比较深, 但变化时只是外层属性变化 ===> shallowReactive。
  -  如果有一个对象数据，后续功能不会修改该对象中的属性，而是生新的对象来替换 ===> shallowRef。

## 2.readonly 与 shallowReadonly

- readonly: 让一个响应式数据变为只读的（深只读）。
- shallowReadonly：让一个响应式数据变为只读的（浅只读）。
- 应用场景: 不希望数据被修改时。

## 3.toRaw 与 markRaw

- toRaw：
  - 作用：将一个由```reactive```生成的<strong style="color:orange">响应式对象</strong>转为<strong style="color:orange">普通对象</strong>。
  - 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新。
- markRaw：
  - 作用：标记一个对象，使其永远不会再成为响应式对象。
  - 应用场景:
    1. 有些值不应被设置为响应式的，例如复杂的第三方类库等。
    2. 当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能。

## 4.customRef

- 作用：创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。

- 实现防抖效果：

  ```vue
  <template>
  	<input type="text" v-model="keyword">
  	<h3>{{keyword}}</h3>
  </template>
  
  <script>
  	import {ref,customRef} from 'vue'
  	export default {
  		name:'Demo',
  		setup(){
  			// let keyword = ref('hello') //使用Vue准备好的内置ref
  			//自定义一个myRef
  			function myRef(value,delay){
  				let timer
  				//通过customRef去实现自定义
  				return customRef((track,trigger)=>{
  					return{
  						get(){
  							track() //告诉Vue这个value值是需要被“追踪”的
  							return value
  						},
  						set(newValue){
  							clearTimeout(timer)
  							timer = setTimeout(()=>{
  								value = newValue
  								trigger() //告诉Vue去更新界面
  							},delay)
  						}
  					}
  				})
  			}
  			let keyword = myRef('hello',500) //使用程序员自定义的ref
  			return {
  				keyword
  			}
  		}
  	}
  </script>
  ```

  

## 5.provide 与 inject

<img src="https://v3.cn.vuejs.org/images/components_provide.png" style="width:300px" />

- 作用：实现<strong style="color:#DD5145">祖与后代组件间</strong>通信

- 套路：父组件有一个 `provide` 选项来提供数据，后代组件有一个 `inject` 选项来开始使用这些数据

- 具体写法：

  1. 祖组件中：

     ```js
     setup(){	......    let car = reactive({name:'奔驰',price:'40万'})    provide('car',car)    ......}
     ```

  2. 后代组件中：

     ```js
     setup(props,context){	......    const car = inject('car')    return {car}	......}
     ```

## 6.响应式数据的判断

- isRef: 检查一个值是否为一个 ref 对象
- isReactive: 检查一个对象是否是由 `reactive` 创建的响应式代理
- isReadonly: 检查一个对象是否是由 `readonly` 创建的只读代理
- isProxy: 检查一个对象是否是由 `reactive` 或者 `readonly` 方法创建的代理

# 四、Composition API 的优势

## 1.Options API 存在的问题

使用传统OptionsAPI中，新增或者修改一个需求，就需要分别在data，methods，computed里修改 。

<div style="width:600px;height:370px;overflow:hidden;float:left">
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f84e4e2c02424d9a99862ade0a2e4114~tplv-k3u1fbpfcp-watermark.image" style="width:600px;float:left" />
</div>
<div style="width:300px;height:370px;overflow:hidden;float:left">
    <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5ac7e20d1784887a826f6360768a368~tplv-k3u1fbpfcp-watermark.image" style="zoom:50%;width:560px;left" /> 
</div>
















## 2.Composition API 的优势

我们可以更加优雅的组织我们的代码，函数。让相关功能的代码更加有序的组织在一起。

<div style="width:500px;height:340px;overflow:hidden;float:left">
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc0be8211fc54b6c941c036791ba4efe~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>
<div style="width:430px;height:340px;overflow:hidden;float:left">
    <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6cc55165c0e34069a75fe36f8712eb80~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>














# 五、新的组件

## 1.Fragment

- 在Vue2中: 组件必须有一个根标签
- 在Vue3中: 组件可以没有根标签, 内部会将多个标签包含在一个Fragment虚拟元素中
- 好处: 减少标签层级, 减小内存占用

## 2.Teleport

- 什么是Teleport？—— `Teleport` 是一种能够将我们的<strong style="color:#DD5145">组件html结构</strong>移动到指定位置的技术。

  ```vue
  <teleport to="移动位置">
      <div v-if="isShow" class="mask">	
          <div class="dialog">	
              <h3>我是一个弹窗</h3>	
              <button @click="isShow = false">关闭弹窗</button>		
          </div>	
      </div>
  </teleport>
  ```

## 3.Suspense

- 等待异步组件时渲染一些额外内容，让应用有更好的用户体验

- 使用步骤：

  - 异步引入组件

    ```js
    import {defineAsyncComponent} from 'vue'const Child = defineAsyncComponent(()=>import('./components/Child.vue'))
    ```

  - 使用```Suspense```包裹组件，并配置好```default``` 与 ```fallback```

    ```vue
    <template>	<div class="app">		<h3>我是App组件</h3>		<Suspense>			<template v-slot:default>				<Child/>			</template>			<template v-slot:fallback>				<h3>加载中.....</h3>			</template>		</Suspense>	</div></template>
    ```

# 六、其他

## 1.全局API的转移

- Vue 2.x 有许多全局 API 和配置。

  - 例如：注册全局组件、注册全局指令等。

    ```js
    //注册全局组件
    Vue.component('MyButton', {  data: () => ({    count: 0  }),  template: '<button @click="count++">Clicked {{ count }} times.</button>'})//注册全局指令Vue.directive('focus', {  inserted: el => el.focus()}
    ```

- Vue3.0中对这些API做出了调整：

  - 将全局的API，即：```Vue.xxx```调整到应用实例（```app```）上

    | 2.x 全局 API（```Vue```） | 3.x 实例 API (`app`)                        |
    | ------------------------- | ------------------------------------------- |
    | Vue.config.xxxx           | app.config.xxxx                             |
    | Vue.config.productionTip  | <strong style="color:#DD5145">移除</strong> |
    | Vue.component             | app.component                               |
    | Vue.directive             | app.directive                               |
    | Vue.mixin                 | app.mixin                                   |
    | Vue.use                   | app.use                                     |
    | Vue.prototype             | app.config.globalProperties                 |

## 2.其他改变

- data选项应始终被声明为一个函数。

- 过度类名的更改：

  - Vue2.x写法

    ```css
    .v-enter,.v-leave-to {  opacity: 0;}.v-leave,.v-enter-to {  opacity: 1;}
    ```

  - Vue3.x写法

    ```css
    .v-enter-from,.v-leave-to {  opacity: 0;}.v-leave-from,.v-enter-to {  opacity: 1;}
    ```

- <strong style="color:#DD5145">移除</strong>keyCode作为 v-on 的修饰符，同时也不再支持```config.keyCodes```

- <strong style="color:#DD5145">移除</strong>```v-on.native```修饰符

  - 父组件中绑定事件

    ```vue
    <my-component  v-on:close="handleComponentEvent"  v-on:click="handleNativeClickEvent"/>
    ```

  - 子组件中声明自定义事件

    ```vue
    <script>  export default {    emits: ['close']  }</script>
    ```

- <strong style="color:#DD5145">移除</strong>过滤器（filter）

  > 过滤器虽然这看起来很方便，但它需要一个自定义语法，打破大括号内表达式是 “只是 JavaScript” 的假设，这不仅有学习成本，而且有实现成本！建议用方法调用或计算属性去替换过滤器。

- ......

# Vue 项目实战

## loadsh插件防抖和节流

> 在进行窗口的resize、scroll，输入框内容校验等操作时，如果事件处理函数调用的频率无限制，会加重浏览器的负担，导致用户体验非常糟糕。此时我们可以采用debounce（防抖）和throttle（节流）的方式来减少调用频率，同时又不影响实际效果。
> 安装lodash插件，该插件提供了防抖和节流的函数，我们可以引入js文件，直接调用。当然也可以自己写防抖和节流的函数

----------------

**概要：**

防抖：用户操作很频繁，但是只执行一次，减少业务负担。
节流：用户操作很频繁，但是把频繁的操作变为少量的操作，使浏览器有充分时间解析代码
防抖和节流简述
例如：下面代码就是将changeIndex设置了节流，如果操作很频繁，限制50ms执行一次。这里函数定义采用的键值对形式。throttle的返回值就是一个函数，所以直接键值对赋值就可以，函数的参数在function中传入即可。

```js

import {throttle} from 'lodash'

 methods: {
    //鼠标进入修改响应元素的背景颜色
    //采用键值对形式创建函数，将changeIndex定义为节流函数，该函数触发很频繁时，设置50ms才会执行一次
    changeIndex: throttle(function (index){
      this.currentIndex = index
    },50),
    //鼠标移除触发时间
    leaveIndex(){
      this.currentIndex = -1
    }
  }

```

## 编程式导航+事件委托实现路由跳转

![](https://img-blog.csdnimg.cn/5e9580e64f6f4b6c9f6f24837470d966.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5q-b5q-b6Jmr5ZGc5ZGc,size_20,color_FFFFFF,t_70,g_se,x_16)

如上图所示，三级标签列表有很多，每一个标签都是一个页面链接，我们要实现通过点击表现进行路由跳转。
路由跳转的两种方法：导航式路由，编程式路由。

对于导航式路由，我们有多少个a标签就会生成多少个router-link标签，这样当我们频繁操作时会出现卡顿现象。
对于编程式路由，我们是通过触发点击事件实现路由跳转。同理有多少个a标签就会有多少个触发函数。虽然不会出现卡顿，但是也会影响性能。

上面两种方法无论采用哪一种，都会影响性能。我们提出一种：编程时导航+事件委派 的方式实现路由跳转。事件委派即把子节点的触发事件都委托给父节点。这样只需要一个回调函数goSearch就可以解决。

## mockjs

> 没什么用的垃圾技术

```jsfmo
// 使用 Mock
var Mock = require('mockjs')
var data = Mock.mock({
    // 属性 list 的值是一个数组，其中含有 1 到 10 个元素
    'list|1-10': [{
        // 属性 id 是一个自增数，起始值为 1，每次增 1
        'id|+1': 1
    }]
})
// 输出结果
console.log(JSON.stringify(data, null, 4))
```

## Swiper

> 移动端和网页端都要使用的轮播图

引入

引入Swiper样式

````js
  // Import Swiper Vue.js components
  import { Swiper, SwiperSlide } from 'swiper/vue';

  // Import Swiper styles
  import 'swiper/css';
````



创建Swiper实例在中



# Vue3项目

关闭ESlint方法

```
找到文件 vue.config.js （如果没有这个文件就在根目录上创建一个）
将lintOnSave属性设置为false，如果没有就自己写个进去。

module.exports = {
lintOnSave: false
}
```





## 引入SASS

在 Vue 專案中，你可能會需要另外使用 SCSS 資料夾來管理所有 SCSS 檔案。有四種方式可以引入使用：

- 引入於個別 Component
- 引入於 App.vue
- 引入於 main.js
- 引入於 vue.config.js (使用 webpack 引入)

那麼這四種方法，有什麼差別？該使用哪一種呢？

### ➤ 開始之前，準備一下檔案們

- Vue CLI 環境
  建構 Vue CLI 環境時，記得勾選 CSS Pre-processors 使用 CSS 預處理器

![img](https://miro.medium.com/max/1400/1*D2IB80CzwsqobaTZ3nXN_g.png)

- 新增 SCSS 檔案，並加入一個變數
  assets 底下新增一個 scss 資料夾，用以存放所有 SCSS 檔案。 scss 資料夾下新增一個 SCSS 檔案 main.scss。main.scss 檔案裡頭新增一個變數 `$mainColor:#cd6798;`目前看起來會是這樣子：

![img](https://miro.medium.com/max/1132/1*jUF4IZe06egZADeuhtx2_w.png)

準備好檔案後，我們一個一個方法來看看吧。

### ➤ 引入於個別 Component

依照需求，我們可單獨引入至需要的 Component，以 Vue CLI 的範例 Component HelloWorld 為例，我們可以引入並使用它。

![img](https://miro.medium.com/max/1400/1*xev0dSOGcqCDVAetBEPfpA.png)

### ➤ 引入於 App.vue

引入於 App.vue 與前述引入於個別 Component，其實是一樣的概念。只是通常 App.vue 在 style 的使用上，不會加上 scoped 參數。因此這邊的 CSS 會影響到全局。

這邊有另一個思考上的小陷阱，我們將 SCSS 檔案引入至 App.vue，並不等同於，其他的 Component 皆可以使用 SCSS 變數。

舉個例子，將 SCSS 檔案引入於 App.vue，並使用。

![img](https://miro.medium.com/max/1400/1*F_l0KhFD4QwtxhH4QcT7xQ.png)

- 設定 h1、h2 tag 顏色為變數 mainColor，整體專案中的 h1、h2 皆會受到影響。
- 另一個 Component (例如：HelloWorld)無法因此而能夠使用 SCSS 變數。App.vue 因未設置 scoped 參數，可以全局共享 CSS。關鍵是共享的是 CSS，而非共享 SCSS。

### ➤ 引入於 main.js（主要）

引入於與前述引入於 App.vue，基本上是一樣的概念。引入於 main.js 的 SCSS 檔案，會全局影響。但一樣是編譯後的 CSS 影響，非全局引入 SCSS 的概念。

因此當我們這樣做的時候：

![img](https://miro.medium.com/max/1400/1*yoKOkVOScJQFbeJwdIFiYg.png)

在目前的範例中，SCSS 檔案裡面只有顏色變數，無法編譯出有意義的 CSS 檔案。

因此我們也可以整理出一個思緒：放進 App.vue 及 main.js 的 SCSS 檔案，因具備有共享 CSS 的意義，我們可以將 reset 檔案、第三方套件修改檔案等放在這兩個地方，達到全局影響。

### ➤ 引入於 vue.config.js (使用 webpack 引入 主要)

引入於 vue.config.js，即為使用 webpack 達成全局引入，可傳遞共享的全局變數給所有的 Component。

- 首先安裝我們需要的 loader

```
npm install --save-dev node-sass sass-loader
```

- 新增 `vue.config.js` 檔案，用以更動 webpack 設定

```
module.exports = {
  css: {
    loaderOptions: {
      sass: {
        prependData: `
           @import "@/assets/scss/main.scss";
        `
      }
    }
  }
};
```

💡 這邊要注意你的 sass-loader 版本，若為 v7 舊版的話，需要這樣寫：

```
module.exports = {
  css: {
    loaderOptions: {
      sass: {
        data: `
           @import "@/assets/scss/main.scss";
        `
      }
    }
  }
};
```

兩者只差在 prependData 與 data，可參考文件([點我查看](https://cli.vuejs.org/guide/css.html#passing-options-to-pre-processor-loaders))

- 透過 webpack 全局引入，引入的是 SCSS 資源，我們可以在任一個 component 中使用變數。

⚠️ 要注意的是，使用 webpack 全局引入，相當於每個 component 都引入了，要注意程式碼重複引入編譯的問題。這邊較適合引入變數等SCSS 資源。

### ➤ 結論

綜合以上得以下結論：

- 引入於個別 Component
  按照需求引入，通常只有幾個 component 需要使用。因此不考慮全局。
- 引入於 App.vue
  與引入 main.js 概念相同，引入檔案，會共享 CSS 至全局。適合 reset、第三方套件修改檔案等。
- 引入於 main.js
  與引入 App.vue 概念相同，引入檔案，會共享 CSS 至全局。適合 reset、第三方套件修改檔案等。筆者較喜歡引入於 main.js，可透由 main.js 得知整體專案引入了哪些共同內容。
- 引入於 vue.config.js (使用 webpack 引入)
  共享 SCSS 資源。適合由此引入全局想共用的 SCSS 資源，例如變數、mixin 等。

在進入框架後，以往學習的 SCSS 資料夾管理方式，可能會被 Component 概念取代，SCSS 的使用情境可能會逐漸變小，不過筆者認為目前的 SCSS 變數對於整體專案管理還是很好用的👍

以上是今天的小小分享，程式碼可以看這邊：[GitHub](https://github.com/UnaLai/vuecli_scss) ，有任何問題都歡迎留言指教，謝謝 😊