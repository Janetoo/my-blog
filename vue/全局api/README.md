# Vue基础知识

vue.js——开发版本：包含完整的警告和调试模式
vue.min.js——生产版本：删除了警告，进行了压缩

#### 1、全局api
全局API并不在构造器里，而是先声明全局变量或者直接在Vue上定义一些新功能，Vue内置了一些全局API。说的简单些就是，在构造器外部用Vue提供给我们的API函数来定义新的功能。

##### 1.1 Vue.directive 

除了Vue提供的内部指令，还可以根据Vue提供的全局api来定义一些属于自己的指令。比如定义一个change指令，作用就是让文字颜色变成红色。

demo：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue.directive 自定义指令</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>Vue.directive 自定义指令</h1>
    <hr>
    <div id="app">
        <div v-change="color">{{num}}</div>
        <p><button @click="add">ADD</button></p>
    </div>

    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                num: 10
            },
            methods: {
                add: function() {
                    this.num++
                }
            }
        })
    </script>
</body>
</html>
```
接下来定义一个全局指令change让文字变色，这里使用Vue.directive();来定义全局指令：
```
//如果在 bind 和 update 时触发相同行为，而不关心其它的钩子，可以简写成这种形式
Vue.directive('change',function(el,binding,vnode, oldVnode){
        el.style='color:'+binding.value;
});
```
使用声明周期钩子：
```
Vue.directive("change", {
    bind:function(el, binding) {//被绑定
        console.log('1 - bind')

        el.style="color:"+binding.value
    },
    inserted: function() {//绑定到节点
        console.log('2 - inserted')
    },
    update:function(){//组件更新
        console.log('3 - update');
    },
    componentUpdated:function(){//组件更新完成
        console.log('4 - componentUpdated');
    },
    unbind:function(){//解绑
        console.log('5 - unbind');
    }
})
```
写完之后可以看到数字已经变成了绿色，说明自定义指令起到了作用。

**自定义指令中传递的四个个参数：**

- el: 指令所绑定的元素，可以用来直接操作DOM。

- binding: 一个对象，包含指令的很多信息。

- vnode：Vue 编译生成的虚拟节点。

- oldVnode：上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。

**自定义指令的生命周期：**

自定义指令有五个生命周期（也叫钩子函数），分别是 bind,inserted,update,componentUpdated,unbind

1. bind:只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个绑定时执行一次的初始化动作。
2. inserted:被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于document中）。
3. update:被绑定于元素所在的模板更新时调用，而无论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新。
4. componentUpdated:被绑定元素所在模板完成一次更新周期时调用。
5. unbind:只调用一次，指令与元素解绑时调用。

```
bind:function(){//被绑定
     console.log('1 - bind');
},
inserted:function(){//绑定到节点
      console.log('2 - inserted');
},
update:function(){//组件更新
      console.log('3 - update');
},
componentUpdated:function(){//组件更新完成
      console.log('4 - componentUpdated');
},
unbind:function(){//解绑
      console.log('5 - unbind');
}
```

##### 1.2 Vue.extend

Vue.extend 返回的是一个“扩展实例构造器”,也就是预设了部分选项的Vue实例构造器。经常服务于Vue.component用来生成组件，可以简单理解为当在模板中遇到该组件名称作为标签的自定义元素时，会自动调用“扩展实例构造器”来生产组件实例，并挂载到自定义元素上。

扩展实例demo：
```
//Vue.extend 返回的是一个“扩展实例构造器”，也就是一个预设了部分选项的 Vue 实例构造器
var authorExtend = Vue.extend({
    template: "<p><a :href='authorUrl'>{{authorName}}</a></p>",
    data: function() {
        return {
            authorName: 'zaishuiyixia',
            authorUrl: 'https://www.jianshu.com/u/6d5f3f6492f8'
        }
    }
});
```

想要展示在页面上，还需要把扩展实例挂载到元素上。
```
new authorExtend().$mount('#author');
```
还可以通过HTML标签或者标签的class来生成扩展实例构造器，Vue.extend里的代码是一样的，只是在挂载的时候，我们用类似jquery的选择器的方法，来进行挂载就可以了。
```
new authorExtend().$mount('.extend');
```
完整代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue.extend 扩展实例构造器</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>Vue.extend 扩展实例构造器</h1>
    <hr>
    <div id="author" class="extend"></div>

    <script type="text/javascript">
        //Vue.extend 返回的是一个“扩展实例构造器”，也就是一个预设了部分选项的 Vue 实例构造器
        var authorExtend = Vue.extend({
            template: "<p><a :href='authorUrl'>{{authorName}}</a></p>",
            data: function() {
                return {
                    authorName: 'zaishuiyixia',
                    authorUrl: 'https://www.jianshu.com/u/6d5f3f6492f8'
                }
            }
        });

        //调用 new authorExtend 构造函数来生成组件实例，并挂载到元素上
        new authorExtend().$mount('#author');

        //调用 new authorExtend 构造函数来生成组件实例，并挂载到元素上
        // var vm = new authorExtend({
        //  el: '#author'
        // })
    </script>
</body>
</html>
```
##### 1.3 Vue.set
当一个 Vue 实例被创建时，它向 Vue 的响应式系统中加入了其 data 对象中能找到的所有的属性。当这些属性的值发生改变时，视图将会产生“响应”，即匹配更新为新的值。
```
// 我们的数据对象
var data = { a: 1 }

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 获得这个实例上的属性
// 返回源数据中对应的字段
vm.a == data.a // => true

// 设置属性也会影响到原始数据
vm.a = 2
data.a // => 2

// ……反之亦然
data.a = 3
vm.a // => 3
```
当这些数据改变时，视图会进行重渲染。值得注意的是只有当实例被创建时 data 中存在的属性才是响应式的。也就是说如果你添加一个新的属性，比如：
```
vm.b = 'hi'
```
那么对 b 的改动将不会触发任何视图的更新。

如果我就是希望新添加的属性也是响应式的，应该怎么办呢？

Vue.set就是来解决这个问题的。

Vue.set 的作用就是向响应式对象中添加一个属性，并确保这个新属性同样是响应式的，且触发视图更新。它主要用于向响应式对象上添加新属性，因为 Vue 无法探测普通的新增属性 。也可以通过Vue.set在构造器外部操作构造器内部的数据、属性或者方法。

什么是外部数据，就是不在Vue构造器里里的data处声明，而是在构造器外部声明，然后在data处引用就可以了。外部数据的加入让程序更加灵活，我们可以在外部获取任何想要的数据形式，然后让data引用：
```
//在构造器外部声明数据
 var outData={
    count:1,
    goodName:'zaishuiyixia'
};
var app=new Vue({
    el:'#app',
    //引用外部数据
    data:outData
})
```
在外部改变数据的三种方法：

1、用Vue.set改变
```
function add(){
       Vue.set(outData,'count',4);
 }
```
2、用Vue对象的方法添加
```
app.count++;
```
3、直接操作外部数据
```
outData.count++;
```

**Vue.set存在的意义：**

由于Javascript的限制，Vue不能自动检测以下变动的数组。

1. 当你利用索引直接设置一个项时，vue不会为我们自动更新。
2. 当你修改数组的长度时，vue不会为我们自动更新。

example:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Vue.set 全局api</title>
</head>
<body>
    <h1>Vue.set 全局api</h1>
    <hr>
    <div id="app">
        <ul>
            <li v-for=" aa in arr">{{aa}}</li>
        </ul>
    </div>
    <button onclick="add()">外部添加</button>
    <script type="text/javascript">
        function add(){
            console.log("我已经执行了");
            app.arr[1]='ddd';
            //Vue.set(app.arr,1,'ddd');
        }

        var outData={
            arr:['aaa','bbb','ccc']
        };
        
        var app=new Vue({
            el:'#app',
            data:outData
        })
    </script>
</body>
</html>
```
这时我们的界面是不会自动跟新数组的，我们需要用Vue.set(app.arr,1,’ddd’)来设置改变，vue才会给我们自动更新，这就是Vue.set存在的意义。

另一个代码例子：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Vue.set 全局api</title>
</head>
<body>
    <h1>Vue.set 全局api</h1>
    <hr>
    <div id="app">
        <ul>
            <li v-for=" aa in arr">{{aa}}</li>
        </ul>
        <hr>
         <p v-for="item in items" :key="item.id">
            {{item.message}}
        </p>
        <!--@click等价于v-on:click-->
        <button class="btn" @click="btn1Click()">点我试试</button>
        <br/>
    </div>
    <button onclick="add()">外部添加</button>
    <script type="text/javascript">
        function add(){
            console.log("我已经执行了");
            app.arr[1]='ddd';
            //Vue.set(app.arr,1,'ddd');
        }

        var outData={
            arr:['aaa','bbb','ccc'],
            items:[
                {message:"Test one",id:"1"},
                {message:"Test two",id:"2"},
                {message:"Test three",id:"3"}
            ]
        };

        var app=new Vue({
            el:'#app',
            data:outData,
            methods:{
                btn1Click:function(){
                    //为data中的items动态新增一条数据
                    this.items.push({message:"动态新增"});
                }
            }
        })
    </script>
</body>
</html>
```
点击之后试图更新，列表新增了一条数据，通过数组的变异方法（ Vue数组变异方法：push()、pop()、shift()、unshift()、splice()、sort()、reverse() ）我们可以动态控制数据的增减，但是却无法做到对某一条数据的修改。这时候就也会用到Vue的内置方法Vue.set。

**Vue.set( target, key, value ) 响应式新增与修改数据：**

1. target：{Object | Array} 要更改的数据源(可以是对象或者数组)
2. key：{Object | Array} 要更改的具体数据
3. value ：重新赋的值

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Vue.set 全局api</title>
</head>
<body>
    <h1>Vue.set 全局api</h1>
    <hr>
    <div id="app">
        <ul>
            <li v-for=" aa in arr">{{aa}}</li>
        </ul>
        <button onclick="add()">外部添加</button>
        <br/>
        <hr>
         <p v-for="item in items" :key="item.id">
            {{item.message}}
        </p>
        <!--@click等价于v-on:click-->
        <button class="btn" @click="btn1Click()">点我试试</button>
        <br/>
        <br/>
        <button class="btn" @click="btn2Click()">动态赋值</button>
        <br/>
        <br/>
        <button class="btn" @click="btn3Click()">为data新增属性</button>
        <br/>
        <br/>
        <button class="btn" @click="btn4Click()">通过数组下表修改数据</button>
    </div>
    <script type="text/javascript">
        function add(){
            console.log("我已经执行了");
            app.arr[1]='ddd';
            //Vue.set(app.arr,1,'ddd');
        }

        var outData={
            arr:['aaa','bbb','ccc'],
            items:[
                {message:"Test one",id:"1"},
                {message:"Test two",id:"2"},
                {message:"Test three",id:"3"}
            ]
        };

        var app=new Vue({
            el:'#app',
            data:outData,
            methods:{
                btn1Click:function(){
                    //为data中的items动态新增一条数据
                    this.items.push({message:"动态新增"});
                    console.log('btn1Click', this.items)
                },
                btn2Click:function(){
                    //Vue methods中的this 指向的是Vue的实例，这里可以直接在this中找到items
                    Vue.set(this.items,0,{message:"Change Test",id:'10'});
                    console.log('btn2Click', this.items)
                },
                btn3Click:function(){
                    var itemLen=this.items.length;
                    Vue.set(this.items,itemLen,{message:"Test add attr",id:itemLen});
                    console.log('btn3Click', this.items)
                },
                btn4Click:function(){
                    this.items[0]={message:"Change Test",id:'10'},
                    console.log('btn4Click', this.items)
                }
            }
        })
    </script>
</body>
</html>
```
点击第一个按钮后新增了一条数据，此时点击第二个按钮将Test one更改为Change Test。

可以看到列表中第一列的Test one已经变成了Change Test。

当写惯了JS之后，有可能想改数组中某个下标的中的数据我直接this.items[XX]就改了，如：
```
btn4Click:function(){
    this.items[0]={message:"Change Test",id:'10'}
}
```
点击btn4Click按钮时，通过打印出的数据可以看到，虽然数据已经改变了，但是视图没有更新。这是因为由于 JavaScript 的限制，当通过数组下标直接修改数据时，Vue 不能检测出数据的改变，所以当我们需要动态改变数据的时候，可以使用Vue.set()。
```
btn3Click:function(){
    var itemLen=this.items.length;
    Vue.set(this.items,itemLen,{message:"Test add attr",id:itemLen});
    console.log('btn3Click', this.items)
}
```
点击btn3Click按钮可以看到视图更新并且新增了一条数据。

Vue.set()不光能修改数据，还能添加数据，弥补了Vue数组变异方法的不足。

>Vue.set()在methods中也可以写成this.$set()

##### 1.4 Vue的生命周期

Vue实例有一个完整的生命周期，也就是从开始创建、初始化数据、编译模板、挂载Dom、渲染→更新→渲染、销毁等一系列过程，我们称这是Vue的生命周期。通俗说就是Vue实例从创建到销毁的过程，就是生命周期。

每一个组件或者实例都会经历一个完整的生命周期，可以大致分为三个阶段：初始化、运行中、销毁。

Vue一共有10个生命周期函数，我们可以利用这些函数在vue的每个阶段执行一些操作，例如操作数据或者改变内容。

1. beforeCreate：实例、组件通过new Vue() 创建出来之后会初始化生命周期，然后就会执行beforeCreate钩子函数，这个时候，数据还没有挂载呢，只是一个空壳，无法访问到数据和真实的dom，一般不做操作。

2. created：接下来初始化数据data，绑定事件（methods里面定义的方法），然后执行created函数，这个时候已经可以使用到数据，也可以更改数据,在这里更改数据不会触发updated函数，在这里可以在渲染前倒数第二次更改数据的机会，不会触发其他的钩子函数，一般可以在这里做(请求数据)初始数据的获取。

3. beforeMount： 接下来开始找实例或者组件对应的模板，编译模板为虚拟dom放入到render函数中准备渲染，然后执行beforeMount钩子函数，在这个函数中虚拟dom已经创建完成，马上就要渲染,在这里也可以更改数据，不会触发updated，在这里可以在渲染前最后一次更改数据的机会，不会触发其他的钩子函数，在这里做初始数据的更改，也可以做初始数据的获取。

4. mounted：接下来开始render，渲染出真实dom，然后执行mounted钩子函数，此时，组件已经出现在页面中，数据、真实dom都已经处理好了,事件都已经挂载好了，可以在这里操作真实dom等事情...

5. beforeUpdate：当组件或实例的数据更改之后，会立即执行beforeUpdate，然后vue的虚拟dom机制会重新构建虚拟dom与上一次的虚拟dom树利用diff算法进行对比之后重新渲染，一般不做什么事儿。

6. updated：当数据更新完成，重新渲染完成后，执行updated，这是数据已经更改完成，dom也重新render完成，可以操作更新后的真实dom。

7. activated：搭配keep-alive使用。keep-alive是Vue的内置组件，能在组件切换过程中将状态保留在内存中，防止切换回组件后重复渲染DOM。一般搭配路由或者组件使用，作用是路由或组件的内容被加载过一次之后，放到内存之中，下一此再进这个路由或者切换回这个组件的时候就不用重新渲染这个组件了，继而也就不会重新执行钩子函数，也不会有像发送请求再次获取数据这样的操作了，只有当数据变化时，才使用VirtualDOM进行diff更新。它会把以前的内容(数据)从内存里拿出来显示，继而不用从新发请求渲染数据了。包裹动态组件时，会缓组件实例，而不是销毁它们。<br><br>
和 `<transition>` 相似，`<keep-alive>` 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。当第一次进入keep-alive 页面的时候，钩子函数的触发顺序是：beforeCreate>created-> mounted-> activated，退出时触发deactivated。当再次进入时，只触发activated。

8. deactivated：与activated生命周期函数相对应的就是deactivated生命周期函数。它会在组件被替换、页面被隐藏(如跳到其他页面)的时候执行。

9. beforeDestroy：当经过某种途径调用$destroy方法后，立即执行beforeDestroy，在组件或实例销毁前执行。一般在这里做一些善后工作，例如清除计时器、清除非指令绑定的事件等等...

10. destroyed：在组件或实例销毁后执行，这时已经解除了组件的数据绑定、指令绑定的事件监听...去掉后只剩下dom空壳，在这里做善后工作也可以。

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>vue的生命周期</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>vue的声明周期</h1>
    <hr>
    <div id="app">
         <p>{{ message }}</p>
         <button @click="add">增加内容</button>
    </div>
    <br>
    <button onclick="app.$destroy()">销毁</button>
    <script type="text/javascript">
        
      var app = new Vue({
            el: '#app',
            data: {
                message : "zaishuiyixia is boy" 
            },
            methods: {
                alert: function () {
                    console.log('events')
                },
                add:function () {
                    this.message += '!'
                }
            },

          //beforeCreate:刚刚创建vue实例，这个时候，数据还没有挂载，只是一个空壳 
           beforeCreate: function () {
               console.group('beforeCreate 创建前状态===============》');
               console.log("%c%s", "color:red" , "el     : " + this.$el); //undefined
               console.log("%c%s", "color:red","data   : " + this.$data); //undefined 
               console.log("%c%s", "color:red","message: " + this.message) //undefined
               console.log('beforeCreate: 刚刚new Vue()之后，这个时候，数据还没有挂载，只是一个空壳')
               console.log('this.$methods',this.$methods) //undefined
               // console.log(this.alert()) // 会报错 this.alert is not a function
            },

            //created:挂载数据，绑定事件(methods里面定义的事件)，这个时候已经可以使用到数据，也可以更改数据,在这里更改数据不会触发updated函数
            created: function () {
                console.group('created 创建完毕状态===============》');
                console.log("%c%s", "color:red","el     : " + this.$el); //undefined
                console.log("%c%s", "color:red","data   : " + this.$data); //已被初始化 
                console.log("%c%s", "color:red","message: " + this.message); //已被初始化
                console.log(this.alert()) //可以执行methods里定义的函数了 初始化事件
                this.message = 'zaishuiyixia is a pretty handsome boy!'
                console.log('更改数据',this.$data.message)
                console.log('在这里可以在渲染前倒数第二次更改数据的机会，不会触发其他的钩子函数，一般可以在这里做初始数据的获取')
                console.log('接下来开始找实例或者组件对应的模板，编译模板为虚拟dom放入到render函数中准备渲染')
            },

            //beforeMount：虚拟dom已经创建完成，马上就要渲染,在这里也可以更改数据，不会触发updated
            beforeMount: function () {
                console.group('beforeMount 挂载前状态===============》');
                console.log("%c%s", "color:red","el     : " + (this.$el)); //已被初始化
                console.log(this.$el); //el已被初始化,是虚拟dom，数据message还没有渲染上去
                console.log("%c%s", "color:red","data   : " + this.$data); //已被初始化  
                console.log("%c%s", "color:red","message: " + this.message); //已被初始化  
                this.message = 'The last change to change data!'
                console.log('最后一次改变数据的机会', this.$data.message)
                console.log('在这里可以在渲染前最后一次更改数据的机会，不会触发其他的钩子函数，一般可以在这里做初始数据的获取')
                console.log('接下来开始render，渲染出真实dom')
            },

            //mounted：此时，数据、真实dom都已经处理好了,dom上的事件也都已经挂载好了
            mounted: function () {
                console.group('mounted 挂载结束状态===============》');
                console.log("%c%s", "color:red","el     : " + this.$el); //已被初始化
                console.log(this.$el);    
                console.log("%c%s", "color:red","data   : " + this.$data); //已被初始化
                console.log("%c%s", "color:red","message: " + this.message); //已被初始化 
                console.log('可以在这里操作真实dom等事情...')
            },

            //beforeUpdate:重新渲染之前触发,当组件或实例的数据更改之后，会立即执行beforeUpdate
            beforeUpdate: function () {        
                console.group('beforeUpdate 更新前状态===============》');
                console.log("%c%s", "color:red","el     : " + this.$el);
                console.log(this.$el);   
                console.log("%c%s", "color:red","data   : " + this.$data); 
                console.log("%c%s", "color:red","message: " + this.message); 
                console.log('在这里不能更改数据，否则会陷入死循环')
                console.log('组件或实例的数据更改之后，会立即执行beforeUpdate，然后vue的虚拟dom机制会重新构建虚拟dom并与上一次的虚拟dom树利用diff算法进行对比之后重新渲染')
            },

            //当更新完成后，执行updated，数据已经更改完成，dom也重新render完成，可以操作更新后的虚拟dom
            updated: function () {
                console.group('updated 更新完成状态===============》');
                console.log("%c%s", "color:red","el     : " + this.$el);
                console.log(this.$el); 
                console.log("%c%s", "color:red","data   : " + this.$data); 
                console.log("%c%s", "color:red","message: " + this.message); 
                console.log('在这里不能更改数据，否则会陷入死循环')
                console.log('当数据更新完成后，执行updated，这时候数据已经更改完成，dom也重新render完成，可以操作更新后的虚拟dom')
            },

            //beforeDestory:销毁前执行（$destroy方法被调用的时候就会执行）,一般在这里做善后处理:清除计时器、清除非指令绑定的事件等等...
            beforeDestroy: function () {
                console.group('beforeDestroy 销毁前状态===============》');
                console.log("%c%s", "color:red","el     : " + this.$el);
                console.log(this.$el);    
                console.log("%c%s", "color:red","data   : " + this.$data); 
                console.log("%c%s", "color:red","message: " + this.message); 
                console.log('beforeDestory:组件或实例销毁前执行（$destroy方法被调用的时候就会立刻执行）,一般在这里做善后处理:清除计时器、清除非指令绑定的事件等等...')
            },

            //destroyed:组件的数据绑定、监听...都去掉了,只剩下dom空壳，这里也可以用来做善后处理
            destroyed: function () {
                console.group('destroyed 销毁完成状态===============》');
                console.log("%c%s", "color:red","el     : " + this.$el);
                console.log(this.$el);  
                console.log("%c%s", "color:red","data   : " + this.$data); 
                console.log("%c%s", "color:red","message: " + this.message)
                console.log('在组件或实例销毁后执行，这是已经解除了组件的数据绑定、指令绑定的事件监听...去掉后只剩下dom空壳，在这里做善后工作也可以')
            }
        })
    </script>
</body>
</html>

```
打开控制台，可以看到刚进入页面的时候会执行beforeCreate、created、beforeMount、mounted这四个生命周期函数。

可以看到在beforeMount阶段打印出的实例的挂载点是虚拟dom，数据还没有挂载上去；在mounted阶段数据才被挂载上去，这时才可以操作真实的dom。

点击增加内容按钮后可以看到执行了beforeUpdate、和updated这两个声明周期函数：

最后点击销毁按钮可以看到执行了beforeDestroy和destroyed这两个生命周期函数：

#### 1.5 template模板标签

>一个字符串模板作为 Vue 实例的标识使用。模板将会替换 挂载的元素。挂载元素的内容都将被忽略，除非模板的内容有分发插槽。

这是[官网](https://cn.vuejs.org/v2/api/#template)给出的template模板标签的作用描述，感觉不是很详细，写几个例子看看它都能做什么。

把template写在挂载元素外面：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Vue模板标签templete</title>
</head>
<body>
    <h1>Vue模板标签templete</h1>
    <hr>
    <div id="app"></div>
    <template>
        <h1 style="color:red">我是Template</h1>
    </template>

    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!'
            },
        })
    </script>
</body>
</html>
```
当把template写在挂载点外面的时候，打开浏览器可以看到在浏览器上并没有渲染出任何信息，这是因为template标签内容天生不可见，设置了display：none；属性。

把template写在挂载元素里面：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Vue模板标签templete</title>
</head>
<body>
    <h1>Vue模板标签templete</h1>
    <hr>
    <div id="app">
        <template>
            <h1 style="color:red">我是Template</h1>
        </template>
    </div>

    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!'
            },
        })
    </script>
</body>
</html>
```
当把template写在挂载点里面的时候，打开浏览器可以看到在浏览器上渲染出了模板，并且最终的渲染结果并没有包含 <template> 元素。

根据渲染结果并没有包含 <template> 元素的这个特性，它可以用来：

1. 把template元素当做一个不可见的包裹元素，用于分组的条件判断：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Vue模板标签templete</title>
</head>
<body>
    <h1>Vue模板标签templete</h1>
    <hr>
    <div id="app">
        <template v-if="show">
            <h1 style="color:red">我是Template</h1>
        </template>
        <template v-else>
            <h1 style="color:blue">我是Template2</h1>
        </template>
    </div>

    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!',
                show: false
            },
        })
    </script>
</body>
</html>
```

利用渲染结果将不包含 <template> 元素的这个特性，可以结合指令v-if进行条件渲染。因为 v-if 是一个指令，所以必须将它添加到一个元素上，所以可以把它和template搭配使用来进行元素间的切换。

>注意：Vue中另一个用于根据条件展示元素的选项是 v-show 指令。但是v-show 不支持 <template> 元素，不能搭配使用。

2. 把template元素当做一个不可见的包裹元素，用于列表渲染

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Vue模板标签templete</title>
    <style>
        .divider {
            font-size: 16px;
            color: red;
        }
    </style>
</head>
<body>
    <h1>Vue模板标签templete</h1>
    <hr>
    <div id="app">
        <ul>
            <template v-for="item in items">
                <li>{{ item.msg }}</li>
                <li class="divider">{{message}}</li>
            </template>
        </ul>
    </div>

    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!',
                items: [{
                    msg: 'list1'
                }, {
                    msg: 'list2'
                }, {
                    msg: 'list3'
                }, {
                    msg: 'list4'
                }, {
                    msg: 'list5'
                }]
            },
        })
    </script>
</body>
</html>
```

3. 用于制作模板，最后将写好的模板挂载到指定元素上展示内容。有四种写法：

第一种：直接在构造器里的template选项后边编写。这种写法比较直观，但是如果模板html代码多的话，不建议这么写。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Template</title>
</head>
<body>
    <h1>Template</h1>
    <hr>
    <div id="app">我是挂载的内容</div>

    <script type="text/javascript">
        var app=new Vue({
             el:'#app',
             data:{
                 message:'hello Vue!'
              },
             template:`
                <h1 style="color:red">我是选项模板</h1>
             `
        })
    </script>
</body>
</html>
```

可以看到模板替换了挂载的元素。挂载元素的内容都被忽略了。template元素最终也没有渲染出来。

**这里需要注意的是：模板的标识不是单引号和双引号，而是`，就是Tab上面的键。**

第二种：写在template标签里的模板，并且template元素写在挂载元素外面。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Template</title>
</head>
<body>
    <h1>Template</h1>
    <hr>
    <div id="app">我是挂载的内容</div>

    <template id="demo2">
             <h2 style="color:red">我是template标签模板</h2>
    </template>

    <script type="text/javascript">
        var app=new Vue({
             el:'#app',
             data:{
                 message:'hello Vue!'
              },
             template:'#demo2'
        })
    </script>
</body>
</html>
```

第三种：写在script标签里的模板。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Template</title>
</head>
<body>
    <h1>Template</h1>
    <hr>
    <div id="app">我是挂载的内容</div>

    <script type="x-template" id="demo3">
        <h2 style="color:red">我是script标签模板</h2>
    </script>

    <script type="text/javascript">
        var app=new Vue({
             el:'#app',
             data:{
                 message:'hello Vue!'
              },
             template:'#demo3'
        })
    </script>
</body>
</html>
```

第四种：使用Vue官方脚手架vue-cli，xxx.vue单文件组件的写法
```
<template>
    <div>
        我是第四种template写法
    </div>
</template>

<script type="text/javascript">
......
</script>

<style scoped>
</style>
```

#### 1.6 component组件

组件可以理解成就是制作自定义的标签，这些标签在HTML中是没有的。

**一、全局注册组件：**

全局就是在构造器的外部用Vue.component来注册组件：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Component-1</title>
</head>
<body>
    <h1>component-1</h1>
    <hr>
    <div id="app">
        <hello></hello>
    </div>

    <script type="text/javascript">
        //Vue.component全局注册组件，一次只能定义一个组件
        Vue.component('hello',{
            template:`<div>全局化注册的：&lt;hello&gt;标签，在构造器外部定义的</div>`
        });
    
        var app = new Vue({
            el:'#app',
            data: {
                
            }
        })
    </script>
</body>
</html>
```
上面的例子在javascript里注册了一个组件，在HTML中调用了这个组件。这就是最简单的一个组件的编写方法，并且它可以放到多个构造器的作用域里。

**二、局部注册组件：**

局部注册组件和全局注册组件是相对应的，局部注册的组件只能在组件注册的作用域里进行使用，其他作用域使用无效。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Component-1</title>
</head>
<body>
    <h1>component-1</h1>
    <hr>
    <div id="app">
        <helloworld></helloworld>
        <sayhi></sayhi>
    </div>

    <script type="text/javascript">
        var helloworld = { template:`<div>局部注册的：&lt;helloworld&gt;标签，在构造器里面定义的</div>`}
    
        var app = new Vue({
            el:'#app',
            data: {
                
            },
            //components足部注册组件，一次能定义多个组件
            components: {
                "helloworld": helloworld,
                "sayhi": {
                    template: `<div>你好，再睡一夏~</div>`
                }
            }
        })
    </script>
</body>
</html>
```
从代码中你可以看出局部注册其实就是写在构造器里，但是需要注意的是，构造器里的components 是加s的，而全局注册是不加s的。

#### 1.7 component props 组件属性
props选项就是获取标签上的属性值的。props 可以是数组或对象，用于接收传入组件的数据。props 可以是简单的数组，或者使用对象作为替代，对象允许配置高级选项，如类型检测、自定义校验和设置默认值。

**一、定义属性并获取属性值：**
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>component-2 props 组件属性</title>
</head>
<body>
    <h1>component-2 props 组件属性</h1>
    <hr>
    <div id="app">
        <date week="Monday"></date>
    </div>

    <script type="text/javascript">
        var date = {
            template: `<div>Today is {{week}}!</div>`,
            props: ['week']
        }
    
        var app=new Vue({
            el:'#app',
            data:{

            },
            components:{
                "date": date
            }
        })
    </script>
</body>
</html>
```

**二、属性中带'-'的处理方式：**

有时在写属性时会加入'-'来进行分词，比如：在props里如果写成props:['form-here']是错误的，必须用小驼峰式写法props:['formHere']：

html文件：
```
<hometown from-here="China"></hometown>
```
javascript文件：
```
 var app=new Vue({
    el:'#app',
    components:{
        "hometown":{
            template:`<div style="color:red;">I'm from {{ here }}.</div>`,
            props:['fromHere']
        }
    }
})
```
**三、在构造器里向组件中传值：**

把构造器中data的值传递给组件，只要进行绑定就可以了。使用指令v-bind:xxx.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>component-2 props 组件属性</title>
</head>
<body>
    <h1>component-2 props 组件属性</h1>
    <hr>
    <div id="app">
        <helloworld :here="message"></helloworld>
    </div>

    <script type="text/javascript">
        
        var helloworld = { 
            template: `<div>My here is {{here}}!</div>`,
            props: ['here']
        }
    
        var app=new Vue({
            el:'#app',
            data:{
                message:'GuangZhou'
            },
            components:{
                "helloworld": helloworld
            }
        })
    </script>
</body>
</html>
```

#### 1.8 component 父子组件
在实际开发中我们经常会遇到在一个自定义组件中要使用其他自定义组件，这就需要一个父子组件
关系。

**一、构造器外部写局部注册组件：**

如果都把局部组件的编写放到构造器内部，那么当组件代码量很大时，会影响构造器的可读性，造成拖拉和错误。

所以应该把组件编写的代码放到构造器外部或者放到单独的文件里。

需要先声明一个对象,对象里就是组件的内容：
```
var world={
    template:`<div><p>局部注册的- &lt;world&gt;组件</p></div>`
}
```
声明好对象后在构造器里引用就可以了：
```
components:{
    "world": world
}
```
html中引用：
```
<world></world>
```
**二、父子组件的嵌套：**

先声明一个父组件，叫world，然后里边加入一个city组件：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>component-3 父子组件</title>
</head>
<body>
    <h1>component-3 父子组件</h1>
    <hr>
    <div id="app">
        <world></world>
    </div>

    <script type="text/javascript">
        var city={
            template:`<div>this is city!</div>`
        }

        var world={
            template:`<div>
                <p>局部注册的- &lt;world&gt;组件</p>     
                <city></city>     
            </div>`,
            //父组件中引用子组件，要先在父组件中注册子组件
            components:{
                "city":city
            }
        }
        
        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!'
            },
            components:{
                "world":world
            }
        })
    </script>
</body>
</html>
```

#### 1.9 component标签，动态组件
component标签是Vue框架自定义的标签，它的用途就是可以动态绑定组件，根据数据的不同更换不同的组件。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>component-4，使用component标签生成动态组件</title>
</head>
<body>
    <h1>component-4，使用component标签生成动态组件</h1>
    <hr>
    <div id="app">
       <component v-bind:is="who"></component>
       <br>
       <button @click="changeComponent">changeComponent</button>
    </div>

    <script type="text/javascript">
        var componentA={
            template:`<div style="color:red;">I'm componentA</div>`
        }
        var componentB={
            template:`<div style="color:green;">I'm componentB</div>`
        }
        var componentC={
            template:`<div style="color:pink;">I'm componentC</div>`
        }
       
        var app=new Vue({
            el:'#app',
            data:{
                who:'componentA'
            },
            components:{
                "componentA":componentA,
                "componentB":componentB,
                "componentC":componentC,
            },
            methods:{
                changeComponent:function(){
                    if(this.who=='componentA'){
                        this.who='componentB';
                    }else if(this.who=='componentB'){
                        this.who='componentC';
                    }else{
                        this.who='componentA';
                    }
                }
            }
        })
    </script>
</body>
</html>
```
