# Vue基础知识

vue.js——开发版本：包含完整的警告和调试模式
vue.min.js——生产版本：删除了警告，进行了压缩

#### 1、内部指令

指令 (Directives) 是带有 v- 前缀的特殊特性。指令特性的值预期是单个 JavaScript 表达式 (v-for 是例外情况)。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。

##### 1.1 v-if & v-show

**v-if**

- v-if:是vue的一个内部指令，必须将它添加到一个元素上。v-if根据条件判断是否加载对应元素的DOM。

- v-else 指令来表示 v-if 的“else 块”。v-else 元素必须紧跟在带 v-if 或者 v-else-if 的元素的后面，否则它将不会被识别。

- v-else-if，顾名思义，充当v-if的“else-if块”，可以连续使用。类似于v-else，v-else-if 也必须紧跟在带 v-if 或者 v-else-if 的元素之后。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>v-if & v-else & v-show</h1>
    <hr>
    <div id="app">
        <div v-if="isLogin == 'A'">v-if</div>
        <div v-else-if="isLogin == 'B'">v-else-if</div>
        <div v-else>v-else</div>
        <div v-show="isLogin">v-show</div>
        <hr>
        <template v-if="isLogin == 'B'">v-if支持template元素</template>
        <template v-show="isLogin == 'B'">v-show不支持template元素</template>>
    </div>

    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                isLogin: 'A'
            },
            components: {
                "test-child": {
                  template:"<h1>我是template</h1>"
                }
            }
        })
    </script>
</body>
</html>
```
**v-show**

- v-show：是另一个用于根据条件展示元素的指令。

不同的是带有 v-show 的元素始终会被渲染并保留在 DOM 中。v-show 只是简单地切换元素的 CSS 属性 display。

>注意，v-show 不支持 `<template> `元素,也不支持 v-else。

**v-if 和v-show的区别：**

- v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。v-if 也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

- v-show 就简单得多，不管初始条件是什么，元素总是会被渲染，即元素会始终渲染并保持在DOM中，并且只是简单地基于 CSS 进行切换。

- v-show不支持template元素，作用在template元素上没有效果，v-if则支持。

>一般来说，v-if 有更高的切换开销（根据条件对元素进行创建...销毁...创建...销毁），而 v-show 有更高的初始渲染开销（不管条件是否为true，，元素都会被渲染并保存在DOM中）。因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。

##### 1.2 v-for指令：解决模板循环问题

用 v-for 指令根据一组数组的选项列表进行渲染。v-for 指令需要使用 item in items 形式的特殊语法，items 是源数据数组并且 item 是数组元素迭代的别名。

>需要注意的是，你需要那个html标签循环，v-for就写在那个标签上面。

基本用法：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>V-for 案例</title>
</head>
<body>
    <h1>v-for指令用法</h1>
    <hr>
    <div id="app">
       <ul>
           <li v-for="item in items">
                {{item}}
           </li>
       </ul>
    </div>
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                items:[20,23,18,65,32,19,54,56,41]
            }
        })
    </script>
</body>
</html>
```
**排序：**
运行上面代码，可以看到浏览器顺利的输出了定义的数组，但是如果想在输出之前对定义的数组进行一些操作，例如：给定义的数组排个序，则可以使用Vue的computed:属性。
```
var app = new Vue({
    ......
    computed:{
        sortItems:function(){
              return this.items.sort(sortNumber);
        }
    }
})

function sortNumber(a,b){
    return a-b
}
```
在computed里新声明了一个对象sortItems，如果不重新声明延用data里面定义的数组items则会报错，原因是这样会污染原来定义的数据源，这是Vue不允许的，所以要重新声明一个对象。

**对象循环输出：**

先定义个数组，数组里边是对象数据
```
students: [
    {name: 'Andy', age: 18},
    {name: 'Tony', age: 17},
    {name: 'Alen', age: 35},
    {name: 'zaishuiyixia', age: 25}
]
```

在模板中输出

```
<ul>
   <li v-for="student in students">
       {{student.name}} - {{student.age}}
   </li>
</ul>
```
加入索引序号：
```
//数组对象方法排序:
function sortByKey(array,key){
    return array.sort(function(a,b){
      var x=a[key];
      var y=b[key];
      return ((x<y)?-1:((x>y)?1:0));
   });
}
```
有了数组的排序方法，在computed中进行调用排序
```
sortStudent:function(){
     return sortByKey(this.students,'age');
}
```
完整代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-for</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>v-for实例</h1>
    <hr>
    <div id="app">
        <ul>
            <li v-for="item in sortItems">
                {{item}}
            </li>
        </ul>
        <hr>
        <ul>
            <li v-for="(student, index) in sortStudent">
                {{index+1}}: {{student.name}}-{{student.age}}
            </li>
        </ul>
    </div>

    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                items:[25,58,98,65,28,98,28,76],
                students: [
                    {name: 'Andy', age: 18},
                    {name: 'Tony', age: 17},
                    {name: 'Alen', age: 35},
                    {name: 'zaishuiyixia', age: 25}
                ]
            },
            computed: {
                sortItems: function() {
                    return this.items.sort(sortNumber)
                },
                sortStudent:function(){
                     return sortByKey(this.students,'age');
                }
            }
        });

        function sortNumber(a, b) {
            return a-b;
        }

        //数组对象方法排序:
        function sortByKey(array,key){
            return array.sort(function(a,b){
              var x=a[key];
              var y=b[key];
              return ((x<y)?-1:((x>y)?1:0));
           });
        }
    </script>
</body>
</html>
```

#### 1.3 v-text & v-html指令

Vue中数据绑定的形式是使用“Mustache”语法 (双大括号) 的文本插值：
```
<span>Message: {{ msg }}</span>
```

使用这种语法是有弊端的，就是当速很慢或者javascript出错时，浏览器会先显示{{xxx}}。Vue提供的v-text，就是解决这个问题的。

```
<span v-text="message"></span>
<!-- 和下面的一样 -->
<span>{{message}}</span>
```

如果在javascript中写有html标签，用v-text是输出不出来的，这时候我们就需要用v-html标签了。

双大括号会将数据解释为纯文本，而非HTML。为了输出真正的HTML，就需要使用v-html 指令。 需要注意的是：在生产环境中动态渲染HTML是非常危险的，因为容易导致XSS攻击。所以只能在可信的内容上使用v-html，永远不要在用户提交和可操作的网页上使用。 完整代码：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-text & v-html实例</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>v-text & v-html实例</h1>
    <hr>
    <div id="app">
        <span>{{message}}</span> == <span v-text="message"></span>
        <br>
        <span v-html="html"></span>
    </div>

    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                message: 'hello world!',
                html: '<h2>hello world!</h2>'
            }
        })
    </script>
</body>
</html>
```

#### 1.4 v-on指令
绑定事件监听器。事件类型由参数指定。表达式可以是一个方法的名字或一个内联语句，如果没有修饰符也可以省略。

用在普通元素上时，只能监听原生 DOM 事件。用在自定义元素组件上时，也可以监听子组件触发的自定义事件。

使用绑定事件监听器，编写一个加分减分的程序：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-on实例</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>v-on实例</h1>
    <hr>
    <div id="app">
        本场比赛得分：{{score}}
        <p>
            <button v-on:click="jiafen">加分</button>
            <button v-on:click="jianfen">减分</button>
            <br><br>
            <!-- 绑定键盘的enter键 -->
            <input type="text" v-on:keyup.enter="onEnter" v-model="score2" >
        </p>
    </div>

    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                score: 0,
                score2: 1
            },
            methods: {
                jiafen: function() {
                    this.score++;
                },
                jianfen: function() {
                    this.score--;
                },
                onEnter: function() {
                    this.score = this.score + parseInt(this.score2)
                }
            }
        })
    </script>
</body>
</html>
```
v-on 还有一种简单的写法，就是用@代替。
```
<button @click="jianfen">减分</button>
```
除了绑定click之外，还可以绑定其它事件，比如键盘回车事件v-on:keyup.enter,现在增加一个输入框，然后绑定回车事件，回车后把文本框里的值加到我们的count上。 绑定事件写法：
```
<input type="text" v-on:keyup.enter="onEnter" v-model="score2">
```
javascript代码：
```
onEnter:function(){
     this.count=this.count+parseInt(this.secondCount);
}
```
因为文本框的数字会默认转变成字符串，所以我们需要用parseInt()函数进行整数转换。

#### 1.5 v-model
v-model指令，可简单的理解为绑定数据源。就是把数据绑定在特定的表单元素上，可以很容易的在表单控件或者组件上创建双向绑定          、 。

使用限制，应该用于表单控件或者组件上：

`<input>、<select>、<textarea>、components`

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-model绑定数据源实例</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>v-model绑定数据源实例</h1>
    <hr>
    <div id="app">
        <p>原始文本信息：{{message}}内部指令</p>
        <h3>文本框</h3>
        <p>v-model：<input type="text" v-model="message"></p>
        <p>v-model.lazy：<input type="text" v-model.lazy="message"></p>
        <p>v-model.number：<input type="text" v-model.number="message"></p>
        <p>v-model.trim<input type="text" v-model.trim="message"></p>
        <hr>
        <h3>文本域</h3>
        <textarea cols="30" rows="10" v-model="message"></textarea>
        <hr>
        <h3>多选框绑定一个值</h3>
        <input type="checkbox" id="isTrue" v-model="isTrue">
        <label for="isTrue">{{isTrue}}</label>
        <hr>
        <h3>多选框绑定数组</h3>
        <p>
            <input type="checkbox" id="alen" value="alen" v-model="names">
            <label for="alen">alen</label>
            <input type="checkbox" id="andy" value="andy" v-model="names">
            <label for="andy">andy</label>
            <input type="checkbox" id="tony" value="tony" v-model="names">
            <label for="tony">tony</label>
            <p>{{names}}</p>
        </p>
        <hr>
        <h3>单选框绑定</h3>
        <p>
            <input type="radio" id="man" value="男" v-model="sex">
            <label for="man">男</label>
            <input type="radio" id="woman" value="女" v-model="sex">
            <label for="woman">女</label>
            <p>你选择的性别是：{{sex}}</p>
        </p>
    </div>

    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                message: 'hello world!',
                isTrue: true,
                names: [],
                sex: '男'
            },
            methods: {
                
            }
        })
    </script>
</body>
</html>
```

#### 1.6 v-bind

动态地绑定一个或多个属性。在绑定 class 或 style 特性时，支持其它类型的值，如数组或对象。

绑定css样式，在绑定CSS样式时，绑定的值必须在vue中的data属性中进行声明：

1、直接绑定class样式
```
<div :class="className">1、绑定classA</div>
```
2、绑定classA并进行判断，在isClassA为true时显示样式，在isClassA为false时不显示样式。
```
<div :class="{classA:isClassA}">2、绑定class中的判断</div>
```
3、绑定class中的数组
```
<div :class="[classA,classB]">3、绑定class中的数组</div>
```
4、绑定class中使用三元表达式判断
```
<div :class="isClassA?classA:classB">4、绑定class中的三元表达式判断</div>
```
5、绑定style
```
<div :style="{color:red,fontSize:font}">5、绑定style</div>
```
6、用对象绑定style样式
```
<div :style="styleObject">6、用对象绑定style样式</div>
......
var app=new Vue({
   el:'#app',
   data:{
       styleObject:{
           fontSize:'24px',
           color:'green'
            }
        }
})
```
完整代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-bind实例</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <style>
        .classA {
            color: red;
        }
        .classB {
            font-size: 150%;
        }
    </style>
</head>
<body>
    <h1>v-bind实例</h1>
    <hr>
    <div id="app">
        <p><img v-bind:src="imgUrl" alt="" width="300px"></p>
        <p><a :href="blogUrl" target="_blank">我的博客地址</a></p>
        <hr>
        <div :class="className">1、绑定class属性</div>
        <div :class="{classA: isClassA}">2、绑定class中的判断</div>
        <div :class="[classA, classB]">3、绑定class中的数组</div>
        <div :class="isClassA?classA:classB">4、绑定class中的三元运算符</div>
        <hr>
        <div>
            <input type="checkbox" id="isClassA" v-model="isClassA">
            <label for="isClassA">isClassA={{isClassA}}</label>
        </div>
        <hr>
        <div :style="{color:color,fontSize:font}">5、绑定style</div>
        <div :style="styleObj">5、对象绑定style</div>
    </div>

    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                imgUrl: 'https://upload-images.jianshu.io/upload_images/1969310-c688f5d4d3a352b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp',
                blogUrl: 'https://www.jianshu.com/p/26010db3776c',
                className: 'classA',
                isClassA: false,
                classA: 'classA',
                classB: 'classB',
                color: 'red',
                font: '20px',
                styleObj: {
                    color: 'green',
                    fontSize: '24px'
                }
            }
        })
    </script>
</body>
</html>
```

#### 1.7 其他指令

**v-pre指令：**

在模板中跳过vue的编译，直接输出原始值。就是在标签中加入v-pre就不会输出vue中的data值了。
```
<div v-pre>{{message}}</div>
```
**v-cloak指令：**

在vue渲染完指定的整个DOM后才进行显示。可以防止数据抖动。
```
<div v-cloak>
  {{ message }}
</div>
```

**v-once指令：**

在第一次DOM时进行渲染，渲染完成后视为静态内容，跳出以后的渲染过程。
```
<div v-once>第一次绑定的值：{{message}}</div>
<div><input type="text" v-model="message"></div>
```
完整代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-pre & v-cloak & v-once</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>v-pre & v-cloak & v-once</h1>
    <hr>
    <div id="app">
        <div v-pre>{{message}}</div>
        <div v-cloak>渲染完成后，才显示！</div>
        <div>{{message}}</div>
        <div><input type="text" v-model="message"></div>
        <div>{{message}}</div>
    </div>

    <script type="text/javascript">
        var app = new Vue({
            el: '#app',
            data: {
                message: 'hello world!'
            }
        })
    </script>
</body>
</html>
```
