## Vue组件通信方法介绍


组件化编程的思路一般为：

 	1. 编写静态页面
	2. 将页面拆分为组件，组件之间的关系形成父子组件，兄弟组件
	3. 为组件绑定数据，如果是几个组件都会用的数据，则放到父组件中维护；如果只有自己用到的数据，则放到自己组件维护
	4. 为组件添加交互，主要为绑定监听
 
所以，组建通信主要讲的是第3点，数据在组件中的传递方法。这里介绍六种组件通信的方法：

	1. props属性
	2. 自定义事件
	3. 子组件索引
	4. 消息订阅和发布，pubsub
	5. slot内容分发
	6. vuex数据仓库


### 1、props 

**场景：父组件传递给子组件**
	
	1. 父组件通过子组件标签传递一个自定义参数
	2. 子组件需要显式地用props选项声明“prop”传递的参数，可以以{参数名：参数类型}的方式来做参数过滤
	3. 动态Props：用v-bind绑定动态props到父组件的数据。每当父组件的数据变化时，也会传导给子组件


**例子：**
通过props传递了数据和方法

	父组件中：
	 <ul class="list-group">
      <item v-for="(comment,index) in comments" :index="index" :comment="comment" :deletecomment="deletecomment" :commentindex="index"></item>
    </ul>

	子组件中：
	<template>
	  <li class="list-group-item">
	    <div class="handle">
	      <a href="javascript:;" @click="deleteItem">删除</a>
	    </div>
	    <p class="user"><span >{{comment.name}}</span><span>说:</span></p>
	    <p class="centence">{{comment.content}}</p>
	  </li>
	</template>
	
	<script>
	export default {
	  props:{
	    comment:Object,
	    deletecomment:Function,
	    commentindex:Number
	  },
	  name: 'item',
	  data () {
	    return {
	      msg: 'Welcome to Your Vue.js App'
	    }
	  },
	  methods:{
	    deleteItem(){
	        this.deletecomment(this.index)//传递的方法可以通过this调用
	    }
	  }
	}



### 2、自定义事件 

**场景：子组件调用父组件**
	
	1. 绑定事件,在子组件标签中@XX事件名="事件处理方法"
	2. 父组件中实现事件触发后的"事件处理方法"
	* 1 2步骤可以使用在父组件中this.$refs.子组件名.$on(事件名，function处理逻辑)代替
	3. 在子组件<templet>中绑定子组件中触发的事件，如@click=method
	4. 在子组件method中实现提交emit自定义事件，即this.$emit(XX事件名,data)
**例子：方式1**

	父组件：
	<template>
	  <div id="app">
	   <search @myevent="dealsearchword"/>
	    <maincom/>
	  </div>
	</template>
	
	<script>
	  methods:{
	    dealsearchword(data){
	      ……
	    }
	  }
	

	父组件：
	</script>

	子组件：
	<template>	
		    <div>
		      <input type="text" placeholder="enter the name you search" v-model="searchword"/>
		      <button @click="search">Search</button>
		    </div>
	</template>

	<script>
	  import PubSub from 'pubsub-js'
	    export default {
	        name: "search",
	      data(){
	          return {
	            searchword:''
	          }
	      },
	      methods:{
	        search(){
	          //PubSub.publish("search",this.searchword)
	          this.$emit('myevent',this.searchword)//触发自定义事件
	        }
	      }
	    }
	</script>

**例子：方式2**
	
	<template>
	  <div id="app">
	   <search ref="search"/>//通过ref建立组件索引
	    <maincom/>
	  </div>
	</template>
	
	<script>
	import HelloWorld from './components/HelloWorld'
	import search from  './components/search'
	import maincom from  './components/main'
	export default {
	  name: 'App',
	  components: {
	    HelloWorld,
	    search,
	    maincom
	  },
	  methods:{
	    dealsearchword(data){
	      alert(data)
	    }
	
	  },
	  mounted(){
	    this.$refs.search.$on("myevent",this.dealsearchword)//在mouted中绑定子组件的监听
	  }
	
	}
	</script>

### 3、子组件索引
**场景：父组件调用子组件**

通过使用refs来调用子组件的方法	

	<子组件 ref=“子组件索引”>	
	this.$refs.子组件索引.XXmethod();

### 4、消息订阅和发布
**场景：任意组件之间传递消息**

消息的订阅和发布基于pubsub工具，首先需要安装pubsub

	npm install --save pubsub-js
使用步骤：

1、mounted方法中订阅消息

	import PubSub from 'pubsub-js'	 
	mounted() {
          PubSub.subscribe("消息名",(msg,参数data)=>{}
	}

2、在需要其他组件发布消息

	import PubSub from 'pubsub-js'
	PubSub.publish("消息名",参数data)



### 5、slot内容分发
**场景:父组件将内容分发到子组件**

##### 1）匿名slot

	子组件包含<slot>，父组件中<子组件>标签之间的内容会代替<slot>位置，新内容插入到slot。如果子组件<solt>中有内容，也会被父组件内容替换掉。

##### 2）有名slot

* 当父组件需要传递多个内容到子组件时，需要用到有名slot，通过slot的name属性来设置。
* 仍然可以保留有一个匿名slot,它是默认slot，作为找不到匹配的内容片段的备用插槽。如果没有默认的slot，这些找不到匹配的内容片段将被抛弃。

		<template>
		  <div id="app">
		    <children>
		      <div slot="header">
		        <ul>
		          <li>首页</li>
		          <li>商城</li>
		        </ul>
		      </div>
		      <div>
		        这个是默认的没有具名的solt
		      </div>
		      <div slot="footer">
		          <p>备案号</p>
		      </div>
		    </children>
		  </div>
		</template>
		<script>
		  var Child = {
		    template: ' <div>这是子组件<div></div> <slot name="header"></slot> <slot></slot> <slot name="footer"></slot> </div>'
		  }
		  export default {
		    name: 'hello',
		    components: {
		      children: Child
		    }
		  }
		</script>


### 6、vuex
**场景：任意组件之间共享公共数据**

vuex的内容相对复杂，后续单独学习。