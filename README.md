### 使用方法
> * git clone https://github.com/zhouyupeng/vue-mock.git 
> * cd vue-mock
> * npm install
> * npm run dev

 
* ###搭建一个vue项目
```javascript
# 全局安装 vue-cli
$ npm install --global vue-cli
# 创建一个基于 webpack 模板的新项目
vue init webpack vue-mock
$ cd my-project
# 安装依赖
$ npm install
```
* ###安装mockjs
>npm install mockjs --save-dev

* ###开启项目
>npm run dev
* ####创建一个mockjs文件夹以及mockjs,并且在main.js引入这个文件
此时可以看到像这样的一个项目结构

![QQ20171022-195122@2x.png](https://pic2.zhimg.com/80/v2-a8422770183c9da3bc60e2bf1fc28780_hd.jpg)

* ###mockjs的使用
在项目中的mock.js文件中，写入模拟的数据，此时我们可以参照一下[mockjs](https://github.com/nuysoft/Mock/wiki)的文档。
```javascript
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
接下来可以做我们想要做的事了
在mock.js中模拟简单的一些数据
```javascript
 const Mock = require('mockjs');
// 获取 mock.Random 对象
 const Random = Mock.Random;
 // mock一组数据
 const produceData = function (opt) {
   console.log('opt', opt);
   let articles = [];
   for (let i = 0; i < 30; i++) {
     let newArticleObject = {
       title: Random.csentence(5, 30), // Random.csentence( min, max )
       thumbnail_pic_s: Random.dataImage('300x250', 'mock的图片'), // Random.dataImage( size, text ) 生成一段随机的 Base64 图片编码
       author_name: Random.cname(), // Random.cname() 随机生成一个常见的中文姓名
       date: Random.date() + ' ' + Random.time() // Random.date()指示生成的日期字符串的格式,默认为yyyy-MM-dd；Random.time() 返回一个随机的时间字符串
     }
     articles.push(newArticleObject)
   }
   return {
     data: articles
   }
 }
Mock.mock('/news', /post|get/i, produceData);//当post或get请求到/news路由时Mock会拦截请求并返回上面的数据
```
在vue中请求  
```javascript
methods: {
      setNewsApi: function() {
        this.$http.post("/news", "type=top&key=123456").then(res => {
          console.log(res.data);
  
          this.newsListShow = res.data.data;
        });
      }
    }
```      
效果预览

![WX20171022-2134561@2x.png](https://pic4.zhimg.com/80/v2-9707d510e439f19023b4fed2990b25d7_hd.jpg)
###[线上预览地址](https://zhouyupeng.github.io/mock-vue/#/)

再做一个删除的处理
模拟数据
```javascript
let arr = []
 for (let i = 0; i < 30; i++) {
   let newArticleObject = {
     name: Random.cname(), // Random.cname() 随机生成一个常见的中文姓名
     content: Random.csentence(5, 30), // Random.csentence( min, max )
     id: i
   }
   arr.push(newArticleObject);
 }
 let list = function (options) {
   let rtype = options.type.toLowerCase(); //获取请求类型
   switch (rtype) {
     case 'get':
       break;
     case 'post':
       let id = parseInt(JSON.parse(options.body).params.id) //获取删除的id
       arr = arr.filter(function(val){
          return val.id!=id;//把这个id对应的对象从数组里删除
       });
       break;
     default:
   }
   return {
     data: arr
   } //返回这个数组,也就是返回处理后的假数据
 }
 Mock.mock('/list', /get|post/i, list);//get用于请求数据，post用于删除数据
```
vue中使用
```javascript
methods: {
      setNewsApi: function() {
        this.$http.get("/list", "").then(res => {
          this.data = res.data.data;
        });
      },
      deleteList(data) { //删除数据
        let id = data.id;
        this.$http.post('/list', {
            params: {
              id: id
            }
          }).then(function(res) {
            console.log(res);
            this.data = res.data.data;
            alert(data.name + '删除成功');
          }.bind(this))
          .catch(function(error) {
            console.log(error)
          });
      },
    }
 ```
效果预览

![WX20171022-2146143@2x.png](https://pic3.zhimg.com/80/v2-3f1d8a12a3821d5c3487e667c9a23ae5_hd.jpg)

###[线上预览地址](https://zhouyupeng.github.io/mock-vue/#/list2)
