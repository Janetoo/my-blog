#### 前言
>记录平时学到的知识，标题写的大气一点，也算是给自己一点鼓励，希望在技术这条路可以远走越远，路越走越宽~

[文中代码地址](https://github.com/zaishuiyixia/my-blog/tree/master/vue/%E5%AE%9E%E4%BE%8B%E5%92%8C%E5%86%85%E7%BD%AE%E7%BB%84%E4%BB%B6)

>**PS：如果对你有一点帮助，请顺手给个小星星哦，鼓励我继续写下去~**


#### 引入的文件文件说明
vue.js——开发版本：包含完整的警告和调试模式
vue.min.js——生产版本：删除了警告，进行了压缩

#### 通过new Vue({...})生成的实例

通过new Vue({...})生成的实例可以在在构造器外部操作构造器内部的属性选项或者方法。实例的作用就是给原生的或者其他javascript框架一个融合的接口或者说是机会，让Vue和其他框架一起使用。

#### 1. 实例
引入jquery，在DOM被挂载后修改里边的内容：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script src="../assets/js/jquery.js"></script>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>实例</title>
</head>
<body>
    <h1>实例</h1>
    <hr>
    <div id="app">
        {{message}}
    </div>
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message: 'Hello zaishuiyixia!'
            },
            mounted () {
                $('#app').html('我是jQuery!');
            }
        })
    </script>
</body>
</html>
```
现在页面显示是：我是jQuery，而不是Hello zaishuiyixia!了。

**实例调用自定义方法：**

在Vue的构造器里写一个add方法，然后用实例的方法调用它：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script src="../assets/js/jquery.js"></script>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>实例</title>
</head>
<body>
    <h1>实例</h1>
    <hr>
    <div id="app">
        {{message}}
    </div>
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                message: 'Hello zaishuiyixia!'
            },
            mounted() {
                $('#app').html('我是jQuery!');
            },
            methods: {
                add() {
                    console.log('通过实例调用了了add方法。')
                }
            }
        })

        app.add();
    </script>
</body>
</html>
```
打开控制台可以看到打印出了：通过实例调用了了add方法。

#### 2. 实例方法
**2.1 $mount方法**
如果 Vue 实例在实例化时没有收到 el 选项，则它处于“未挂载”状态，没有关联的 DOM 元素。可以使用 vm.$mount() 手动地挂载一个未挂载的实例。

这个方法返回实例自身，因而可以链式调用其它实例方法。

$mount方法可以用来挂载扩展：
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>实例方法</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>实例方法</h1>
    <hr>
    <div id="app">
        {{message}}
    </div>
    <script type="text/javascript">
      var app = Vue.extend({
          template:`<p>{{message}}</p>`,
          data:function(){
              return {
                  message:'Hello ,I am zaishuiyixia!'
              }
          }
      })
      var vm = new app().$mount("#app")
    </script>
</body>
</html>
```

**2.2 $destroy() 卸载/销毁 实例方法**

完全销毁一个实例。清理它与其它实例的连接，解绑它的全部指令及事件监听器。会触发 beforeDestroy 和 destroyed 的生命周期函数。

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>实例方法</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>实例方法</h1>
    <hr>
    <div id="app">
        {{message}}
        <br>    
    </div>
    <p><button onclick="destroy()">銷毀实例</button></p>
    <script type="text/javascript">
        var app = Vue.extend({
            template:`<p>{{message}}</p>`,
            data:function(){
                return {
                    message:'Hello ,I am zaishuiyixia!'
                }
            },
            destroyed() {
                console.log('实例已被销毁。')
            }
        })
        var vm = new app().$mount("#app");

        function destroy(){
           vm.$destroy();
        }
    </script>
</body>
</html>
```

**2.3 forceUpdate() 强制实例重新渲染方法**

强制 Vue 实例重新渲染。注意它仅仅影响实例本身和插入插槽内容的子组件，而不是所有子组件。
会触发beforeUpdate和updateed生命周期函数。
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>实例方法</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>实例方法</h1>
    <hr>
    <div id="app">
        {{message}}
        <br>    
    </div>
    <p><button onclick="destroy()">銷毀实例</button></p>   
    <p><button onclick="forceUpdate()">强制实例重新渲染</button></p>
    <script type="text/javascript">
        var app = Vue.extend({
            template:`<p>{{message}}</p>`,
            data:function(){
                return {
                    message:'Hello ,I am zaishuiyixia!'
                }
            },
            beforeUpdate() {
                console.log('准备更新数据')
            },
            updated() {
                console.log('更新完成')
            },
            destroyed() {
                console.log('实例已被销毁。')
            }
        })
        var vm = new app().$mount("#app");

        function destroy(){
           vm.$destroy();
        }

        function forceUpdate(){
           vm.$forceUpdate();
        }
    </script>
</body>
</html>
```
**2.4 $nextTick()**
当Vue构造器里的data值被修改完成后会调用这个方法，也相当于一个钩子函数，和构造器里的updated生命周期作用很像。
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>实例方法</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>实例方法</h1>
    <hr>
    <div id="app">
        {{message}}
        <br>    
    </div>
    <p><button onclick="destroy()">銷毀实例</button></p>   
    <p><button onclick="forceUpdate()">强制实例重新渲染</button></p>
    <p><button onclick="tick()">更新数据</button></p>
    <script type="text/javascript">
        var app = Vue.extend({
            template:`<p>{{message}}</p>`,
            data:function(){
                return {
                    message:'Hello ,I am zaishuiyixia!'
                }
            },
            beforeUpdate() {
                console.log('准备更新数据')
            },
            updated() {
                console.log('更新完成')
            },
            destroyed() {
                console.log('实例已被销毁。')
            }
        })
        var vm = new app().$mount("#app");

        function destroy(){
           vm.$destroy();
        }

        function forceUpdate(){
           vm.$forceUpdate();
        }

        function tick(){
            vm.message="update message info ";
            vm.$nextTick(function(){
                console.log('message更新完后我被调用了');
            })
        }
    </script>
</body>
</html>
```

#### 3. 实例事件
**vm.$on( event, callback ) 在构造器外部监听当前实例上的自定义事件，事件可以由vm.$emit触发。回调函数会接收所有传入事件触发函数的额外参数。**

$on 在构造器外部添加事件：
```
app.$on('reduce',function(){
    console.log('执行了reduce()');
    this.num--;
});
```
$on接收两个参数，第一个参数是调用时的事件名称，第二个参数是一个匿名方法。

事件可以由vm.$emit触发：
```
//外部调用内部事件
function reduce(){
    app.$emit('reduce');
}
```
完整代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>实例事件</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>Example Event Demo</h1>
    <hr>
    <div id="app">
        <p>{{num}}</p>
        <button @click="add">add</button>
        
    </div>
    <p><button onclick="reduce()">reduce</button></p>
     
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{num:1},
            methods:{
                add:function(){
                    this.num++;
                }
            }
        })

        //实例事件
        app.$on('reduce',function(){
            console.log('执行了reduce()');
            this.num--;
        });
    </script>
</body>
</html>
```
**vm.$once( event, callback )监听一个自定义事件，但是只触发一次，在第一次触发之后移除监听器。**
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>实例事件</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>Example Event Demo</h1>
    <hr>
    <div id="app">
        <p>{{num}}</p>
        <button @click="add">add</button>  
    </div>
     <p><button onclick="reduce()">reduce</button></p>
     <p><button onclick="reduceOnce()">reduceOnce</button></p>
     <p><button onclick="off()">off</button></p>
     

    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{num:1},
            methods:{
                add() {
                    this.num++;
                }
            }
        })

        //实例事件
        app.$on('reduce',function(){
            console.log('执行了reduce()');
            this.num--;
        });

        //只使用一次的实例方法
        app.$once('reduceOnce',function(){
            console.log('只执行一次的方法');
            this.num--;
        });
    </script>
</body>
</html>
```

**vm.$off( [event, callback] )移除自定义事件监听器：**

1. 如果没有提供参数，则移除所有的自定义事件监听器；
2. 如果只提供了事件，则移除该自定义事件事件所有的监听器；
3. 如果同时提供了事件与回调，则只移除这个回调的监听器。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>实例事件</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>Example Event Demo</h1>
    <hr>
    <div id="app">
        <p>{{num}}</p>
        <button @click="add">add</button>  
    </div>
     <p><button onclick="reduce()">reduce</button></p>
     <p><button onclick="reduceOnce()">reduceOnce</button></p>
     <p><button onclick="off()">off</button></p>
     

    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{num:1},
            methods:{
                add() {
                    this.num++;
                }
            }
        })

        //实例事件
        app.$on('reduce',function(){
            console.log('执行了reduce()');
            this.num--;
        });

        //只使用一次的实例方法
        app.$once('reduceOnce',function(){
            console.log('只执行一次的方法');
            this.num--;
        });

        //关闭事件
        function off(){
            app.$off('reduce');
        }

        //外部调用内部事件
        function reduce(){
            app.$emit('reduce');
        }

        function reduceOnce(){
            app.$emit('reduceOnce');
        }
    </script>
</body>
</html>
```

#### 4. 插槽-slot

插槽，也就是slot，是组件的一块HTML模板，这块模板显示不显示、以及怎样显示由父组件来决定。 实际上，一个slot最核心的两个问题在这里就点出来了，是显示不显示和怎样显示。

由于插槽是一块模板，所以，对于任何一个组件，从模板种类的角度来分，其实都可以分为非插槽模板和插槽模板两大类。

非插槽模板指的是html模板，比如'div、span、ul、table'这些，非插槽模板的显示与隐藏以及怎样显示由组件自身控制；插槽模板是slot，它是一个空壳子，因为它的显示与隐藏以及最后用什么样的html模板显示由父组件控制。**但是插槽显示的位置却由子组件自身决定，slot写在组件template的什么位置，父组件传过来的模板将来就显示在什么位置**。

**4.1 默认插槽 | 匿名插槽：**

默认插槽，也可以叫它匿名插槽，它不用设置name属性。它会作为所有未匹配到插槽的内容的统一出口。

**一个组件中只能有一个该类插槽。相对应的，具名插槽就可以有很多个，只要名字（name属性）不同就可以了**。

父组件：
```
<template id="tmp">
    <div class="father">
        <h3>这里是父组件</h3>
        <child>
            <div class="tmpl">
              <span>菜单1</span>
              <span>菜单2</span>
              <span>菜单3</span>
              <span>菜单4</span>
              <span>菜单5</span>
              <span>菜单6</span>
            </div>
        </child>
    </div>
</template>
```

子组件：
```
<template id="tmp2">
    <div class="child">
        <h3>这里是子组件</h3>
        <slot></slot>
    </div>
</template>
```
在这个例子里，因为父组件在里面写了html模板，那么子组件的匿名插槽这块模板就是下面这样。也就是说，子组件的匿名插槽被使用了，是被下面这块模板使用了。
```
<div class="tmpl">
  <span>菜单1</span>
  <span>菜单2</span>
  <span>菜单3</span>
  <span>菜单4</span>
  <span>菜单5</span>
  <span>菜单6</span>
</div>
```
完整代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Mixins Option</title>
    <style>
        .father {
            background: #ccc;
        }
        .child {
            background: green;
        }
    </style>
</head>
<body>
    <h1>内置组件Slot</h1>
    <hr>
    <div id="app">
        <father></father>
    </div>

    <template id="tmp">
        <div class="father">
            <h3>这里是父组件</h3>
            <child>
                <div class="tmpl">
                  <span>菜单1</span>
                  <span>菜单2</span>
                  <span>菜单3</span>
                  <span>菜单4</span>
                  <span>菜单5</span>
                  <span>菜单6</span>
                </div>
            </child>
        </div>
    </template>

    <template id="tmp2">
        <div class="child">
            <h3>这里是子组件</h3>
            <slot></slot>
        </div>
    </template>

    <script type="text/javascript">
        var child = {
            template: '#tmp2'
        }

        var father = {
            template:'#tmp',
            components: {
                "child": child
            }
        }

        var app=new Vue({
            el:'#app',
            data:{
               
            },
            components:{
                "father": father
            }
        })
    </script>
</body>
</html>
```

打开控制台可以查看效果，父组件以灰色背景填充，子组件都以绿色填充。

**4.2 具名插槽 ：**

匿名插槽没有name属性，所以是匿名插槽，那么，插槽加了name属性，就变成了具名插槽。具名插槽可以在一个组件中出现N次，出现在不同的位置。下面的例子，就是一个有两个具名插槽和一个匿名插槽的组件，这三个插槽被父组件用同一套css样式显示了出来，不同的是内容上略有区别。

父组件：

```
<template id="tmp">
  <div class="father">
    <h3>这里是父组件</h3>
    <child>
      <div class="tmpl" slot="up">
        <span>菜单1</span>
        <span>菜单2</span>
        <span>菜单3</span>
        <span>菜单4</span>
        <span>菜单5</span>
        <span>菜单6</span>
      </div>
      <div class="tmpl" slot="down">
        <span>菜单-1</span>
        <span>菜单-2</span>
        <span>菜单-3</span>
        <span>菜单-4</span>
        <span>菜单-5</span>
        <span>菜单-6</span>
      </div>
      <div class="tmpl">
        <span>菜单->1</span>
        <span>菜单->2</span>
        <span>菜单->3</span>
        <span>菜单->4</span>
        <span>菜单->5</span>
        <span>菜单->6</span>
      </div>
    </child>
  </div>
</template>
```
子组件：
```
<template id="tmp2">
  <div class="child">
    <!--具名插槽 -->
    <slot name="up"></slot>
    <h3>这里是子组件</h3>
    <!--具名插槽-->
    <slot name="down"></slot>
    <!--匿名插槽-->
    <slot></slot>
  </div>
</template>
```
完整代码： 
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>具名插槽</title>
    <style>
        .father {
            background: #ccc;
        }
        .up {
            background: green;
        }
        .down {
            background: red;
        }
        .tmpl {
            background: blue;
        }
        .child {
            background: yellow;
        }
    </style>
</head>
<body>
    <h1>具名插槽</h1>
    <hr>
    <div id="app">
        <father></father>
    </div>

    <template id="tmp">
        <div class="father">
        <h3>这里是父组件</h3>
        <child>
          <div class="up" slot="up">
            <span>菜单1</span>
            <span>菜单2</span>
            <span>菜单3</span>
            <span>菜单4</span>
            <span>菜单5</span>
            <span>菜单6</span>
          </div>
          <div class="down" slot="down">
            <span>菜单-1</span>
            <span>菜单-2</span>
            <span>菜单-3</span>
            <span>菜单-4</span>
            <span>菜单-5</span>
            <span>菜单-6</span>
          </div>
          <div class="tmpl">
            <span>菜单->1</span>
            <span>菜单->2</span>
            <span>菜单->3</span>
            <span>菜单->4</span>
            <span>菜单->5</span>
            <span>菜单->6</span>
          </div>
        </child>
      </div>
    </template>

    <template id="tmp2">
        <div class="child">
            <!--具名插槽 -->
            <slot name="up"></slot>
            <h3>这里是子组件</h3>
            <!--具名插槽-->
            <slot name="down"></slot>
            <!--匿名插槽-->
            <slot></slot>
        </div>
    </template>

    <script type="text/javascript">
        var child = {
            template: '#tmp2'
        }

        var father = {
            template:'#tmp',
            components: {
                "child": child
            }
        }

        var app=new Vue({
            el:'#app',
            data:{
               
            },
            components:{
                "father": father
            }
        })
    </script>
</body>
</html>
```
打开浏览器可以看到，父组件通过html模板上的slot属性关联具名插槽。没有slot属性的html模板默认关联匿名插槽。

**4.3 作用域插槽 | 带数据的插槽：**

官方叫它作用域插槽，实际上，对比前面两种插槽，可以叫它带数据的插槽。意思就是前面两种，都是在组件的template里面写：
```
匿名插槽
<slot></slot>
具名插槽
<slot name="up"></slot>
```
但是作用域插槽要求，在slot上面绑定数据。也就是你得写成大概下面这个样子：
```
<slot name="up" :data="data"></slot>
 export default {
    data: function(){
      return {
        data: ['zhangsan','lisi','wanwu','zhaoliu','tianqi','xiaoba']
      }
    },
}
```
插槽最后显示不显示是看父组件有没有在child下面写模板，像下面那样：
```
<child>
   html模板
</child>
```
写了的话，插槽就会在浏览器上显示内容，内容就是html该有的模样，没写，插槽就是空壳子，什么也没有。

作用域插槽跟单个插槽和具名插槽的区别，因为单个插槽和具名插槽不绑定数据，所以父组件提供的模板一般要既包括样式又包括内容。而作用域插槽绑定了一套数据，父组件可以拿来用，所以父组件只需要提供样式，内容可以显示子组件插槽绑定的数据。

父组件：
```
<template id="tmp">
  <div class="father">
    <h3>这里是父组件</h3>
    <!--第一次使用：用flex展示数据-->
    <child>
      <template slot-scope="user">
        <div class="tmpl">
          <span v-for="item in user.data">{{item}}</span>
        </div>
      </template>

    </child>

    <!--第二次使用：用列表展示数据-->
    <child>
      <template slot-scope="user">
        <ul>
          <li v-for="item in user.data">{{item}}</li>
        </ul>
      </template>

    </child>

    <!--第三次使用：直接显示数据-->
    <child>
      <template slot-scope="user">
       {{user.data}}
      </template>

    </child>

    <!--第四次使用：不使用其提供的数据, 作用域插槽退变成匿名插槽-->
    <child>
      我就是模板
    </child>
  </div>
</template>
```
子组件：
```
<template>
  <div class="child">

    <h3>这里是子组件</h3>
    // 作用域插槽
    <slot  :data="data"></slot>
  </div>
</template>

 export default {
    data: function(){
      return {
        data: ['zhangsan','lisi','wanwu','zhaoliu','tianqi','xiaoba']
      }
    }
}
```
完整代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>作用域插槽</title>
    <style>
        .father {
            background: #ccc;
        }
        .child {
            background: green;
        }
    </style>
</head>
<body>
    <h1>作用域插槽</h1>
    <hr>
    <div id="app">
        <father></father>
    </div>

    <template id="tmp">
        <div class="father">
            <h3>这里是父组件</h3>
            <!--第一次使用：用flex展示数据-->
            <child>
                <template slot-scope="user">
                    <div class="tmpl">
                        <span v-for="item in user.data">{{item + '、'}}</span>
                    </div>
                </template>
            </child>

            <!--第二次使用：用列表展示数据-->
            <child>
              <template slot-scope="user">
                <ul>
                  <li v-for="item in user.data">{{item}}</li>
                </ul>
              </template>
            </child>

            <!--第三次使用：直接显示数据-->
            <child>
                <template slot-scope="user">
                    {{user.data}}
                </template>

            </child>

            <!--第四次使用：不使用其提供的数据, 作用域插槽退变成匿名插槽-->
            <child>
                我就是模板
            </child>
        </div>
    </template>

    <template id="tmp2">
        <div class="child">

            <h3>这里是子组件</h3>
            <!--作用域插槽-->
            <slot  :data="data"></slot>
        </div>
    </template>

    <script type="text/javascript">
        var child = {
            template: '#tmp2',
            data: function(){
                return {
                    data: ['zhangsan','lisi','wanwu','zhaoliu','tianqi','xiaoba']
                }
            }
        }

        var father = {
            template:'#tmp',
            components: {
                "child": child
            }
        }

        var app=new Vue({
            el:'#app',
            data:{
               
            },
            components:{
                "father": father
            }
        })
    </script>
</body>
</html>
```