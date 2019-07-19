# vue-router使用简介


## 安装
     npm install vue-router --save
    
## 一、基本使用步骤
1. 创建vuerouter实例
1. 在实例中配置路由
1. 注册路由
2. 使用路由标签

### 实例
1. 创建vuerouter实例，新建router文件夹，创建index.js

    	import  Vue from 'vue'
    	import Router from 'vue-router'

    	Vue.use(Router)

   		 export default new Router({

    	})


2. 在实例中配置路由，这里需要注意，对重定向的配置

		export default new Router({
		routes:[
  		{
		    path:'/home',
		    component:home
	  	},
	  	{ 
			path:'/about',
	   	 	component:about
	  	},
		 {
			 path:'/',
		     redirect:'/home'
		  }
		]
		})


3. 注册路由，在main.js中注册上面创建的router对象

		import Vue from 'vue'
		import App from './App'
		import router from './router'
		
		Vue.config.productionTip = false
		
		/* eslint-disable no-new */
		new Vue({
		  el: '#app',
		  router,
		  components: { App },
		  template: '<App/>'
		})

4. 使用路由标签，在组建中需要路由的地方配置<router-link>和<router-view>

		<template>
		  <div class="hello">
		    <div>
		    <h1>vue router</h1>
		        <router-link to="/home">home</router-link>
		        <router-link to="/about">about</router-link>
		    </div>
		    <router-view></router-view>
		  </div>
		</template>


## 二、其他用法
###1、嵌套路由（子路由）
1、在router的index.js中配置子路由，使用children

\*注意：这里的path不加'\'
		
		{
	      path: '/home',
	      name: 'home',
	      component: home,
	      children:[
	        {
	          path:'home1',
	          component:home1
	        },
	        {
	          path:'home2',
	          component:home2
	        }
	      ]
	    }

2、在父组件中配置子路由
	
	<div>
    	<router-link to="/home/home1">home1</router-link>
   		<router-link to="/home/home2">home2</router-link>
    	<router-view></router-view>
 	</div>

###2、组件路由传递数据
######方式一、路由路劲带参数
1、配置路由带参数，参数前用：

	 children:[
        {
          path:'home1/:id',
          component:home1
        },
        {
          path:'home2',
          component:home2
        }
      ]
    
2、路由路径中传递参数，注意，这里to前需要添加：

	<!--方式1   <router-link :to="'/home/home1/'+id">home1组件参数为{{id}}</router-link>   -->
   	<router-link :to="`/home/home1/${id}`">home1组件参数为{{id}}</router-link> <!--方式2 ES6语法-->
  

3、子路由组建中读取参数

	<template>
	  <div>
	    <p>home1</p>
	    <p>这是路径参数：{{param}}</p>
	</div>
	
	</template>
	
	<script>
	    export default {
	        name: "home1",
	      data(){
	          return{
	            param:this.$route.params.id
	          }
	      }
	    }
	</script>

######方式二、<router-view>属性带参数
1、在<router-view属性定义参数>
	
	<router-view :msg="msg"></router-view>
	
	<script>
	    export default {
	        name: "home1",
	      data(){
	          return{
	            msg:"我是参数"
	          }
	      }
	    }
	</script>
	

2、在子组件中使用props接受参数

	<template>
	  <div>
	    <p>home2</p>
	    <p>从router-view中传递的参数:{{msg}}</p>
	  </div>
	</template>
	
	<script>
	    export default {
	        name: "Home2",
	        props:{
	          msg:String
	        }
	    }
</script>

###3、缓存路由组件

默认情况下切换组件时，当前组件会销毁，再次回来时重新创建，使用<keep-alive>缓存当前组件
	
	<keep-alive >
    	  <router-view></router-view>
	</keep-alive>

###4、编程式路由导航
主要是相关API的使用

* this.$router.push(path)//路由记录栈，path压栈后覆盖栈顶
* this.$router.replace(path)//替换栈顶，删除当前栈顶，使用新path代替
* this.$router.back()//返回上一个path
* this.$router.go(-1)//返回上一个path
* this.$router.go(1)//请求下一个path，如果有
    
