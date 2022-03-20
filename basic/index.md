    一、MVC和MVVM
    二、v-model原理
    1、双向数据绑定
    v-model主要提供了两个功能，view层输入值影响data的属性值，data属性值发生改变会更新view层的数值变化。
    1、v-model主要提供了两个功能，view层输入值影响data的属性值，data属性值发生改变会更新view层的数值变化。
    2、v-model主要提供了两个功能，view层输入值影响data的属性值，data属性值发生改变会更新view层的数值变化。
    3、其核心就是，一方面modal层通过defineProperty来劫持每个属性，一旦监听到变化通过相关的页面元素更新。另一方面通过编译模板文件，为控件的v-model绑定input事件，从而页面输入能实时更新相关data属性值。
    4、v-model只不过是一个语法糖而已,真正的实现靠的还是
        v-bind:绑定响应式数据
        触发oninput 事件并传递数据
        <input v-model="sth" />
        //  等同于
        <input :value="sth" @input="sth = $event.target.value" />
        v-model="sth" 是 :value="sth" @input="sth = $event.target.value" 的缩写
        $event 指代当前触发的事件对象。

        $event.target 指代当前触发的事件对象的dom

        $event.target.value 就是当前dom的value值

    <input placeholder="请输入名字" id="username">
    显示值：<p id="uName"></p>
    <script>
    let obj={}
    Object.defineProperty(obj,"username",{
        //取值
        get:function(){
            return obj.username;
            console.log()
        },
        set:function{val}{
            document.getElementById("uName"),innerText=val;
        }
    })
    document.getElementById("username").addEventListener("keyup",function(){
        obj.username=$event.target.value
    })
    </script>
    三、data()为什么是函数
    //data是一个闭包 闭包=>让每一个组件都有自己的私有作用域，确保各个组件数据不会相互干扰
    //不用闭包就用纯对象，纯对象=>存在干扰，let a={}
     data(){
         return(){
             value:""
         }
     }
      四、v-if和v-show
      v-if去判断要不要渲染Dom,单次判断
      v-show:隐藏DOM，一次渲染，多次切换（display:none）
      权限操作不能用v-show
      五、虚拟DOM
      a、虚拟DOM是什么？
      vue2.x加入
      本质是js对象=>用跨平台特性
      b、虚拟DOM在vue中的角色
      vue的渲染过程，浏览器只识别三剑客：html,cs,js
      将真实DOM转化成虚拟DOM（js对象）
      更新的时候用来做对比（第一次加载的时候从template到render函数直接到真实DOM中间加了虚拟DOM，第一次加载需要的时间更长了）
      3、虚拟DOM怎么提升vue的渲染效率
      vue的两大核心：组件化；数据驱动
      之前是生成新的dom,直接把old的dom干掉
      将新老进行对比，去更新节点
      虚拟dom做到局部更新（节点数据）
      将直接操作dom的地方变成拿到两个js对象去做比较，性能上有优化
      六、diff算法
        patch方法
        虚拟dom怎么转化为真实DOM的
        虚拟dom生成的三个要
        a、当前的目标元素
        b、拿到tag上面的属性 class，id属性
        c、子节点
        初始化 patch(container,vnode)
        更新 update(vnode,newVnode)
        function createElement(vnode){
            let tag=vnode.tag //目标元素 ul标签
            let attrs=vnode.attrs || {} //属性
            let children=vnode.children || [] //子节点
            if(!tag){
                return null
            }
            //创建对应的dom
            let elem=document.createElement(tag);
            let attrName
            //给dom添加属性
            for(attrName in attrs){
                if(attrs.hasOwnProperty(attrName)){
                    elem.setAttribute(attrName,attrs[attrName])
                }
            }
            //添加子元素
            children.forEach(function(childNode){
                elem.appendChild(createElement(childNode))
            })
            return elem;
        }
        更新子节点，不需要更新所有
        function updateChildren(vnode,newVnode){
             let children=vnode.children || []  //现在的节点
             let newChildren=newVnode.children || [] //新节点
             children.forEach(function(childrenVnode,index){ //循环的每一项，index当前第几个
                 let newChildrenVnode=neChildren[index]; // 新节点中对应的每一项
                 if(childrenVnode.tag===newChildrenVnode.tag){
                     //第一层没变化
                    updateChildren(childrenVnode,newChildrenVnode)
                 }else{
                     //
                     replaceNode(childrenVnode,newChildrenVnode)
                 }


             }

             )
        }
        七、$nextTick()
        DOM更新之后延迟回调
        A组件调用B组件中的方法，确保B组件加载
        $nextTick({
            this.$refs.b.dn() //调用b组件中的方法
        })
        八、单应用与多应用的区别及优缺点
        单页应用（SPA）:只有一个主页面的应用（VUE）
        VUE的组件可以看成页面卡段
        跳转=>刷新局部资源，A组件跳转到B组件，刷新局部资源，页面片段（使用场景：PC端）

        优点：体验好，快；内容的改变，不需要加载整个页面；前后端分离；效果可以很炫酷

        缺点：不利于SEO；第一次加载比较慢；页面的复杂度高（js,css,html在一个文件中）

        多页应用：
        整页刷新，和单页刚好相反

        九、v-if和v-for(不能同时使用)
        v-for代码执行优先级大于v-if（每一个for循环都执行v-if）

        十、vue-router和location.href的区别
        location.href原生js，简单方便，会刷新页面(跳外链选择)
        vue-router按需加载，减少dom的消耗（底层封装了js原生的history）（跳自身）

       十一、vue2.x响应式原理
       通过发布订阅模式加数据劫持（Object.defineProperty）去实现

       1、订阅器模型
       let Dep={
           clientList:{},//容器
           //添加订阅的方法
           listen:function(key,fn){
            //key:用户id；fn:用户关注
            //先判断有无记录，将订阅记录下来
            if(!this.clientList[key]){
                this.clientList[key]=[]
            }
            this.clientList[key].push(fn);
            //短路表达式
            (this.clientList[key] || (this.clientList[key]=[])).push(fn)
           },
           //发布
           trigger: function(){
               let key=Array.prototype.shift.call(arguments);
               fns=this.clientList[key];
               if(!fns || fns.length===0){
                   return false;
               }
               for(let i=0,fn;fn=fns[i++]){
                   fn.apply(this,arguments);
               }

           }
       }
       2、数据劫持
       let dataHi = function({data,tag,dataKey,selector}){
           let value="";
           el=document.querySelector(selector)
            Object.defineProperty(data,dataKey,{
                //取值
                get:function(){
                    return value;
                    console.log()
                },
                set:function{val}{
                    value=val;
                    //发布
                    Dep.trigger(tag,val)
                }
            })
            //绑定订阅
            Dep.listen(tag,function(text){
                el.innerHTML=text;
            })
       }
       核心：双向数据绑定；需要捕获到修改

       十二、var let const

       1、声明提升，先定义后赋值
       console.log(num) //undefined
       //var num = 123;
       let num=123; 报错

       2、var存在变量覆盖，let直接报错
       var a1=12;
       var a2=34;
       console.log(a2);

       3、var没有块级作用域，let有块级作用域
       function f(){
           for(var i=0;i<3;i++){
               console.log(i);
           }
           console.log(i);//输出3,let这里报错
       }

        4、const定义的值不能修改；声明之后一定要赋值，否则会报错；其它和let一样，一般用于全局变量
       const demo = 123;
       demo = 456;

        解构相关
       let a=1;
       let b=2;
       [a,b]=[b,a];

        ES6去重
       let a=[11,12,11,13,12,15,14,12,13,46];
       let b=[...new Set(a)];

       1、Promise构造函数，构造函数是同步的
       2、then()是异步执行
       const promise = new Promise((resolve,reject)=>{
           console.log(1);
           resolve();
           console.log(2);
       })
       promise.then(() => {
           console.log(3);
       })
       console.log(4);
       输出1, 2,4,3

       十三、