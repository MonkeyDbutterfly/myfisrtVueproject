

## 制作首页app组件
1.完成Header区域，使用mint-ui中的Header组件
2.制作tabber区域，使用MUI中的Tabber.html
  +在制作购物车小图标的时候，操作会相对多一些
  +先把扩展图标的css样式拷贝到项目中去
  +拷贝扩展字体库的ttf到项目中去
  +为购物车小图标跑添加如下样式
  mui-icon mui-icon-extra mui-icon-extra-cart
3.要在中间放置一个router-view 来展示路由



##2019-03-19  
一.完成新闻页面发表评论的功能
  +1.进行数据的双向绑定 v-model 
  +2.使用post请求发送数据  
  1)判断输入的字符串是否为空
  2)使用get请求 地址为 http://www.liulongbin.top:3005/api/postcomment/:id;
  3)发送成功后将发送的数据unshift到commentsList的数组里
  4)清空msg

二.改造图片分享的路由跳转
  1. 新建一个组件页面 
  2. 改造路由链接 `HomeContainer.vue`
  3. 添加一个路由规则 `router.js`

三.实现图片分享顶部的导航
  1.采用MUI `tab-top-webview-main.html`控件来实现
  2. 当拿到 UI 代码片段之后，需要把 `mui-fullscreen` 去掉
  3. 当页面布局没有大问题之后，发现无法实现滑动效果，此时，需要使用官方推荐的形式，去初始化一下控件,具体初始化的方式，参考官方文档:`http://dev.dcloud.net.cn/mui/ui/#scroll`

四.当在项目中引入了MUI的JS文件报错的问题：
> 报错信息：Uncaught TypeError: 'caller', 'callee', and 'arguments' properties may not be accessed on strict mode functions or the arguments objects for calls to them
1. 分析问题的原因：
 + webpack 打包出来的  bundle.js 中，默认启用了严格模式
 + 在项目中，import 导入的 mui.js 中，使用了 callee caller 这些特性， 但是这些特性在严格模式中不支持，会报错；
 + 经过分析：发现，关闭webpack的严格模式，更容易一些，因为不再需要修改mui.js的源代码了；
 + 如何关闭webpack的严格模式呢？使用一个webpack的插件：`https://blog.csdn.net/belonghuang157405/article/details/86522930`
 
 问题：
1.加载时需要刷新才能滑动
 将mui的初始化mui.scroll的方法时 页面还没加载完成
 应当将初始化的操作放入vue实例的mounted的函数
2.移出滑动区警告的操作
 为'mui-slider'的类添加touch-active：pan-x;  

3.解决 tabbar 区域 和 图片分类列表JS行为冲突的问题
1. 冲突的原因：每一个tabbar 按钮的 类样式`mui--tab-item`和JS行为有冲突；
2. 解决的思路：把所有和 `mui-tab-item`相关的样式粘出来，然后改个类名，重新把自己的类名，替换掉之前的即可

五.获取图片分类数据并加载到页面上
1.通过unshift（）的函数 将头部的id 和title添加到获取的数据头
2.默认是全部到航头被选中  手动添加:class="item.id===0?'mui-control-item mui-active':'mui-control-item'"选中第一个为高亮

## 默认加载全部的分类数据
1. 定义一个`getPhotoByCategory`的方法， 接收一个分类Id，然后根据Id获取所有的图片列表
2. 把获取到的图片，挂载到 `photoshare`中
3. 根据Mint-UI 的 lazy-load 组件，实现图片的懒加载效果；
 + 在实现懒加载效果时候，需要注意： 图片的 URL 地址，需要通过`v-lazy`来指定；
 + 需要把图片的样式也粘贴到项目中，同时，把 `image`选择器修改成`img`标签选择器；

## 美化图片列表

 ## 点击图片分类，切换图片列表数据
 1. 为每一个分类，添加点击事件，重新发起数据请求，来获取图片列表

 ## 点击图片跳转到图片详情页面的路由改造
 1. 把 每个图片的 li 改造成`router-link`同时，要添加`tag="li"`属性，而且，跳转的链接，需要使用`:to`来设置，因为需要传递这个图片的Id过去；
2. 添加一个路由的规则，在规则中，使用`props` 来快速传递路由中的参数；
3. 添加路由对应的组件

## 开发图片的详情页面
1. 根据`props`中接收的图片Id，获取图片的详情数据
2. 根据图片的详情，渲染页面最基本的结构
3. 发表评论的功能，直接引用现成的组件：
 + 使用 import 把评论子组件导入到当前组件中
 + 把 导入的评论组件对象，注册 为 当前 图片详情页面的子组件 `components`
 + 以标签形式，把注册好的评论子组件名称，引入到页面指定区域
 + 注意：如果要实现评论的功能，需要在（以标签形式）引用评论子组件的时候，为其传递一个 `newsid` 的属性，属性值，应该是当前图片的id;

 ## 实现缩略图的效果
 1. 先加载所有的图片并显示到页面上；
 2. 使用`vue2-preview`插件来实现缩略图效果；
    具体参考https://www.npmjs.com/package/vue2-preview
 3. 如何使用缩略图组件呢：
  + 运行`cnpm i vue2-preview -S`安装
  + 在 全局的配置文件`main.js`中，安装此组件：
  ```
  import VuePreview from 'vue2-preview'
  Vue.use(VuePreview)
  ```
  + 在页面中，先把获取到的图片，按照标准的格式做一层包装：为每个图片添加`w`和`h`属性；
  + 把官方提供的 例子中的`img`标签粘贴到页面中，去渲染数据就行了；