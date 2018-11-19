#### 前言
>记录平时学到的知识，标题写的大气一点，也算是给自己一点鼓励，希望在技术这条路可以远走越远，路越走越宽~

[文中代码地址](https://github.com/zaishuiyixia/my-blog/tree/master/vue/%E9%80%89%E9%A1%B9)

>**PS：如果对你有一点帮助，请顺手给个小星星哦，鼓励我继续写下去~**


#### 引入的文件文件说明
vue.js——开发版本：包含完整的警告和调试模式
vue.min.js——生产版本：删除了警告，进行了压缩

#### 1. propsData Option 全局扩展的数据传递

propsData在实际开发中使用的并不多，用在全局扩展时进行传递数据，主要搭配Vue.extend使用。

在实际的项目中，使用全局扩展的方式制作自定义标签比较少用，完全可以使用组件来替代。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>propsData Option</title>
</head>
<body>
    <h1>propsData Option</h1>
    <hr>
    <div id="app"></div>

    <script type="text/javascript">
       var  header_a = Vue.extend({
           template:`<p>{{message}}</p>`,
           data:function(){
               return {
                   message:'Hello,I am Header'
               }
           }
       });
       
       new header_a().$mount('#app');
    </script>
</body>
</html>
```
扩展标签已经做好了，这时要在挂载时传递数据，就用到了propsData。

使用用propsData三步解决传值： 
1. 在全局扩展里加入props进行接收;
2. 传递时用propsData进行传递;
3. 用插值的形式写入模板;

完整代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>propsData Option</title>
</head>
<body>
    <h1>propsData Option</h1>
    <hr>
    <div id="app"></div>

    <script type="text/javascript">
        var  header_a = Vue.extend({
            template:`<p>{{message}}-{{a}}</p>`,
            data:function(){
                return {
                    message:'Hello,I am Header'
                }
            },
            props:['a']
        }); 
        
        new header_a({propsData:{a:1}}).$mount('header');
    </script>
</body>
</html>
```

#### 2.computed Option 计算选项
computed计算属性的主要作用是在数据渲染之前，根据实际需求对数据进行处理，比如：大小写转换，顺序重排，添加符号……。为了不污染data中定义的数据源，在computed里需要新声明一个对象保存处理之后的数据。

computed计算属性的所有getter和setter的this上下文自动地绑定为 Vue 实例。**注意如果你为一个计算属性使用了箭头函数，则 this 不会指向这个组件的实例，不过你仍然可以将其实例作为函数的第一个参数来访问。**
```
computed: {
  aDouble: vm => vm.a * 2 
}
```

计算属性的结果会被缓存，除非依赖的响应式属性变化才会重新计算。**注意，如果某个依赖 (比如非响应式属性) 在该实例范畴之外，则计算属性是不会被更新的。**

用计算属性反转新闻数据数组，让最近发生的新闻放在前面显示，demo：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>computed 计算选项</title>
</head>
<body>
    <h1>computed 计算选项</h1>
    <hr>
    <div id="app">
        {{newPrice}}
        <br>
        <br>
        <ul>
            <li v-for="item in reverseNews">{{item.title}} - {{item.date}}</li>
        </ul>
    </div>
    <script type="text/javascript">
        var newsList = [
            {title:'香港或就“装甲车被扣”事件追责 起诉涉事运输公司',date:'2017/3/10'},
            {title:'日本第二大准航母服役 外媒：针对中国潜艇',date:'2017/3/12'},
            {title:'中国北方将有明显雨雪降温天气 南方阴雨持续',date:'2017/3/13'},
            {title:'起底“最短命副市长”：不到40天落马，全家被查',date:'2017/3/23'},
        ];

        var app=new Vue({
            el:'#app',
            data:{
                price:100,
                newsList: newsList
            },
            computed:{
                newPrice:function(){
                    return this.price='￥' + this.price + '元';
                },
                reverseNews:function(){
                    return this.newsList.reverse();
                }
            }
        })
    </script>
</body>
</html>
```

computed 计算属性是非常有用并且在实际的项目开发中经常使用，它在输出数据前可以根据实际项目需求对数据进行处理，改变数据。

#### 3. methods Option 方法选项
在Vue中，可以使用v-on给元素绑定事件，在methods选项中处理一些逻辑方面的事情。在Vue中的逻辑处理，一般都在Vue的methods选项中来处理，那是因为很多事件处理逻辑代码都很复杂，如果直接把JavaScript代码写在v-on指令中有时并不可行，所以在methods中定义方法，让v-on指令来接收（调用）。

一个数字，每点击一下按钮加2：
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>methods Option</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>methods Option</h1>
    <hr>
    <div id="app">
        {{ a }}
        <p><button @click="add">add</button></p>
    </div>
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                a:1
            },
            methods:{
                add () {
                    this.a++
                }
            }
        })
    </script>
</body>
</html>
```
**methods中参数的传递：**

使用方法和正常的javascript传递参数的方法一样，分为两步：

1. 在methods的方法中进行声明，比如给add方法传入一个num参数，就要写出add (num){...};
2. 调用方法时直接传递，比如要传递2这个参数，在button上就直接可以写。`<button @click=”add(2)”></button>`;

给add添加num参数，并在按钮上调用传递：
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>methods Option</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>methods Option</h1>
    <hr>
    <div id="app">
        {{ a }}
        <p><button @click="add(2)">add</button></p>
    </div>
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                a:1
            },
            methods:{
                add (num) {
                    this.a += num
                }
            }
        })
    </script>
</body>
</html>
```

这时，再点击按钮时结果每次加2。

**methods中的$event参数：**

传递的$event参数都是关于你点击鼠标的一些事件和属性。传递方法：

```
<button @click=”add(2,$event)”>add</button> 。
```

这时候可以打印一下，看看event到底是个怎样的对象。你会发现，它包含了大部分鼠标事件的属性：
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>methods Option</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>methods Option</h1>
    <hr>
    <div id="app">
        {{ a }}
        <p><button @click="add(2,'每次加2', $event)">add</button></p>
    </div>
    <script type="text/javascript">
        var app=new Vue({
            el:'#app',
            data:{
                a:1
            },
            methods:{
                add (num, msg, event) {
                    console.log('num==>', num)
                    console.log('msg==>', msg)
                    console.log('event==>', event)
                    this.a += num
                }
            }
        })
    </script>
</body>
```
**native 给组件绑定构造器里的原生事件：**

在实际开发中经常需要把某个按钮封装成组件，然后反复使用，如何让组件调用构造器里的方法，而不是组件里的方法。就需要用到.native修饰器了。

把我们的add按钮封装成组件，声明btn对象：
```
var btn={
    template:`<button>组件Add</button>`     
}
```
在构造器里注册组件：
```
components:{
    "btn":btn
}
```
用.native修饰器来调用构造器里的add方法：
```
<p><btn @click.native="add(3)"></btn></p>
```
完整代码：
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>methods Option</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>methods Option</h1>
    <hr>
    <div id="app">
       {{a}}
       <p><button @click="add(2, '每次加2', $event)">add</button></p>
       <p><btn @click.native="add(3, '每次加3', $event)"></btn></p>
    </div>

    <script type="text/javascript">
       var btn={
           template:'<button>外部组件</button>'
       }
        var app=new Vue({
            el:'#app',
            data:{
                a:1
            },
            methods:{
                add (num, msg, event) {
                    console.log('num==>', num)
                    console.log('msg==>', msg)
                    console.log('event==>', event)
                    this.a += num
                }
            },
            components:{
                "btn":btn
            }
        })
    </script>
</body>
</html>
```

**作用域外部调用构造器里的方法：**

这种不经常使用，如果你出现了这种情况，说明你的代码组织不够好。
```
<button onclick="app.add(4)" >外部调用构造器里的方法</button>
```
完整代码：
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>methods Option</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>methods Option</h1>
    <hr>
    <div id="app">
       {{a}}
       <p><button @click="add(2, '每次加2', $event)">add</button></p>
       <p><btn @click.native="add(3, '每次加3', $event)"></btn></p>
    </div>
    <button onclick="app.add(4, '每次加4')">外部访问构造器里的方法</button>

    <script type="text/javascript">
       var btn={
           template:'<button>外部组件</button>'
       }
        var app=new Vue({
            el:'#app',
            data:{
                a:1
            },
            methods:{
                add (num, msg, event) {
                    console.log('num==>', num)
                    console.log('msg==>', msg)
                    console.log('event==>', event)
                    this.a += num
                }
            },
            components:{
                "btn":btn
            }
        })
    </script>
</body>
</html>
```

#### 4. Watch 选项 监控数据
使用watch选项来监控数据的变化。watch选项对应一个对象，键是观察表达式，值是对应回调。值也可以是方法名，或者是对象，包含选项。在实例化时为每个键调用 $watch() 。

天气预报的穿衣指数，它主要是根据温度来进行提示，温度大于26度时，建议穿T恤短袖，温度小于26度大于0度时，建议穿夹克长裙，温度小于0度时建议穿棉衣羽绒服：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>watch Option</title>
</head>
<body>
    <h1>watch Option</h1>
    <hr>
    <div id="app">
        <p>今日温度：{{temperature}}°C</p>
        <p>穿衣建议:{{suggestion}}</p>
        <p>
            <button @click="add">添加温度</button>
            <button @click="reduce">减少温度</button>
        </p>
    </div>

    <script type="text/javascript">
        var suggestion=['T恤短袖','夹克长裙','棉衣羽绒服'];
        var app=new Vue({
            el:'#app',
            data:{
                temperature:14,
                suggestion:'夹克长裙'
            },
            methods:{
                add:function(){
                    this.temperature+=5;
                },
                reduce:function(){
                    this.temperature-=5;
                }
            },
            watch:{
                temperature (newVal,oldVal) {
                    if(newVal>=26){
                        this.suggestion=suggestion[0];
                    }else if(newVal<26 && newVal >=0)
                    {
                        this.suggestion=suggestion[1];
                    }else{
                        this.suggestion=suggestion[2];
                    }
                }
            }
        })
    </script>
</body>
</html>
```

**handler方法和immediate属性：**

这里 watch 的一个特点是，最初绑定的时候是不会执行的，要等到 temperature 改变时才执行监听计算。那我们想要一开始就让它最初绑定的时候就执行改怎么办呢？我们需要修改一下我们的 watch 写法，修改过后的 watch 代码如下：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>watch Option</title>
</head>
<body>
    <h1>watch Option</h1>
    <hr>
    <div id="app">
        <p>今日温度：{{temperature}}°C</p>
        <p>穿衣建议:{{suggestion}}</p>
        <p>
            <button @click="add">添加温度</button>
            <button @click="reduce">减少温度</button>
        </p>
    </div>

    <script type="text/javascript">
        var suggestion=['T恤短袖','夹克长裙','棉衣羽绒服'];
        var app=new Vue({
            el:'#app',
            data:{
                temperature:14,
                suggestion:'T恤短袖'
            },
            methods:{
                add:function(){
                    this.temperature+=5;
                },
                reduce:function(){
                    this.temperature-=5;
                }
            },
            watch:{
                temperature: {
                    handler (newVal,oldVal) {
                        if(newVal>=26){
                            this.suggestion=suggestion[0];
                        }else if(newVal<26 && newVal >=0)
                        {
                            this.suggestion=suggestion[1];
                        }else{
                            this.suggestion=suggestion[2];
                        }
                    },
                    // 代表在wacth里声明了temperature这个方法之后立即先去执行handler方法
                    immediate: true
                }
            }
        })
    </script>
</body>
</html>
```
注意到handler了吗，我们给 temperature 绑定了一个handler方法，之前我们写的 watch 方法其实默认写的就是这个handler，Vue.js会去处理这个逻辑，最终编译出来其实就是这个handler。

而immediate:true代表如果在 wacth 里声明了 temperature 之后，就会立即先去执行里面的handler方法，如果为 false就跟我们以前的效果一样，不会在绑定的时候就执行。

在上面的事例中把`suggestion`的初始数据设置为'T恤短袖'，而初始温度是14°C，这时如果没有让watch在最初绑定的时候就执行，那么推荐的穿衣就有问题，因为14°C原本是推荐穿'夹克长裙'的，通过让watch在最初绑定的时候就执行完美解决了这个问题。

**deep属性：**
watch 里面还有一个属性 deep，默认值是 false，代表是否深度监听，比如我们 data 里有一个obj属性：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>watch Option</title>
</head>
<body>
    <h1>watch Option</h1>
    <hr>
    <div id="app">
        <p>今日温度：{{obj.temperature}}°C</p>
        <p>穿衣建议:{{obj.suggestion}}</p>
        <p>
            <button @click="add">添加温度</button>
            <button @click="reduce">减少温度</button>
        </p>
    </div>

    <script type="text/javascript">
        var suggestion=['T恤短袖','夹克长裙','棉衣羽绒服'];
        var app=new Vue({
            el:'#app',
            data: {
                obj: {
                    temperature: 14,
                    suggestion:'T恤短袖'
                }
            },
            methods:{
                add:function(){
                    this.obj.temperature+=5;
                },
                reduce:function(){
                    this.obj.temperature-=5;
                }
            },
            watch: {
                obj: {
                    handler(newVal, oldVal) {
                        console.log('obj.a changed', newVal.temperature)
                        if(newVal.temperature>=26){
                            this.obj.suggestion=suggestion[0];
                        }else if(newVal.temperature<26 && newVal.temperature >=0)
                        {
                            this.obj.suggestion=suggestion[1];
                        }else{
                            this.obj.suggestion=suggestion[2];
                        }
                        console.log('obj.a 2', newVal.suggestion)
                    },
                    immediate: true
                }
            } 
        })
    </script>
</body>
</html>
```
当我们在点击添加温度和减少温度按钮改变obj.temperature的值时，发现是无效的。受现代 JavaScript 的限制 (以及废弃 Object.observe)，Vue 不能检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化过程，所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。

默认情况下 handler 只监听obj这个属性它的引用的变化，我们只有给obj赋值的时候它才会监听到，比如我们在 mounted事件钩子函数中对obj进行重新赋值：
```
mounted: {
  this.obj = {
    temperature: 40,
    suggestion:'T恤短袖'
  }
}
```
这样我们的 handler 才会执行。

如果需要监听obj里的属性temperature的值，这时候deep属性就派上用场了：
```
watch: {
    obj: {
        handler(newVal, oldVal) {
            console.log('obj.a changed', newVal.temperature)
            if(newVal.temperature>=26){
                this.obj.suggestion=suggestion[0];
            }else if(newVal.temperature<26 && newVal.temperature >=0)
            {
                this.obj.suggestion=suggestion[1];
            }else{
                this.obj.suggestion=suggestion[2];
            }
            console.log('obj.a 2', newVal.suggestion)
        },
        immediate: true,
        deep: true
    }
} 
```
deep的意思就是深入观察，监听器会一层层的往下遍历，给对象的所有属性都加上这个监听器，但是这样性能开销就会非常大了，任何修改obj里面任何一个属性都会触发这个监听器里的 handler。

优化，可以使用字符串形式监听：
```
watch: {
    'obj.temperature': {
        handler(newVal, oldVal) {
            console.log('obj.a changed', newVal)
            if(newVal>=26){
                this.obj.suggestion=suggestion[0];
            }else if(newVal<26 && newVal >=0)
            {
                this.obj.suggestion=suggestion[1];
            }else{
                this.obj.suggestion=suggestion[2];
            }
            console.log('obj.a 2', this.obj.suggestion)
        },
        immediate: true,
        deep: true
    }
} 
```
这样Vue.js才会一层一层解析下去，直到遇到属性temperature，然后才给temperature设置监听函数。

完整代码： 
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>watch Option</title>
</head>
<body>
    <h1>watch Option</h1>
    <hr>
    <div id="app">
        <p>今日温度：{{obj.temperature}}°C</p>
        <p>穿衣建议:{{obj.suggestion}}</p>
        <p>
            <button @click="add">添加温度</button>
            <button @click="reduce">减少温度</button>
        </p>
    </div>

    <script type="text/javascript">
        var suggestion=['T恤短袖','夹克长裙','棉衣羽绒服'];
        var app=new Vue({
            el:'#app',
            data: {
                obj: {
                    temperature: 14,
                    suggestion:'T恤短袖'
                }
            },
            methods:{
                add:function(){
                    this.obj.temperature+=5;
                },
                reduce:function(){
                    this.obj.temperature-=5;
                }
            },
            // watch: {
            //     obj: {
            //         handler(newVal, oldVal) {
            //             console.log('obj.a changed', newVal.temperature)
            //             if(newVal.temperature>=26){
            //                 this.obj.suggestion=suggestion[0];
            //             }else if(newVal.temperature<26 && newVal.temperature >=0)
            //             {
            //                 this.obj.suggestion=suggestion[1];
            //             }else{
            //                 this.obj.suggestion=suggestion[2];
            //             }
            //             console.log('obj.a 2', newVal.suggestion)
            //         },
            //         immediate: true,
            //         deep: true
            //     }
            // }

            //优化使用字符串形式监听
            watch: {
                'obj.temperature': {
                    handler(newVal, oldVal) {
                        console.log('obj.a changed', newVal)
                        if(newVal>=26){
                            this.obj.suggestion=suggestion[0];
                        }else if(newVal<26 && newVal >=0)
                        {
                            this.obj.suggestion=suggestion[1];
                        }else{
                            this.obj.suggestion=suggestion[2];
                        }
                        console.log('obj.a 2', this.obj.suggestion)
                    },
                    immediate: true,
                    deep: true
                }
            } 
        })
    </script>
</body>
</html>
```

**用实例属性写watch监控：**

有些时候我们会用实例属性的形式来写watch监控。也就是把我们watch卸载构造器的外部，这样的好处就是降低我们程序的耦合度，使程序变的灵活：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>watch Option</title>
</head>
<body>
    <h1>watch Option</h1>
    <hr>
    <div id="app">
        <p>今日温度：{{temperature}}°C</p>
        <p>穿衣建议:{{suggestion}}</p>
        <p>
            <button @click="add">添加温度</button>
            <button @click="reduce">减少温度</button>
        </p>
    </div>

    <script type="text/javascript">
        var suggestion=['T恤短袖','夹克长裙','棉衣羽绒服'];
        var app=new Vue({
            el:'#app',
            data:{
                temperature:14,
                suggestion:'夹克长裙'
            },
            methods:{
                add:function(){
                    this.temperature+=5;
                },
                reduce:function(){
                    this.temperature-=5;
                }
            }
        })

        app.$watch('temperature',function(newVal,oldVal){
            if(newVal>=26){
                this.suggestion=suggestion[0];
            }else if(newVal<26 && newVal >=0)
            {
                this.suggestion=suggestion[1];
            }else{
                this.suggestion=suggestion[2];
            }
        })
    </script>
</body>
</html>
```

**注销watch：**

为什么要注销 watch？因为我们的组件是经常要被销毁的，比如我们跳一个路由，从一个页面跳到另外一个页面，那么原来的页面的 watch 其实就没用了，这时候我们应该注销掉原来页面的 watch 的，不然的话可能会导致内置溢出。当把 watch 写在组件的选项中的，它会随着组件的销毁而销毁。

但是，如果我们使用上面的这样的方式写 watch：
```
app.$watch('temperature',function(newVal,oldVal){
    if(newVal>=26){
        this.suggestion=suggestion[0];
    }else if(newVal<26 && newVal >=0)
    {
        this.suggestion=suggestion[1];
    }else{
        this.suggestion=suggestion[2];
    }
})
```

那么就要手动注销了，这种注销其实也很简单:
```
var unwatch = app.$watch('temperature',function(newVal,oldVal){
    if(newVal>=26){
        this.suggestion=suggestion[0];
    }else if(newVal<26 && newVal >=0)
    {
        this.suggestion=suggestion[1];
    }else{
        this.suggestion=suggestion[2];
    }
})

unWatch(); // 手动注销watch
```
app.$watch调用后会返回一个值，就是unWatch方法，你要注销 watch 只要调用unWatch方法就可以了。

#### 5. mixins 混入选项
混入 (mixins) 是一种分发 Vue 组件中可复用功能的非常灵活的方式。混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被混入该组件本身的选项。

mixins一般有两种用途：

1. 在你已经写好了构造器后，需要增加方法或者临时的活动时使用的方法，这时用混入会减少源代码的污染。
2. 很多地方都会用到的公用方法，用混入的方法可以减少代码量，实现代码重用。

现在有个数字点击递增的程序，假设已经完成了，这时希望每次数据变化时都能够在控制台打印出提示：“数据发生变化”：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Mixins Option</title>
</head>
<body>
    <h1>Mixins Option Demo</h1>
    <hr>
    <div id="app">
        <p>num: {{ num }}</p>
        <P><button @click="add">增加数量</button></P>
    </div>
    <script type="text/javascript">
        //额外临时加入，用于显示日志
        var addLog={
            updated:function(){
                console.log("数据放生变化,变化成"+this.num+".");
            }
        }

        var app=new Vue({
            el:'#app',
            data:{
                num:1
            },
            methods:{
                add:function(){
                    this.num++;
                }
            },
            //混入
            mixins:[addLog]
        })
    </script>
</body>
</html>
```
**选项合并：**

当组件和混入对象含有同名选项时，这些选项将以恰当的方式混合，合并规则：

1. 值为对象的选项，例如数据对象data、方法对象methods、组件对象components和自定义指令对象directives，将被混合为同一个对象。两个对象键名冲突时，取组件对象的键值对。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Mixins Option</title>
</head>
<body>
    <h1>Mixins Option Demo</h1>
    <hr>
    <div id="app">
        <p>num: {{ num }}</p>
        <P><button @click="add">增加数量</button></P>
    </div>
    <script type="text/javascript">
        //额外临时加入，用于显示日志
        var addLog={
            data: function () {
                return {
                    message: 'hello',
                    foo: 'abc'
                }
            },
            updated () {
                console.log("数据放生变化,变化成"+this.num+".");
            },
            created () {
                console.log('混入对象的钩子被调用==>', this.$data)
            }
        }

        var app=new Vue({
            el:'#app',
            data:{
                num:1,
                message: 'goodbye',
                bar: 'def'
            },
            methods:{
                add:function(){
                    this.num++;
                }
            },
            created () {
                console.log('实例钩子被调用==>', this.$data)
            },
            //混入
            mixins:[addLog]
        })
    </script>
</body>
</html>
```
打开控制台可以看到，混入的created钩子函数和实例的created钩子函数打印出的this.$data都为`{bar: "def", foo: "abc", message: "goodbye", num: 1}`。

2. 由1的例子可以看出，同名钩子函数将混合为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子之前调用。

**mixins的调用顺序：**

从执行的先后顺序来说，都是混入的先执行，然后构造器里的再执行，需要注意的是，这并不是方法的覆盖，而是被执行了两边。

同名钩子函数将混合为一个数组，因此都将被调用。混入对象的钩子将在组件自身钩子之前调用。

**全局API混入方式：**

可以全局注册混入对象。但应注意使用！因为一旦使用全局混入对象，将会影响到所有之后创建的 Vue 实例。

定义全局的混入，然后在需要这段代码的地方直接引入js，就可以拥有这个功能了：
```
Vue.mixin({
    updated:function(){
        console.log('我是全局被混入的');
    }
})
```

**注意：全局混入的执行顺序要前于混入和构造器里的方法。**

完整代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>Mixins Option</title>
</head>
<body>
    <h1>Mixins Option Demo</h1>
    <hr>
    <div id="app">
        <p>num: {{ num }}</p>
        <P><button @click="add">增加数量</button></P>
    </div>
    <script type="text/javascript">
        //全局混入
        Vue.mixin({
            created () {
                console.log('我是全局混入');
            }
        })

        //额外临时加入，用于显示日志
        var addLog={
            data: function () {
                return {
                    message: 'hello',
                    foo: 'abc'
                }
            },
            updated () {
                console.log("数据放生变化,变化成"+this.num+".");
            },
            created () {
                console.log('混入对象的钩子被调用==>', this.$data)
            }
        }

        var app=new Vue({
            el:'#app',
            data:{
                num:1,
                message: 'goodbye',
                bar: 'def'
            },
            methods:{
                add:function(){
                    this.num++;
                }
            },
            created () {
                console.log('实例钩子被调用==>', this.$data)
            },
            //混入
            mixins:[addLog]
        })
    </script>
</body>
</html>
```
#### 6. extends Option 扩展选项
通过外部增加对象的形式，对构造器进行扩展。允许声明扩展另一个组件(可以是一个简单的选项对象或构造函数)，而无需使用 Vue.extend。这主要是为了便于扩展单文件组件。和 mixins 类似。

**选项合并：**

当组件和扩展对象含有同名选项时，这些选项将以恰当的方式混合，合并规则：

1. 值为对象的选项，例如数据对象data、方法对象methods、组件对象components和自定义指令对象directives，将被混合为同一个对象。两个对象键名冲突时，取组件对象的键值对。
2. 同名钩子函数将混合为一个数组，因此都将被调用。另外，扩展对象的钩子将在组件自身钩子之前调用。

完整代码：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Extends Optin</title>
    <script type="text/javascript" src="../assets/js/vue.js"></script>
</head>
<body>
    <h1>Extends Optin Demo</h1>
    <hr>
    <div id="app">
        {{message}}
        <p><button @click="add">add</button></p>
    </div>
    <script type="text/javascript">
        var bbb={
            created () {
                console.log("我是扩展出来的钩子函数");
            },
            methods:{
                add () {
                    console.log('我是被扩展出来的方法！');
                }
            }
        };

        var app=new Vue({
            el:'#app',
            data:{
                message:'hello Vue!'
            },
            methods:{
                add () {
                    console.log('我是原生方法');
                }
            },
            created () {
                console.log("我是原生的钩子函数");
            },
            extends:bbb
        })
    </script>
</body>
</html>
```
打开控制台可以看到，扩展出来的钩子函数先执行，然后是实例的钩子函数；点击按钮之后只有实例的方法被执行。

#### 7. delimiters 选项
delimiters的作用是改变插值的符号。Vue默认的插值是双大括号{{}}。但有时我们会有需求更改这个插值的形式。

比如后台渲染模板如swig，也使用“{{ }}“作为渲染，与前端vue的数据绑定“Mustache”语法 (双大括号)产生冲突，此时只要在新建Vue对象时，添加delimiters: ['${', '}'],就搞定了。

现在插值形式就变成了${}。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script type="text/javascript" src="../assets/js/vue.js"></script>
    <title>delimiters Option</title>
</head>
<body>
    <h1>delimiters Option</h1>
    <hr>
    <div id="app">
        <ul>
            <li v-for=" aa in arr">${aa}</li>
        </ul>
    </div>
    <script type="text/javascript">

        var outData={
            arr:['aaa','bbb','ccc'],
        };

        var app=new Vue({
            delimiters: ['${', '}'],
            el: '#app',
            data: outData
        })
    </script>
</body>
</html>
```







