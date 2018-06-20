## Welcome to GitHub Pages

学习微信小程序，遇到的一些容易出bug的点

### Markdown
学习微信小程序，遇到的一些容易出bug的点

```markdown

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

## app
  在app是全局的配置，
  ```
    - app.json 全局的配置文件，pages的第一个就是默认页面
    - app.js 全局的js
    - app.wxss 全局的css
   ```
  
## 样式
```
    iPhone5	1rpx = 0.42px	1px = 2.34rpx
    iPhone6	1rpx = 0.5px	1px = 2rpx
    iPhone6 Plus	1rpx = 0.552px	1px = 1.81rpx
    2.高度一般用rpx；
    3.线条一般用1px；
    4.不可以使用rem
```
## 事件
所有事件前面都要加一个bind,写在wxml的标签内 <view bindtap="event"></view>
```
 1.单击事件
   bindtap   会冒泡
   catchtap   不会冒泡 不会触发上级的事件
 2.input事件
   bindinput
 3.select的事件
   bindchange

```
##跳转
  跳转事件（重定向）
  ```
    1.redirectTo  没有返回按钮
    2.navigateTo  和前面的页面有联系，会有返回按钮 只适合导航跳转
    3.switchTab   如果有tabBar(在app.json中进行设置，如下) ，只能使用这个方式切换
  ```
## tab切换按钮
  tabBar在app.json中写。这个list的length至少是2，且"pagePath"，iconPath，中必须使用相对路径，否则编译上线后会报错
```
 tabBar": {
     color:'red',   tab 上的文字默认颜色
     selectedColor:'',  tab 上的文字选中时的颜色
     backgroundColor:'yellow',   tab 的背景色
     borderStyle:'black/white'   tabbar上边框的颜色， 仅支持 black/white
     "list": [
      第一个的pagePath为默认的选中页，
      iconPath -- icon的图片的路径
      selectedIconPath -- 选中时候icon的图片路径
      {
        "pagePath": "pages/homes/home", 默认的选中页
        "text": "主页",
        "iconPath": "iconPath",
        "selectedIconPath": "selectedIconPath"
      },
      {
        "pagePath": "pages/homes/home",
        "text": "text",
        "iconPath": "iconPath",
        "selectedIconPath": "selectedIconPath"
       }
     ]
 }
```
## 轮播
  使用标签
  ```
  <swiper autoplay="{{true}}" interval='2000' indicator-dots='{{true}}' circular='{{true}}' vertical='{{true}}'>
    <swiper-item wx:for="{{imgs}}">
      <image src="/images/111.jpg"></image>
   </swiper-item>
   < swiper-item wx:for="{{imgs}}">
      <image src="{{item}}}"></image>
   </swiper-item>
  </swiper>
  ```
  ```
 注意点：这里的autoplay 表示是可以自动轮播，大概5秒一切换
        但是这里不可以直接写 autoplay =“true”或者autoplay =“false”，因为是字符串，会自动转化为布尔值，那么将都是true
         这里要写成表达式 autoplay="{{true}} " 
         interval='2000' interval 表示间隔时间
	     indicator-dots  表示出现可点击的点
         circular 循环轮播
         vertical 向上轮播，默认是向右
	     wx:for="{{lists}}"  循环  那么每一项就是item
	     wx:for-item="{{lists}}"  循环  那么每一项就是lists
         这里设置高，必须给swiper设置，不可以给swiper-item上
```
##关于假数据
  当我们做好一个假数据，怎么能在其他页面进行引用
  var data = [];
  支持common.js语法，可以使用module.exports = data；进行导出；
  在需要使用的页面使用  var list = require(“../../data/data.js”);
  这里这个require的路径使用绝对路径
  提前在data中进行定义，然后获取了之后，在onload中进行绑定数据，必须使用setData来进行绑定数据
   onLoad: function (options) {
     this.setData({
      list:slist
     })
  }
##  抽取公用的模板
  注意：模板中是不可以放js和json的，只有wxml和wxss
  ```
  比如：建立一个模板的文件夹listtemple，在他下面建立list-tmp.wxml，list-tmp.wxss
  list-tmp.wxml中  这个name是方便在引入的页面的识别
   <template name="listTem">
	这里写模板的内容
   </template> 
  ```  
  比如我要引入到home页面中,那么我就需要进行引入这个模板页面
  在home.wxml中
  ```
  <import src="listtemple/list-tmp.wxml"/>  这是将模板引入，这里要写相对路径，并且，要进行标签的闭合
  否则不识别，报错；
  引入之后，在我需要的位置
  <view bindtab="event" data-uid = "{{index}}">
    <template is = "listTem" data="{{item}}"></template>
  </view>
  那么，我就可以在event这个事件中，通过e.currentTarget.dataset.uid获取，进行传参
  因为我的模板中使用了{{item}}，所以，这里要写data = {{item}}
  
  这里可以进行处理，将data = {{...item}},就不需要顾忌模板中使用的名字是什么了；
  ```
  公共模板的循环中，这个item是可以进行简化处理，方便之后的模板中的不同的数据格式的渲染
  ```
   这里将<template is = "listTem" data="{{...item}}"></template>
   模板中的绑定就这样写<view class='list_right'>  
        <text class="list_title">{{title}}</text>
        <text class="list_name">{{content}}</text>
    </view>
   可以去掉item了，原来是item.title
   这里如果不这样写的话，那么我们在使用模板的时候会受到限制，因为模板中是
   item.data，那么我其他页面的命名也必须是item，否则渲染失败，使用{{...data}}的方式可以解决受限的问题
  ```
  最后，将样式 @import "listtemple/list-tmp.wxss",引入到对应的页面的wxss中
  就ok了
## 音视频文件的引入
     在api的媒体中进行看相关的属性和方法 



##  label中的for和input的关系
 一点label，就会自己进入到input中


## 事件
   分享功能
  提示框  showModal
          showActionSheet
  

## 时间选择器  start和end是可以不写的，也可以动态绑定的
  //这个是时间选择器，
   <picker mode="time" start="10:00" end="18:00" data="{{date}}" bindchange='timeEvent'>
     <view>你选择的时间是{{date}}</view>
   </picker>
   
   //这个是日期选择器  这个日期的格式必须是 xxxx-mm-dd
   <picker mode="date" start="{{aa}}" end="" data="{{time}}" bindchange='timedateEvent'>
     <view>你选择的时间是{{time}}</view>
   </picker>


## 请求动态数据
```
   wx.request({
	url: 'https://sp0.baidu.com/5a1Fazu8AA54nxGko9WTAnF6hhy/su?&cb=a',
    data: {
      wd: this_.data.val,
    },
    method: 'GET',
    header: {},
    success(res) {
      var lists =res.data.slice(19,-3);
      console.log(lists)  
      this_.setData({
        lists: JSON.parse(lists)
      });
	  this_.setData({
          flag: true
      })
	}
   });
  根据需要处理数据即可
```
## 提取公共方法
   建一个公共文件夹util，在它下面新建一个utils.js,比如下面，一个请求数据的公共方法
    utils.js
   ```
   function fetchData(url){
    var promise = new Promise((resolve,reject) => {
        wx.request({
        url: url,
        data:{},
        method:'GET',
        header:{
            //访问豆瓣的限制，不能给application
            'content-type':'json'
        },
        success:resolve,
        fail:reject
        })
    });
    return promise; //这里必须的返回promise，这样春泥更then
    };
    export {fetchData};
   ```
   需要使用该方法的其他js中，比如home.js
   ```
    首先进行引入import {fetchData} from '../../utils/utils.js'; 
    需要的位置进行调用；
    var url1 = 'https://m.douban.com/rexxar/api/v2/muzzy/columns/10008/items?start=0&count=6';
    fetchData(url1).then((res,rej)=>{
        console.log(res)
        this.fn(res);  这个fn是处理数据的一个函数
    }) 
   ```

## 当使用input进行输入值就进行搜索的时候，
   当使用input进行输入值就进行搜索的时候，最好写一个定时器，稍微有一定的延迟在进行请求，要不会频繁的发送请求，影响性能；
  ```
   然后每次进来这个函数的时候，都进行清一次定时器
   比如 <input bindinput="aa"/>
   aa（e）{
    延迟500毫秒，最终只查询一次
    clearTimeout（this.time）  注意这个this，
    this.time = settimeout(()=>{  fn() },500)
   }
```
## 判断是否不是空字符串的方法  str ！=""；