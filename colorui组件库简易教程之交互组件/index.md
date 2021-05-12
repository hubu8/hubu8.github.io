# ColorUI交互组件


<!--more-->

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/miao_yf/article/details/103081988)

**[官方示例](https://github.com/weilanwl/ColorUI)在此！！！[官方示例](https://github.com/weilanwl/ColorUI)在此！！！[官方示例](https://github.com/weilanwl/ColorUI)在此！！！**

**这主要是基于uni-app开发的，所以，请多看uni-app文档，了解一些标签、属性，可以更好的使用该组件**

**目录**

[交互组件](#t0)

[Bar操作条](#t1)

[Nav导航栏](#t2)

[List列表](#t3)

[Card卡片](#t4)

[Form表单](#t5)

[TimeLine时间轴](#t6)

[Chat聊天](#t7)

[Swiper轮播图](#t8)

[Modal模态框](#t9)

[Steps步骤条](#t10)

[扩展插件](#t11) 

* * *

交互组件
----

### Bar操作条

**底部操作条**

> 我们可以使用Bar操作条自定义小程序底部的tabBar，就拿官方示例代码来说吧。
> 
> 当我们要自定义tabBar的时候，pages.json文件中的tabBar配置就不写了，直接在首页上代码，pages.json文件中的pages数组中第一项表示应用启动页，即首页。示例中通过变量PageCur来切换不同tabBar页面，控制tabBar图标文字的样式切换。
> 
> 底部操作条包括底部的tabBar和商场小程序加入购物车的操作条，底部操作条中cu-bar和tabbar是必备的class，foot则设置该操作条固定在页面底部。

![示例操作条](https://img-blog.csdnimg.cn/20191115140850835.png)

```
1.  <template>
2.  	<view>
3.  		<basics v-if="PageCur=='basics'"></basics>
4.  		<components v-if="PageCur=='component'"></components>
5.  		<plugin v-if="PageCur=='plugin'"></plugin>
6.  		<view class="cu-bar tabbar bg-white shadow foot">
7.  			<view class="action" @click="NavChange" data-cur="basics">
8.  				<view class='cuIcon-cu-image'>
9.  					<image :src="'/static/tabbar/basics' + [PageCur=='basics'?'_cur':''] + '.png'"></image>
10.  				</view>
11.  				<view :class="PageCur=='basics'?'text-green':'text-gray'">元素</view>
12.  			</view>
13.  			<view class="action" @click="NavChange" data-cur="component">
14.  				<view class='cuIcon-cu-image'>
15.  					<image :src="'/static/tabbar/component' + [PageCur == 'component'?'_cur':''] + '.png'"></image>
16.  				</view>
17.  				<view :class="PageCur=='component'?'text-green':'text-gray'">组件</view>
18.  			</view>
19.  			<view class="action" @click="NavChange" data-cur="plugin">
20.  				<view class='cuIcon-cu-image'>
21.  					<image :src="'/static/tabbar/plugin' + [PageCur == 'plugin'?'_cur':''] + '.png'"></image>
22.  				</view>
23.  				<view :class="PageCur=='plugin'?'text-green':'text-gray'">扩展</view>
24.  			</view>
25.  		</view>
26.  	</view>
27.  </template>

29.  <script>
30.  	export default {
31.  		data() {
32.  		return {
33.  				PageCur: 'basics'
34.  			}
35.  		},
36.  		methods: {
37.  			NavChange: function(e) {
38.  				this.PageCur = e.currentTarget.dataset.cur
39.  			}
40.  		}
41.  	}
42.  </script> 
```

> 操作条中的图标，可以是自定义引入的图片，也可以是这个组件中的图标，加角标就是上一章在[基础元素](https://blog.csdn.net/miao_yf/article/details/102971767)中介绍的标签组件cu-tag、badge，背景颜色以及文字图标颜色都可以添加相应的颜色class进行自定义

![操作条1](https://img-blog.csdnimg.cn/20191115141516589.png)

```


1.  <view class="cu-bar tabbar bg-white">
2.  	<view class="action">
3.  		<view class="cuIcon-cu-image">
4.  			<image src="/static/tabbar/basics_cur.png"></image>
5.  		</view>
6.  		<view class="text-green">元素</view>
7.  	</view>
8.  	<view class="action">
9.  		<view class="cuIcon-cu-image">
10.  			<image src="/static/tabbar/component.png"></image>
11.  		</view>
12.  		<view class="text-gray">组件</view>
13.  	</view>
14.  	<view class="action">
15.  		<view class="cuIcon-cu-image">
16.  			<image src="/static/tabbar/plugin.png"></image>
17.  			<view class="cu-tag badge">99</view>
18.  		</view>
19.  		<view class="text-gray">扩展</view>
20.  	</view>
21.  	<view class="action">
22.  		<view class="cuIcon-cu-image">
23.  			<image src="/static/tabbar/about.png"></image>
24.  			<view class="cu-tag badge"></view>
25.  		</view>
26.  		<view class="text-gray">关于</view>
27.  	</view>
28.  </view>

30.  <view class="cu-bar tabbar margin-bottom-xl bg-black">
31.  	<view class="action text-orange">
32.  		<view class="cuIcon-homefill"></view> 首页
33.  	</view>
34.  	<view class="action text-gray">
35.  		<view class="cuIcon-similar"></view> 分类
36.  	</view>
37.  	<view class="action text-gray">
38.  		<view class="cuIcon-recharge"></view>
39.  		积分
40.  	</view>
41.  	<view class="action text-gray">
42.  		<view class="cuIcon-cart">
43.  			<view class="cu-tag badge">99</view>
44.  		</view>
45.  		购物车
46.  	</view>
47.  	<view class="action text-gray">
48.  		<view class="cuIcon-my">
49.  			<view class="cu-tag badge"></view>
50.  		</view>
51.  		我的
52.  	</view>
53.  </view>


```

>  这种操作条就是中间设置了一个midButton，只需在中间的view上添加class add-action，其他设置同上，不赘述

![操作条2](https://img-blog.csdnimg.cn/20191115142637207.png)

```


1.  <view class="cu-bar tabbar margin-bottom-xl bg-white">
2.  	<view class="action text-green">
3.  		<view class="cuIcon-homefill"></view> 首页
4.  	</view>
5.  	<view class="action text-gray">
6.  		<view class="cuIcon-similar"></view> 分类
7.  	</view>
8.  	<view class="action text-gray add-action">
9.  		<button class="cu-btn cuIcon-add bg-green shadow"></button>
10.  		发布
11.  	</view>
12.  	<view class="action text-gray">
13.  		<view class="cuIcon-cart">
14.  			<view class="cu-tag badge">99</view>
15.  		</view>
16.  		购物车
17.  	</view>
18.  	<view class="action text-gray">
19.  		<view class="cuIcon-my">
20.  			<view class="cu-tag badge"></view>
21.  		</view>
22.  		我的
23.  	</view>
24.  </view>


```

> 加入购物车的操作条增加了shop类名，但是我不清楚，加不加shop有什么区别，我删了他，样式好像也没变，有大佬懂得望指教。submit类名用于加入购物车和立即购买等操作，样式可以撑开占满。 

![shop1](https://img-blog.csdnimg.cn/20191115143012720.png)

```


1.  <view class="cu-bar bg-white tabbar border shop">
2.  	<button class="action" open-type="contact">
3.  		<view class="cuIcon-service text-green">
4.  			<view class="cu-tag badge"></view>
5.  		</view>
6.  		客服
7.  	</button>
8.  	<view class="action text-orange">
9.  		<view class="cuIcon-favorfill"></view> 已收藏
10.  	</view>
11.  	<view class="action">
12.  		<view class="cuIcon-cart">
13.  			<view class="cu-tag badge">99</view>
14.  		</view>
15.  		购物车
16.  	</view>
17.  	<view class="bg-red submit">立即订购</view>
18.  </view>


```

> 这种样式就是把上面那种换成了button标签，设置成round圆形 

![shop2](https://img-blog.csdnimg.cn/20191115144900942.png)

```


1.  <view class="cu-bar bg-white tabbar border shop">
2.  	<button class="action" open-type="contact">
3.  		<view class="cuIcon-service text-green">
4.  			<view class="cu-tag badge"></view>
5.  		</view> 客服
6.  	</button>
7.  	<view class="action">
8.  		<view class="cuIcon-cart">
9.  			<view class="cu-tag badge">99</view>
10.  		</view>
11.  		购物车
12.  	</view>
13.  	<view class="btn-group">
14.  		<button class="cu-btn bg-orange round shadow-blur">加入购物车</button>
15.  		<button class="cu-btn bg-red round shadow-blur">立即订购</button>
16.  	</view>
17.  </view>


```

**标题操作条**

>  看下面代码注释吧，一条对应一条，清晰一点

**![标题1](https://img-blog.csdnimg.cn/2019111515084340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)![标题2](https://img-blog.csdnimg.cn/20191115150857366.png)**

```


1.  <view class="box">
2.  //第一种和第二种都是标题和类似边框的组合样式，添加border-title类名， 底部样式是text标签的last-child选择器设置的，可以自定义颜色和长度
3.  	<view class="cu-bar bg-white">
4.  		<view class="action border-title">
5.  			<text class="text-xl text-bold">关于我们</text>
6.  			<text class="bg-grey" style="width:2rem"></text>
7.  		</view>
8.  	</view>
9.  	<view class="cu-bar bg-white">
10.  		<view class="action border-title">
11.  			<text class="text-xl text-bold text-blue">关于我们</text>
12.  			<text class="bg-gradual-blue" style="width:3rem"></text>
13.  		</view>
14.  	</view>
15.  //下面这两种和上面的原理相同，添加的是sub-title类名，底部可以设置文字
16.  	<view class="cu-bar bg-white">
17.  		<view class="action sub-title">
18.  			<text class="text-xl text-bold text-green">关于我们</text>
19.  			<text class="bg-green"></text>
20.  		</view>
21.  	</view>
22.  	<view class="cu-bar bg-white">
23.  		<view class="action sub-title">
24.  			<text class="text-xl text-bold text-blue">关于我们</text>
25.  			<text class="text-ABC text-blue">about</text>
26.  		</view>
27.  	</view>
28.  //这一种主要是类名self-end，设置了最后一行字的位置，就是css属性align-self: flex-end;详情可以去看看flex布局
29.  	<view class="cu-bar bg-white">
30.  		<view class="action">
31.  			<text class="text-xl text-bold">关于我们</text>
32.  			<text class="text-Abc text-gray self-end margin-left-sm">about</text>
33.  		</view>
34.  	</view>
35.  //最后这两种就是简单的添加了两个图标，没什么好说的
36.  	<view class="cu-bar bg-white">
37.  		<view class="action">
38.  			<text class="cuIcon-title text-green"></text>
39.  			<text class="text-xl text-bold">关于我们</text>
40.  		</view>
41.  	</view>
42.  	<view class="cu-bar bg-white">
43.  		<view class="action">
44.  			<text class="cuIcon-titles text-green"></text>
45.  			<text class="text-xl text-bold">关于我们</text>
46.  		</view>
47.  	</view>
48.  </view>


```

**顶部操作条**

> 顶部操作条具体设置方法请移步上一章[自定义导航栏](https://blog.csdn.net/miao_yf/article/details/102971767#%E5%BC%80%E5%A7%8B)，以下几种样式可以参考，就不详解了。

![顶部1](https://img-blog.csdnimg.cn/20191115154708964.png) ![顶部2](https://img-blog.csdnimg.cn/2019111515472512.png)

```


1.  <view class="box">
2.  	<view class="cu-bar bg-white">
3.  		<view class="action">
4.  			<text class="cuIcon-back text-gray"></text> 返回
5.  		</view>
6.  		<view class="content text-bold">操作条	</view>
7.  	</view>
8.  	<view class="cu-bar bg-white">
9.  		<view class="action">
10.  			<text class="cuIcon-homefill text-gray"></text> 首页
11.  		</view>
12.  		<view class="content text-bold">鲜亮的高饱和色彩，专注视觉的小程序组件库</view>
13.  		<view class="action">
14.  			<text class="cuIcon-cardboardfill text-grey"></text>
15.  			<text class="cuIcon-recordfill text-red"></text>
16.  		</view>
17.  	</view>
18.  	<view class="cu-bar bg-blue">
19.  		<view class="action">
20.  			<text class="cuIcon-close"></text>关闭
21.  		</view>
22.  		<view class="content text-bold">海蓝</view>
23.  	</view>
24.  	<view class="cu-bar bg-black search">
25.  		<view class="cu-avatar round" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big91012.jpg);"></view>
26.  		<view class="content">ColorUI</view>
27.  		<view class="action">
28.  			<text class="cuIcon-more"></text>
29.  		</view>
30.  	</view>
31.  </view>


```

 **搜索操作条**

> 搜索操作条主要就是search-form，配合round和radius设置input框的样式（越写我越发现，新的东西不多，都是基础元素拼凑出来的，示例就是一个参考样式，耐心仔细看，很简单）

![搜索1](https://img-blog.csdnimg.cn/20191115160458828.png)![搜索2](https://img-blog.csdnimg.cn/20191115160513596.png)

```


1.  <view class="box">
2.  	<view class="cu-bar search bg-white">
3.  		<view class="search-form round">
4.  			<text class="cuIcon-search"></text>
5.  			<input @focus="InputFocus" @blur="InputBlur" :adjust-position="false" type="text" placeholder="搜索图片、文章、视频"
6.  			 confirm-type="search"></input>
7.  		</view>
8.  		<view class="action">
9.  			<button class="cu-btn bg-green shadow-blur round">搜索</button>
10.  		</view>
11.  	</view>
12.  	<view class="cu-bar search bg-white">
13.  		<view class="cu-avatar round" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big11010.jpg"></view>
14.  		<view class="search-form round">
15.  			<text class="cuIcon-search"></text>
16.  			<input @focus="InputFocus" @blur="InputBlur" :adjust-position="false" type="text" placeholder="搜索图片、文章、视频"
17.  			 confirm-type="search"></input>
18.  		</view>
19.  		<view class="action">
20.  			<text>广州</text>
21.  			<text class="cuIcon-triangledownfill"></text>
22.  		</view>
23.  	</view>
24.  	<view class="cu-bar bg-red search">
25.  		<view class="cu-avatar round" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big114004.jpg);"></view>
26.  		<view class="search-form radius">
27.  			<text class="cuIcon-search"></text>
28.  			<input @focus="InputFocus" @blur="InputBlur" :adjust-position="false" type="text" placeholder="搜索图片、文章、视频"
29.  			 confirm-type="search"></input>
30.  		</view>
31.  		<view class="action">
32.  			<text>广州</text>
33.  			<text class="cuIcon-triangledownfill"></text>
34.  		</view>
35.  	</view>
36.  	<view class="cu-bar bg-cyan search">
37.  		<view class="search-form radius">
38.  			<text class="cuIcon-search"></text>
39.  			<input @focus="InputFocus" @blur="InputBlur" :adjust-position="false" type="text" placeholder="搜索图片、文章、视频"
40.  			 confirm-type="search"></input>
41.  		</view>
42.  		<view class="action">
43.  			<text class="cuIcon-close"></text>
44.  			<text>取消</text>
45.  		</view>
46.  	</view>
47.  </view>


```

**按钮组操作条** 

> 这就是几个按钮，cu-bar操作条类名就是为这个按钮组设置了平铺的样式，你看第二组按钮，我删除了cu-bar类名，效果对比就很明显。

![按钮组操作条](https://img-blog.csdnimg.cn/20191115161318801.png)

```


1.  <view class="box">
2.  	<view class="cu-bar btn-group">
3.  		<button class="cu-btn bg-green shadow-blur round lg">保存</button>
4.  	</view>
5.  	<view class="btn-group">
6.  		<button class="cu-btn bg-green shadow-blur">保存</button>
7.  		<button class="cu-btn text-green line-green shadow">上传</button>
8.  	</view>
9.  	<view class="cu-bar btn-group">
10.  		<button class="cu-btn bg-green shadow-blur round">保存</button>
11.  		<button class="cu-btn bg-blue shadow-blur round">提交</button>
12.  	</view>
13.  </view>


```

**输入操作条**

> 主要由cu-bar和input设置出大体样式，内部填充自定义，以下样式供参考 

![输入](https://img-blog.csdnimg.cn/20191115162012367.png)

```


1.  <view class="box">
2.  	<view class="cu-bar input">
3.  		<view class="action">
4.  			<text class="cuIcon-sound text-grey"></text>
5.  		</view>
6.  		<input @focus="InputFocus" @blur="InputBlur" :adjust-position="false" class="solid-bottom" :focus="false" maxlength="300"
7.  		 cursor-spacing="10"></input>
8.  		<view class="action">
9.  			<text class="cuIcon-emojifill text-grey"></text>
10.  		</view>
11.  		<button class="cu-btn bg-green shadow-blur">发送</button>
12.  	</view>

14.  	<view class="cu-bar input">
15.  		<view class="cu-avatar round" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big91012.jpg);"></view>
16.  		<view class="action">
17.  			<text class="cuIcon-roundaddfill text-grey"></text>
18.  		</view>
19.  		<input @focus="InputFocus" @blur="InputBlur" :adjust-position="false" class="solid-bottom" maxlength="300"
20.  		 cursor-spacing="10"></input>
21.  		<view class="action">
22.  			<text class="cuIcon-emojifill text-grey"></text>
23.  		</view>
24.  		<button class="cu-btn bg-green shadow-blur">发送</button>
25.  	</view>
26.  </view>


```

**操作条相关class**

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>cu-bar</td><td>底部操作条必选值</td><td>——</td></tr><tr><td>tabbar</td><td>底部操作条必选值</td><td>——</td></tr><tr><td>foot</td><td>固定定位在页面底部</td><td>——</td></tr><tr><td>add-action</td><td>小程序tabBar的midButton</td><td>——</td></tr><tr><td>shop</td><td>购物操作条必选值</td><td>——</td></tr><tr><td>submit</td><td>提交按钮</td><td>——</td></tr><tr><td>border-title</td><td>带下边框样式的标题</td><td>——</td></tr><tr><td>sub-title</td><td>带副标题样式的标题</td><td>——</td></tr><tr><td>self-end</td><td>css属性align-self: flex-end;</td><td>——</td></tr><tr><td>search-form</td><td>搜索框</td><td>——</td></tr><tr><td>input</td><td>输入框样式</td><td>——</td></tr></tbody></table>

### Nav导航栏

> nav导航栏是结合uni-app的scroll-view标签设计出来的，使用的时候一些属性可以参考一下uni-app的文档[https://uniapp.dcloud.io/component/scroll-view](https://uniapp.dcloud.io/component/scroll-view)，类名nav和cu-item是必选值

**默认**

> 不知道咋解释，看代码很简单，就是通过index值进行切换每个tab，及其字体边框样式，我就不赘述了

**![默认nav](https://img-blog.csdnimg.cn/20191118143525152.png)**

```


1.  <scroll-view scroll-x class="bg-white nav" scroll-with-animation :scroll-left="scrollLeft">
2.  	<view class="cu-item" :class="index==TabCur?'text-green cur':''" v-for="(item,index) in 10" :key="index" @tap="tabSelect" :data-id="index">
3.  		Tab{{index}}
4.  	</view>
5.  </scroll-view>

7.  <script>
8.  	export default {
9.  		data() {
10.  			return {
11.  				TabCur: 0,
12.  				scrollLeft: 0
13.  			};
14.  		},
15.  		methods: {
16.  			tabSelect(e) {
17.  				this.TabCur = e.currentTarget.dataset.id;
18.  				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
19.  			}
20.  		}
21.  	}
22.  </script>


```

**居中** 

> 通过text-center居中

![居中nav](https://img-blog.csdnimg.cn/20191118144326708.png)

```


1.  <scroll-view scroll-x class="bg-white nav text-center">
2.  	<view class="cu-item" :class="index==TabCur?'text-blue cur':''" v-for="(item,index) in 3" :key="index" @tap="tabSelect" :data-id="index">
3.  		Tab{{index}}
4.  	</view>
5.  </scroll-view>


```

**平分**

> 设置flex和flex-sub实现弹性布局，flex-sub就是css属性flex:1，将弹性盒子内子元素按照1:1:1:1来分配空间

 ![平分nav](https://img-blog.csdnimg.cn/201911181443543.png)

```


1.  <scroll-view scroll-x class="bg-white nav">
2.  	<view class="flex text-center">
3.  		<view class="cu-item flex-sub" :class="index==TabCur?'text-orange cur':''" v-for="(item,index) in 4" :key="index" @tap="tabSelect" :data-id="index">
4.  			Tab{{index}}
5.  		</view>
6.  	</view>
7.  </scroll-view>


```

**背景**

>  bg-{{color}}，参考[背景色](https://blog.csdn.net/miao_yf/article/details/102971767#%E8%83%8C%E6%99%AF%C2%A0)

 ![背景nav](https://img-blog.csdnimg.cn/20191118144414392.png)

```


1.  <scroll-view scroll-x class="bg-red nav text-center">
2.  	<view class="cu-item" :class="index==TabCur?'text-white cur':''" v-for="(item,index) in 3" :key="index" @tap="tabSelect" :data-id="index">
3.  		Tab{{index}}
4.  	</view>
5.  </scroll-view>


```

**图标**

>  cuIcon-{{icon}}，参考[图标](https://blog.csdn.net/miao_yf/article/details/102971767#ICON%E5%9B%BE%E6%A0%87)

 ![图标nav](https://img-blog.csdnimg.cn/20191118144439675.png)

```


1.  <scroll-view scroll-x class="bg-green nav text-center">
2.  	<view class="cu-item" :class="0==TabCur?'text-white cur':''" @tap="tabSelect" data-id="0">
3.  		<text class="cuIcon-camerafill"></text> 数码
4.  	</view>
5.  	<view class="cu-item" :class="1==TabCur?'text-white cur':''" @tap="tabSelect" data-id="1">
6.  		<text class="cuIcon-upstagefill"></text> 排行榜
7.  	</view>
8.  	<view class="cu-item" :class="2==TabCur?'text-white cur':''" @tap="tabSelect" data-id="2">
9.  		<text class="cuIcon-clothesfill"></text> 皮肤
10.  	</view>
11.  </scroll-view>


```

**导航栏相关class**

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>nav</td><td>导航栏必选值</td><td>——</td></tr><tr><td>cu-item</td><td>导航栏子元素</td><td>——</td></tr><tr><td>flex</td><td>flex布局</td><td>——</td></tr><tr><td>flex-sub</td><td>flex:1，平分</td><td>——</td></tr><tr><td>text-center</td><td>居中</td><td>——</td></tr></tbody></table>

### List列表

> cu-list和cu-item配合使用

**宫格列表**

> 宫格列表就是列表结合grid布局设计出的样式，通过col-{{number}}设置每行的个数，number取值1~5，默认有边框，可以加类名no-border去除边框

![宫格列表1](https://img-blog.csdnimg.cn/20191118153358267.png)![宫格列表](https://img-blog.csdnimg.cn/20191118153413191.png)

```


1.  <view class="cu-list grid" :class="['col-' + gridCol,gridBorder?'':'no-border']">
2.  	<view class="cu-item" v-for="(item,index) in cuIconList" :key="index" v-if="index<gridCol*2">
3.  		<view :class="['cuIcon-' + item.cuIcon,'text-' + item.color]">
4.  			<view class="cu-tag badge" v-if="item.badge!=0">
5.  				<block v-if="item.badge!=1">{{item.badge>99?'99+':item.badge}}</block>
6.  			</view>
7.  		</view>
8.  		<text>{{item.name}}</text>
9.  	</view>
10.  </view>

12.  data() {
13.  	return {
14.  		cuIconList: [{cuIcon: 'cardboardfill',color: 'red',badge: 120,name: 'VR'}, 
15.  		{cuIcon: 'recordfill',color: 'orange',badge: 1,name: '录像'},
16.  		{cuIcon: 'picfill',color: 'yellow',badge: 0,name: '图像'}, 
17.  		{cuIcon: 'noticefill',color: 'olive',badge: 22,name: '通知'},
18.  		{cuIcon: 'upstagefill',color: 'cyan',badge: 0,name: '排行榜'	}, 
19.  		{cuIcon: 'clothesfill',color: 'blue',badge: 0,name: '皮肤'}, 
20.  		{cuIcon: 'discoverfill',color: 'purple',badge: 0,name: '发现'}, 
21.  		{cuIcon: 'questionfill',color: 'mauve',badge: 0,name: '帮助'},
22.  		{cuIcon: 'commandfill',color: 'purple',badge: 0,name: '问答'}, 
23.  		{cuIcon: 'brandfill',color: 'mauve',badge: 0,name: '版权'}],
24.          gridCol: 3,
25.  		gridBorder: false
26.  	};
27.  },


```

**菜单列表** 

> 菜单列表就是列表结合类名menu设计出的样式，可以通过sm-border设置每一行的短边框，通过card-menu将列表设置成卡片样式，通过在列表子元素上添加arrow设置箭头，变化样式效果可以由以下两图对比

**![菜单列表1](https://img-blog.csdnimg.cn/20191118154545385.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)**![菜单列表2](https://img-blog.csdnimg.cn/20191118154614313.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-list menu" :class="[menuBorder?'sm-border':'',menuCard?'card-menu margin-top':'']">
2.  	<view class="cu-item" :class="menuArrow?'arrow':''">
3.  		<view class="content">
4.  			<text class="cuIcon-circlefill text-grey"></text>
5.  			<text class="text-grey">图标 + 标题</text>
6.  		</view>
7.  	</view>
8.  	<view class="cu-item" :class="menuArrow?'arrow':''">
9.  		<view class="content">
10.  			<image src="/static/logo.png" class="png" mode="aspectFit"></image>
11.  			<text class="text-grey">图片 + 标题</text>
12.  		</view>
13.  	</view>
14.  	<view class="cu-item" :class="menuArrow?'arrow':''">
15.  		<button class="cu-btn content" open-type="contact">
16.  			<text class="cuIcon-btn text-olive"></text>
17.  			<text class="text-grey">Open-type 按钮</text>
18.  		</button>
19.  	</view>
20.  	<view class="cu-item" :class="menuArrow?'arrow':''">
21.  		<navigator class="content" hover-class="none" url="../list/list" open-type="redirect">
22.  			<text class="cuIcon-discoverfill text-orange"></text>
23.  			<text class="text-grey">Navigator 跳转</text>
24.  		</navigator>
25.  	</view>
26.  	<view class="cu-item" :class="menuArrow?'arrow':''">
27.  		<view class="content">
28.  			<text class="cuIcon-emojiflashfill text-pink"></text>
29.  			<text class="text-grey">头像组</text>
30.  		</view>
31.  		<view class="action">
32.  			<view class="cu-avatar-group">
33.  				<view class="cu-avatar round sm" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big10001.jpg);"></view>
34.  				<view class="cu-avatar round sm" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big81005.jpg);"></view>
35.  				<view class="cu-avatar round sm" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big25002.jpg);"></view>
36.  				<view class="cu-avatar round sm" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big91012.jpg);"></view>
37.  			</view>
38.  			<text class="text-grey text-sm">4 人</text>
39.  		</view>
40.  	</view>
41.  	<view class="cu-item" :class="menuArrow?'arrow':''">
42.  		<view class="content">
43.  			<text class="cuIcon-btn text-green"></text>
44.  			<text class="text-grey">按钮</text>
45.  		</view>
46.  		<view class="action">
47.  			<button class="cu-btn round bg-green shadow">
48.  				<text class="cuIcon-upload"></text> 上传</button>
49.  		</view>
50.  	</view>
51.  	<view class="cu-item" :class="menuArrow?'arrow':''">
52.  		<view class="content">
53.  			<text class="cuIcon-tagfill text-red  margin-right-xs"></text>
54.  			<text class="text-grey">标签</text>
55.  		</view>
56.  		<view class="action">
57.  			<view class="cu-tag round bg-orange light">音乐</view>
58.  			<view class="cu-tag round bg-olive light">电影</view>
59.  			<view class="cu-tag round bg-blue light">旅行</view>
60.  		</view>
61.  	</view>
62.  	<view class="cu-item" :class="menuArrow?'arrow':''">
63.  		<view class="content">
64.  			<text class="cuIcon-warn text-green"></text>
65.  			<text class="text-grey">文本</text>
66.  		</view>
67.  		<view class="action">
68.  			<text class="text-grey text-sm">小目标还没有实现！</text>
69.  		</view>
70.  	</view>
71.  	<view class="cu-item">
72.  		<view class="content padding-tb-sm">
73.  			<view>
74.  				<text class="cuIcon-clothesfill text-blue margin-right-xs"></text> 多行Item</view>
75.  			<view class="text-gray text-sm">
76.  				<text class="cuIcon-infofill margin-right-xs"></text> 小目标还没有实现！</view>
77.  		</view>
78.  		<view class="action">
79.  			<switch class="switch-sex" @change="SwitchSex" :class="skin?'checked':''" :checked="skin?true:false"></switch>
80.  		</view>
81.  	</view>
82.  </view>

84.  <script>
85.  	export default {
86.  		data() {
87.  			return {
88.  				menuBorder: false,
89.  				menuArrow: false,
90.  				menuCard: false,
91.  				skin: false
92.  			};
93.  		}
94.  	}
95.  </script>


```

**消息列表**

> 消息列表类似于QQ消息微信消息的列表，基本组成为头像和内容，有点像带头像的菜单列表。基本格式如下：

```


1.  <view class="cu-list menu-avatar">
2.  	<view class="cu-item">
3.  		<view class="cu-avatar">头像</view>
4.  		<view class="content">消息内容</view>
5.  	</view>
6.  </view>


```

具体实例请看下面的代码：

![消息列表](https://img-blog.csdnimg.cn/20191118161710463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-list menu-avatar">
2.  	<view class="cu-item">
3.  		<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big10001.jpg);"></view>
4.  		<view class="content">
5.  			<view class="text-grey">凯尔</view>
6.  			<view class="text-gray text-sm flex">
7.  				<view class="text-cut">
8.  					<text class="cuIcon-infofill text-red  margin-right-xs"></text>
9.  					我已天理为凭，踏入这片荒芜，不再受凡人的枷锁遏制。我已天理为凭，踏入这片荒芜，不再受凡人的枷锁遏制。
10.  				</view> </view>
11.  		</view>
12.  		<view class="action">
13.  			<view class="text-grey text-xs">22:20</view>
14.  			<view class="cu-tag round bg-grey sm">5</view>
15.  		</view>
16.  	</view>
17.  	<view class="cu-item">
18.  		<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/img/champion/Taric.png);">
19.  			<view class="cu-tag badge">99+</view>
20.  		</view>
21.  		<view class="content">
22.  			<view class="text-grey">
23.  				<view class="text-cut">瓦洛兰之盾-塔里克</view>
24.  				<view class="cu-tag round bg-orange sm">战士</view>
25.  			</view>
26.  			<view class="text-gray text-sm flex">
27.  				<view class="text-cut">
28.  					塔里克是保护者星灵，用超乎寻常的力量守护着符文之地的生命、仁爱以及万物之美。塔里克由于渎职而被放逐，离开了祖国德玛西亚，前去攀登巨神峰寻找救赎，但他找到的却是来自星界的更高层的召唤。现在的塔里克与古代巨神族的神力相融合，以瓦洛兰之盾的身份，永不疲倦地警惕着阴险狡诈的虚空腐化之力。
29.  				</view>
30.  			</view>
31.  		</view>
32.  		<view class="action">
33.  			<view class="text-grey text-xs">22:20</view>
34.  			<view class="cuIcon-notice_forbid_fill text-gray"></view>
35.  		</view>
36.  	</view>
37.  	<view class="cu-item ">
38.  		<view class="cu-avatar radius lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/img/champion/Morgana.png);"></view>
39.  		<view class="content">
40.  			<view class="text-pink"><view class="text-cut">莫甘娜</view></view>
41.  			<view class="text-gray text-sm flex"> <view class="text-cut">凯尔，你被自己的光芒变的盲目！</view></view>
42.  		</view>
43.  		<view class="action">
44.  			<view class="text-grey text-xs">22:20</view>
45.  			<view class="cu-tag round bg-red sm">5</view>
46.  		</view>
47.  	</view>
48.  	<view class="cu-item grayscale">
49.  		<view class="cu-avatar radius lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big81007.jpg);"></view>
50.  		<view class="content">
51.  			<view><view class="text-cut">伊泽瑞尔</view>
52.  				<view class="cu-tag round bg-orange sm">断开连接...</view>
53.  			</view>
54.  			<view class="text-gray text-sm flex"> <view class="text-cut"> 等我回来一个打十个</view></view>
55.  		</view>
56.  		<view class="action">
57.  			<view class="text-grey text-xs">22:20</view>
58.  			<view class="cu-tag round bg-red sm">5</view>
59.  		</view>
60.  	</view>
61.  	<view class="cu-item cur">
62.  		<view class="cu-avatar radius lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big81020.jpg);">
63.  			<view class="cu-tag badge"></view>
64.  		</view>
65.  		<view class="content">
66.  			<view>
67.  				<view class="text-cut">瓦罗兰大陆-睡衣守护者-新手保护营</view>
68.  				<view class="cu-tag round bg-orange sm">6人</view>
69.  			</view>
70.  			<view class="text-gray text-sm flex">
71.  				<view class="text-cut"> 伊泽瑞尔：<text class="cuIcon-locationfill text-orange margin-right-xs"></text> 传送中...</view></view>
72.  		</view>
73.  		<view class="action">
74.  			<view class="text-grey text-xs">22:20</view>
75.  			<view class="cuIcon-notice_forbid_fill text-gray"></view>
76.  		</view>
77.  	</view>
78.  </view>


```

 **列表左滑**

> 用户开始触摸时，获取触摸点到盒子左侧的距离，根据触摸结束时的距离与开始距离作比较，判断出用户滑动的方向，如果是左滑，则添加类名move-cur，就是将该项左移260upx，右滑则回到原位不变。

![列表左滑](https://img-blog.csdnimg.cn/20191118165236932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-list menu-avatar">
2.  	<view class="cu-item" :class="modalName=='move-box-'+ index?'move-cur':''" v-for="(item,index) in 4" :key="index"
3.  	 @touchstart="ListTouchStart" @touchmove="ListTouchMove" @touchend="ListTouchEnd" :data-target="'move-box-' + index">
4.  		<view class="cu-avatar round lg" :style="[{backgroundImage:'url(https://ossweb-img.qq.com/images/lol/web201310/skin/big2100'+ (index+2) +'.jpg)'}]"></view>
5.  		<view class="content">
6.  			<view class="text-grey">文晓港</view>
7.  			<view class="text-gray text-sm">
8.  				<text class="cuIcon-infofill text-red  margin-right-xs"></text> 消息未送达</view>
9.  		</view>
10.  		<view class="action">
11.  			<view class="text-grey text-xs">22:20</view>
12.  			<view class="cu-tag round bg-grey sm">5</view>
13.  		</view>
14.  		<view class="move">
15.  			<view class="bg-grey">置顶</view>
16.  			<view class="bg-red">删除</view>
17.  		</view>
18.  	</view>
19.  </view>

21.  <script>
22.  	export default {
23.  		data() {
24.  			return {
25.  				modalName: null,
26.  				listTouchStart: 0,
27.  				listTouchDirection: null,
28.  			};
29.  		},
30.  		methods: {
31.  			// ListTouch触摸开始，获取触摸点距盒子左侧的距离
32.  			ListTouchStart(e) {
33.  				this.listTouchStart = e.touches[0].pageX
34.  			},
35.  			// ListTouch计算方向，
36.  			ListTouchMove(e) {
37.  				this.listTouchDirection = e.touches[0].pageX - this.listTouchStart > 0 ? 'right' : 'left'
38.  			},
39.  			// ListTouch计算滚动
40.  			ListTouchEnd(e) {
41.  				if (this.listTouchDirection == 'left') {
42.  					this.modalName = e.currentTarget.dataset.target
43.  				} else {
44.  					this.modalName = null
45.  				}
46.  				this.listTouchDirection = null
47.  			}
48.  		}
49.  	}
50.  </script>


```

**列表相关class** 

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>cu-list</td><td>列表必选值</td><td>——</td></tr><tr><td>cu-item</td><td>列表子元素</td><td>——</td></tr><tr><td>no-border</td><td>无边框</td><td>——</td></tr><tr><td>menu</td><td>菜单列表</td><td>——</td></tr><tr><td>sm-border</td><td>短边框</td><td>——</td></tr><tr><td>card-menu</td><td>卡片样式的菜单列表</td><td>——</td></tr><tr><td>arrow</td><td>右箭头</td><td>——</td></tr><tr><td>menu-avatar</td><td>消息列表（带头像）</td><td>——</td></tr><tr><td>cu-avatar</td><td>头像</td><td>——</td></tr><tr><td>content</td><td>内容</td><td>——</td></tr><tr><td>grayscale</td><td>灰度（background-color: #f5f5f5）</td><td>——</td></tr><tr><td>cur</td><td>background-color: #fcf7e9</td><td>&nbsp;</td></tr><tr><td>move-cur</td><td>左移（transform: translateX(-260upx)）</td><td>&nbsp;</td></tr></tbody></table>

### Card卡片

> cu-card卡片必选值

**案例类卡片**

> cu-card和case结合，在配合子元素的cu-item类名，写出案例类的样式，内部其他样式可以根据其他类名自行设置，cu-item自带margin:30px;可以通过类名no-card去除

![案例类卡片](https://img-blog.csdnimg.cn/20191120113121863.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-card case" :class="isCard?'no-card':''">
2.  	<view class="cu-item shadow">
3.  		<view class="image">
4.  			<image src="https://ossweb-img.qq.com/images/lol/web201310/skin/big10006.jpg"
5.  			 mode="widthFix"></image>
6.  			<view class="cu-tag bg-blue">史诗</view>
7.  			<view class="cu-bar bg-shadeBottom"> <text class="text-cut">我已天理为凭，踏入这片荒芜，不再受凡人的枷锁遏制。我已天理为凭，踏入这片荒芜，不再受凡人的枷锁遏制。</text></view>
8.  		</view>
9.  		<view class="cu-list menu-avatar">
10.  			<view class="cu-item">
11.  				<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big10006.jpg);"></view>
12.  				<view class="content flex-sub">
13.  					<view class="text-grey">正义天使 凯尔</view>
14.  					<view class="text-gray text-sm flex justify-between">
15.  						十天前
16.  						<view class="text-gray text-sm">
17.  							<text class="cuIcon-attentionfill margin-lr-xs"></text> 10
18.  							<text class="cuIcon-appreciatefill margin-lr-xs"></text> 20
19.  							<text class="cuIcon-messagefill margin-lr-xs"></text> 30
20.  						</view>
21.  					</view>
22.  				</view>
23.  			</view>
24.  		</view>
25.  	</view>
26.  </view>


```

**动态类卡片**

> 该样式类似于QQ动态，由cu-card和dynamic结合，再配合子元素cu-item编写而成，动态下方的评论需要添加类名comment

![动态类卡片](https://img-blog.csdnimg.cn/20191120114137548.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-card dynamic" :class="isCard?'no-card':''">
2.  	<view class="cu-item shadow">
3.  		<view class="cu-list menu-avatar">
4.  			<view class="cu-item">
5.  				<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big10006.jpg);"></view>
6.  				<view class="content flex-sub">
7.  					<view>凯尔</view>
8.  					<view class="text-gray text-sm flex justify-between">
9.  						2019年12月3日
10.  					</view>
11.  				</view>
12.  			</view>
13.  		</view>
14.  		<view class="text-content">
15.  			折磨生出苦难，苦难又会加剧折磨，凡间这无穷的循环，将有我来终结！
16.  		</view>
17.  		<view class="grid flex-sub padding-lr" :class="isCard?'col-3 grid-square':'col-1'">
18.  			<view class="bg-img " :class="isCard?'':'only-img'" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big10006.jpg);"
19.  			 v-for="(item,index) in isCard?9:1" :key="index">
20.  			</view>
21.  		</view>
22.  		<view class="text-gray text-sm text-right padding">
23.  			<text class="cuIcon-attentionfill margin-lr-xs"></text> 10
24.  			<text class="cuIcon-appreciatefill margin-lr-xs"></text> 20
25.  			<text class="cuIcon-messagefill margin-lr-xs"></text> 30
26.  		</view>

28.  		<view class="cu-list menu-avatar comment solids-top">
29.  			<view class="cu-item">
30.  				<view class="cu-avatar round" style="background-image:url(https://ossweb-img.qq.com/images/lol/img/champion/Morgana.png);"></view>
31.  				<view class="content">
32.  					<view class="text-grey">莫甘娜</view>
33.  					<view class="text-gray text-content text-df">
34.  						凯尔，你被自己的光芒变的盲目。
35.  					</view>
36.  					<view class="bg-grey padding-sm radius margin-top-sm  text-sm">
37.  						<view class="flex">
38.  							<view>凯尔：</view>
39.  							<view class="flex-sub">妹妹，你在帮他们给黑暗找借口吗?</view>
40.  						</view>
41.  					</view>
42.  					<view class="margin-top-sm flex justify-between">
43.  						<view class="text-gray text-df">2018年12月4日</view>
44.  						<view>
45.  							<text class="cuIcon-appreciatefill text-red"></text>
46.  							<text class="cuIcon-messagefill text-gray margin-left-sm"></text>
47.  						</view>
48.  					</view>
49.  				</view>
50.  			</view>
51.          </view>
52.      </view>
53.  </view>


```

**文章类卡片**

>  该卡片由cu-card和article结合，再配合子元素cu-item

![文章类卡片](https://img-blog.csdnimg.cn/2019112011453697.png)

```


1.  <view class="cu-card article" :class="isCard?'no-card':''">
2.  	<view class="cu-item shadow">
3.  		<view class="title"><view class="text-cut">无意者 烈火焚身;以正义的烈火拔出黑暗。我有自己的正义，见证至高的烈火吧。</view></view>
4.  		<view class="content">
5.  			<image src="https://ossweb-img.qq.com/images/lol/web201310/skin/big10006.jpg"
6.  			 mode="aspectFill"></image>
7.  			<view class="desc">
8.  				<view class="text-content"> 折磨生出苦难，苦难又会加剧折磨，凡间这无穷的循环，将有我来终结！真正的恩典因不完整而美丽，因情感而真诚，因脆弱而自由！</view>
9.  				<view>
10.  					<view class="cu-tag bg-red light sm round">正义天使</view>
11.  					<view class="cu-tag bg-green light sm round">史诗</view>
12.  				</view>
13.  			</view>
14.  		</view>
15.  	</view>
16.  </view>


```

**卡片相关class** 

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>cu-card</td><td>卡片必选值</td><td>——</td></tr><tr><td>case</td><td>案例类卡片</td><td>——</td></tr><tr><td>dynamic</td><td>动态类卡片</td><td>——</td></tr><tr><td>article</td><td>文章类卡片</td><td>——</td></tr><tr><td>comment</td><td>评论</td><td>——</td></tr><tr><td>no-card</td><td>配合cu-card，去除子元素cu-item的margin和border</td><td>——</td></tr><tr><td>desc</td><td>纵向的flex布局</td><td>——</td></tr></tbody></table>

### Form表单

**input输入框**

> form表单是用form标签包裹，每一个子元素添加类名cu-form-group，行内自定义，title类名用于设置每行的文字说明，如果想统一宽度，像示例一样，需要自定义宽度

![表单1](https://img-blog.csdnimg.cn/20191120142812707.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <form>	
2.      <view class="cu-form-group margin-top">
3.  		<view class="title">邮件</view>
4.  		<input placeholder="两字短标题" ></input>
5.  	</view>
6.  	<view class="cu-form-group">
7.  		<view class="title">输入框</view>
8.  		<input placeholder="三字标题" ></input>
9.  	</view>
10.  	<view class="cu-form-group">
11.  		<view class="title">收货地址</view>
12.  		<input placeholder="统一标题的宽度" ></input>
13.  	</view>
14.  	<view class="cu-form-group">
15.  		<view class="title">收货地址</view>
16.  		<input placeholder="输入框带个图标" ></input>
17.  		<text class='cuIcon-locationfill text-orange'></text>
18.  	</view>
19.  	<view class="cu-form-group">
20.  		<view class="title">验证码</view>
21.  		<input placeholder="输入框带个按钮" ></input>
22.  		<button class='cu-btn bg-green shadow'>验证码</button>
23.  	</view>
24.  	<view class="cu-form-group">
25.  		<view class="title">手机号码</view>
26.  		<input placeholder="输入框带标签" ></input>
27.  		<view class="cu-capsule radius">
28.  			<view class='cu-tag bg-blue '>+86</view>
29.  			<view class="cu-tag line-blue">中国大陆</view>
30.  		</view>
31.  	</view>
32.  </form>

34.  <style>
35.  	.cu-form-group .title {
36.  		min-width: calc(4em + 15px);
37.  	}
38.  </style>


```

**picker选择器** 

>  对于表单中从底部谈起的选择器，写法与上述简单的input相同，而选择器picker的使用，建议查看[官方文档](https://uniapp.dcloud.io/component/picker)

![表单2](https://img-blog.csdnimg.cn/20191120143716691.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <form>
2.  	<view class="cu-form-group margin-top">
3.  		<view class="title">普通选择</view>
4.  		<picker @change="PickerChange" :value="index" :range="picker">
5.  			<view class="picker">
6.  				{{index>-1?picker[index]:'禁止换行，超出容器部分会以 ... 方式截断'}}
7.  			</view>
8.  		</picker>
9.  	</view>
10.  	<!-- #ifndef MP-ALIPAY -->
11.  	<view class="cu-form-group">
12.  		<view class="title">多列选择</view>
13.  		<picker mode="multiSelector" @change="MultiChange" @columnchange="MultiColumnChange" :value="multiIndex" :range="multiArray">
14.  			<view class="picker">
15.  				{{multiArray[0][multiIndex[0]]}}，{{multiArray[1][multiIndex[1]]}}，{{multiArray[2][multiIndex[2]]}}
16.  			</view>
17.  		</picker>
18.  	</view>
19.  	<!-- #endif -->
20.  	<view class="cu-form-group">
21.  		<view class="title">时间选择</view>
22.  		<picker mode="time" :value="time" start="09:01" end="21:01" @change="TimeChange">
23.  			<view class="picker">
24.  				{{time}}
25.  			</view>
26.  		</picker>
27.  	</view>
28.  	<view class="cu-form-group">
29.  		<view class="title">日期选择</view>
30.  		<picker mode="date" :value="date" start="2015-09-01" end="2020-09-01" @change="DateChange">
31.  			<view class="picker">
32.  				{{date}}
33.  			</view>
34.  		</picker>
35.  	</view>
36.  	<!-- #ifndef H5 || APP-PLUS || MP-ALIPAY -->
37.  	<view class="cu-form-group">
38.  		<view class="title">地址选择</view>
39.  		<picker mode="region" @change="RegionChange" :value="region">
40.  			<view class="picker">
41.  				{{region[0]}}，{{region[1]}}，{{region[2]}}
42.  			</view>
43.  		</picker>
44.  	</view>
45.  	<!-- #endif -->
46.  </form>


```

**switch开关** 

> 对于开关的样式，参考[switch官方文档](https://uniapp.dcloud.io/component/switch)，需要注意的阿里小程序的不同，我没有用阿里测试，我测的是微信小程序，在微信小程序上，switch的color属性不生效，想要更改开关颜色，可以用颜色类名，直接写颜色就可以，颜色的取值可以参考[上一章背景色](https://blog.csdn.net/miao_yf/article/details/102971767#%E8%83%8C%E6%99%AF%C2%A0)

![开关](https://img-blog.csdnimg.cn/20191120150640698.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <form>
2.  	<view class="cu-form-group margin-top">
3.  		<view class="title">开关选择</view>
4.  		<switch @change="SwitchA" :class="switchA?'checked':''" :checked="switchA?true:false"></switch>
5.  	</view>
6.  	<view class="cu-form-group">
7.  		<view class="title">定义颜色</view>
8.  		<!-- #ifdef MP-ALIPAY -->
9.  		<switch class='red' @change="SwitchB" :class="switchB?'checked':''" :checked="switchB?true:false" color="#e54d42"></switch>
10.  		<!-- #endif -->

12.  		<!-- #ifndef MP-ALIPAY -->
13.  		<switch class='red' @change="SwitchB" :class="switchB?'checked':''" :checked="switchB?true:false"></switch>
14.  		<!-- #endif -->
15.  	</view>
16.  	<view class="cu-form-group">
17.  		<view class="title">定义图标</view>
18.  		<switch class='switch-sex' @change="SwitchC" :class="switchC?'checked':''" :checked="switchC?true:false"></switch>
19.  	</view>
20.  	<!-- #ifndef MP-ALIPAY -->
21.  	<view class="cu-form-group">
22.  		<view class="title">方形开关</view>
23.  		<switch class='orange radius' @change="SwitchD" :class="switchD?'checked':''" :checked="switchD?true:false"></switch>
24.  	</view>
25.  	<!-- #endif -->
26.  </form>


```

**radio单选框** 

> [radio官方文档](https://uniapp.dcloud.io/component/radio)先奉上，更改颜色同上，使用提供的颜色类名即可，其默认样式是圆形全色中间有个√，可以通过类名radio更改形状为镂空中间一个点

![单选1](https://img-blog.csdnimg.cn/20191121100828615.png)![单选2](https://img-blog.csdnimg.cn/2019112110085444.png)

```


1.  <form>
2.  	<radio-group class="block" @change="RadioChange">
3.  		<view class="cu-form-group margin-top">
4.  			<view class="title">单选操作(radio)</view>
5.  			<radio :class="radio=='A'?'checked':''" :checked="radio=='A'?true:false" value="A"></radio>
6.  		</view>
7.  		<!-- #ifndef MP-ALIPAY -->
8.  		<view class="cu-form-group">
9.  			<view class="title">定义样式</view>
10.  			<radio class='radio' :class="radio=='B'?'checked':''" :checked="radio=='B'?true:false" value="B"></radio>
11.  		</view>
12.  		<view class="cu-form-group">
13.  			<view class="title">定义颜色</view>
14.  			<view>
15.  				<radio class='blue radio' :class="radio=='C'?'checked':''" :checked="radio=='C'?true:false" value="C"></radio>
16.  				<radio class='red margin-left-sm' :class="radio=='D'?'checked':''" :checked="radio=='D'?true:false" value="D"></radio>
17.  			</view>
18.  		</view>
19.  		<!-- #endif -->
20.  	</radio-group>
21.  </form>


```

**checkbox复选框** 

> [CheckBox官方文档](https://uniapp.dcloud.io/component/checkbox) ，更改颜色同上，使用提供的颜色类名即可，其默认样式是方形，可以通过类名round更改形状为圆形

![复选](https://img-blog.csdnimg.cn/20191121103540196.png)

```


1.  <form>
2.  	<checkbox-group class="block" @change="CheckboxChange">
3.  		<view class="cu-form-group margin-top">
4.  			<view class="title">复选选操作(checkbox)</view>
5.  			<checkbox :class="checkbox[0].checked?'checked':''" :checked="checkbox[0].checked?true:false" value="A"></checkbox>
6.  		</view>
7.  		<!-- #ifndef MP-ALIPAY -->
8.  		<view class="cu-form-group">
9.  			<view class="title">定义形状</view>
10.  			<checkbox class='round' :class="checkbox[1].checked?'checked':''" :checked="checkbox[1].checked?true:false" value="B"></checkbox>
11.  		</view>
12.  		<view class="cu-form-group">
13.  			<view class="title">定义颜色</view>
14.  			<checkbox class='round blue' :class="checkbox[2].checked?'checked':''" :checked="checkbox[2].checked?true:false"
15.  			 value="C"></checkbox>
16.  		</view>
17.  		<!-- #endif -->
18.  	</checkbox-group>
19.  </form>


```

 **图片上传**

>  [图片相关操作的API](https://uniapp.dcloud.io/api/media/image)，自己看一下哈，我没什么好说的

 ![图片1](https://img-blog.csdnimg.cn/20191121103632495.png)![图片2](https://img-blog.csdnimg.cn/20191121103642804.png)

```


1.  <form>
2.  	<view class="cu-form-group">
3.  		<view class="grid col-4 grid-square flex-sub">
4.  			<view class="bg-img" v-for="(item,index) in imgList" :key="index" @tap="ViewImage" :data-url="imgList[index]">
5.  			 <image :src="imgList[index]" mode="aspectFill"></image>
6.  				<view class="cu-tag bg-red" @tap.stop="DelImg" :data-index="index">
7.  					<text class='cuIcon-close'></text>
8.  				</view>
9.  			</view>
10.  			<view class="solids" @tap="ChooseImage" v-if="imgList.length<4">
11.  				<text class='cuIcon-cameraadd'></text>
12.  			</view>
13.  		</view>
14.  	</view>
15.  </form>

17.  <script>
18.  	export default {
19.  		data() {
20.  			return {
21.  				index: -1,
22.  				imgList: []
23.  			};
24.  		},
25.  		methods: {
26.  			ChooseImage() {
27.  				uni.chooseImage({
28.  					count: 4, //默认9
29.  					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
30.  					sourceType: ['album'], //从相册选择
31.  					success: (res) => {
32.  						if (this.imgList.length != 0) {
33.  							this.imgList = this.imgList.concat(res.tempFilePaths)
34.  						} else {
35.  							this.imgList = res.tempFilePaths
36.  						}
37.  					}
38.  				});
39.  			},
40.  			ViewImage(e) {
41.  				uni.previewImage({
42.  					urls: this.imgList,
43.  					current: e.currentTarget.dataset.url
44.  				});
45.  			},
46.  			DelImg(e) {
47.  				uni.showModal({
48.  					title: '召唤师',
49.  					content: '确定要删除这段回忆吗？',
50.  					cancelText: '再看看',
51.  					confirmText: '再见',
52.  					success: res => {
53.  						if (res.confirm) {
54.  							this.imgList.splice(e.currentTarget.dataset.index, 1)
55.  						}
56.  					}
57.  				})
58.  			}
59.  		}
60.  	}
61.  </script>


```

**头像**

> 这个样式可以用于用户修改个人资料中，修改头像 

![表单头像](https://img-blog.csdnimg.cn/20191121105630578.png)

```


1.  <form>
2.  	<view class="cu-form-group margin-top">
3.  		<view class="title">头像</view>
4.  		<view class="cu-avatar radius bg-gray"></view>
5.  	</view>
6.  </form>


```

**多行文本** 

> [官方官方](https://uniapp.dcloud.io/component/textarea)

 ![多行文本](https://img-blog.csdnimg.cn/2019112111034226.png)

```


1.  <form>
2.  	<!-- !!!!! placeholder 在ios表现有偏移 建议使用 第一种样式 -->
3.  	<view class="cu-form-group margin-top">
4.  		<textarea maxlength="-1" :disabled="modalName!=null" @input="textareaAInput" placeholder="多行文本输入框"></textarea>
5.  	</view>
6.  	<view class="cu-form-group align-start">
7.  		<view class="title">文本框</view>
8.  		<textarea maxlength="-1" :disabled="modalName!=null" @input="textareaBInput" placeholder="多行文本输入框"></textarea>
9.  	</view>
10.  </form>


```

**表单相关class**

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>cu-form-group</td><td>表单子元素</td><td>——</td></tr><tr><td>title</td><td>标题（自定义宽度）</td><td>——</td></tr></tbody></table>

### TimeLine时间轴

**默认**

> 时间轴默认结构如下代码，由cu-timeline包裹cu-time和cu-item，在item内编写内容，由content包裹

![时间轴1](https://img-blog.csdnimg.cn/20191121113455285.png)

```


1.  <view class="cu-timeline">
2.  	<view class="cu-time">06-17</view>
3.  	<view class="cu-item">
4.  		<view class="content">
5.  			<text>01:30</text> 【喵星】 MX-12138 已揽收，准备发往银河系
6.  		</view>
7.  	</view>
8.  </view>


```

**多彩时间轴** 

> 这个与默认的基本格式相同，只是自定义了内容框颜色和图标，结合基础元素的样式，你可以写个彩虹

![时间轴2](https://img-blog.csdnimg.cn/20191121114015420.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-timeline">
2.  	<view class="cu-time">昨天</view>
3.  	<view class="cu-item cur cuIcon-noticefill">
4.  		<view class="content bg-green shadow-blur">
5.  			<text>22:22</text> 【广州市】快件已到达地球
6.  		</view>
7.  	</view>
8.  	<view class="cu-item text-red cuIcon-attentionforbidfill">
9.  		<view class="content bg-red shadow-blur">
10.  			这是第一次，我家的铲屎官走了这么久。久到足足有三天！！
11.  		</view>
12.  	</view>
13.  	<view class="cu-item text-grey cuIcon-evaluate_fill">
14.  		<view class="content bg-grey shadow-blur">
15.  			这是第一次，我家的铲屎官走了这么久。
16.  		</view>
17.  	</view>
18.  	<view class="cu-item text-blue">
19.  		<view class="bg-blue content">
20.  			<text>20:00</text> 【月球】快件已到达月球，准备发往地球
21.  		</view>
22.  		<view class="bg-cyan content">
23.  			<text>10:00</text> 【银河系】快件已到达银河系，准备发往月球
24.  		</view>
25.  	</view>
26.  </view>


```

**自定义** 

> 上一个是简单的修改了颜色图标，这一个则是稍微复杂一点，结合之前的标签、列表等，丰富了内容

![时间轴3](https://img-blog.csdnimg.cn/20191121114320780.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-timeline">
2.  	<view class="cu-time">06-17</view>
3.  	<view class="cu-item">
4.  		<view class="content">
5.  			<view class="cu-capsule radius">
6.  				<view class="cu-tag bg-cyan">上午</view>
7.  				<view class="cu-tag line-cyan">10:00</view>
8.  			</view>
9.  			<view class="margin-top">这是第一次，我家的铲屎官走了这么久。久到足足有三天！！ 在听到他的脚步声响在楼梯间的那一刻，我简直想要破门而出，对着他狠狠地吼上10分钟，然后再看心情要不要他进门。</view>
10.  		</view>
11.  	</view>
12.  	<view class="cu-item text-blue">
13.  		<view class="bg-blue shadow-blur content">
14.  			<view class="cu-list menu-avatar radius">
15.  				<view class="cu-item">
16.  					<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big10006.jpg);"></view>
17.  					<view class="content">
18.  						<view class="text-grey">文晓港</view>
19.  						<view class="text-gray text-sm">
20.  							<text class="cuIcon-infofill text-red"></text> 消息未送达</view>
21.  					</view>
22.  					<view class="action">
23.  						<view class="text-grey text-xs">22:20</view>
24.  						<view class="cu-tag round bg-grey sm">5</view>
25.  					</view>
26.  				</view>
27.  				<view class="cu-item">
28.  					<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big10006.jpg);">
29.  						<view class="cu-tag badge">99+</view>
30.  					</view>
31.  					<view class="content">
32.  						<view class="text-grey">文晓港
33.  							<view class="cu-tag round orange sm">SVIP</view>
34.  						</view>
35.  						<view class="text-gray text-sm">
36.  							<text class="cuIcon-redpacket_fill text-red"></text> 收到红包</view>
37.  					</view>
38.  					<view class="action">
39.  						<view class="text-grey text-xs">22:20</view>
40.  						<text class="cuIcon-notice_forbid_fill text-gray"></text>
41.  					</view>
42.  				</view>
43.  			</view>
44.  		</view>
45.  	</view>
46.  </view>


```

**时间轴相关class** 

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>cu-timeline</td><td>时间轴必选值</td><td>——</td></tr><tr><td>cu-time</td><td>时间</td><td>——</td></tr><tr><td>cu-item</td><td>子元素</td><td>——</td></tr><tr><td>content</td><td>内容信息</td><td>——</td></tr></tbody></table>

### Chat聊天

> cu-chat聊天必选值

**基本样式**

> 聊天的外部大框架是cu-chat，cu-item包裹某人的聊天信息，self表示右侧自己的聊天样式，main包含聊天内容，date表示消息时间，cu-info表示提示词

![聊天1](https://img-blog.csdnimg.cn/20191121145435968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-chat">
2.  	<view class="cu-item self">
3.  		<view class="main">
4.  			<view class="content bg-green shadow">
5.  				<text>喵喵喵！喵喵喵！喵喵喵！喵喵！喵喵！！喵！喵喵喵！</text>
6.  			</view>
7.  		</view>
8.  		<view class="cu-avatar radius" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big107000.jpg);"></view>
9.  		<view class="date">2018年3月23日 13:23</view>
10.  	</view>
11.  	<view class="cu-info round">对方撤回一条消息!</view>
12.  	<view class="cu-item">
13.  		<view class="cu-avatar radius" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big143004.jpg);"></view>
14.  		<view class="main">
15.  			<view class="content shadow">
16.  				<text>喵喵喵！喵喵喵！</text>
17.  			</view>
18.  		</view>
19.  		<view class="date "> 13:23</view>
20.  	</view>
21.  	<view class="cu-info">
22.  		<text class="cuIcon-roundclosefill text-red "></text> 对方拒绝了你的消息
23.  	</view>
24.  	<view class="cu-info">
25.  		对方开启了好友验证，你还不是他(她)的好友。请先发送好友验证请求，对方验证通过后，才能聊天。
26.  		<text class="text-blue">发送好友验证</text>
27.  	</view>
28.  </view>


```

**其他聊天内容**

> 聊天内容除了语句，还有图片，语音消息，地理位置消息等 ，main包含的聊天内容不止是消息框里的，还有消息框旁边的提示图标文字等，消息框里的内容由content包裹

![聊天2](https://img-blog.csdnimg.cn/20191121145901549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-chat">
2.  	<view class="cu-item self">
3.  		<view class="main">
4.  			<image src="https://ossweb-img.qq.com/images/lol/web201310/skin/big10006.jpg" class="radius" mode="widthFix"></image>
5.  		</view>
6.  		<view class="cu-avatar radius" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big107000.jpg);"></view>
7.  		<view class="date"> 13:23</view>
8.  	</view>
9.  	<view class="cu-item self">
10.  		<view class="main">
11.  			<view class="action text-bold text-grey">
12.  				3"
13.  			</view>
14.  			<view class="content shadow">
15.  				<text class="cuIcon-sound text-xxl padding-right-xl"> </text>
16.  			</view>
17.  		</view>
18.  		<view class="cu-avatar radius" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big107000.jpg);"></view>
19.  		<view class="date">13:23</view>
20.  	</view>
21.  	<view class="cu-item self">
22.  		<view class="main">
23.  			<view class="action">
24.  				<text class="cuIcon-locationfill text-orange text-xxl"></text>
25.  			</view>
26.  			<view class="content shadow">
27.  				喵星球，喵喵市
28.  			</view>
29.  		</view>
30.  		<view class="cu-avatar radius" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big107000.jpg);"></view>
31.  		<view class="date">13:23</view>
32.  	</view>
33.  	<view class="cu-item">
34.  		<view class="cu-avatar radius" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big143004.jpg);"></view>
35.  		<view class="main">
36.  			<view class="content shadow">
37.  				@#$^&**
38.  			</view>
39.  			<view class="action text-grey">
40.  				<text class="cuIcon-warnfill text-red text-xxl"></text> <text class="text-sm margin-left-sm">翻译错误</text>
41.  			</view>
42.  		</view>
43.  		<view class="date">13:23</view>
44.  	</view>
45.  </view>


```

**发送框**

> 发送框就是之前的操作条，相关内容看最上面操作条的讲解

 ![聊天3](https://img-blog.csdnimg.cn/20191121150543194.png)

```


1.  <view class="cu-bar foot input" :style="[{bottom:InputBottom+'px'}]">
2.  	<view class="action">
3.  		<text class="cuIcon-sound text-grey"></text>
4.  	</view>
5.  	<input class="solid-bottom" :adjust-position="false" :focus="false" maxlength="300" cursor-spacing="10"
6.  	 @focus="InputFocus" @blur="InputBlur"></input>
7.  	<view class="action">
8.  		<text class="cuIcon-emojifill text-grey"></text>
9.  	</view>
10.  	<button class="cu-btn bg-green shadow">发送</button>
11.  </view>


```

**聊天相关class**

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>cu-chat</td><td>聊天必选值</td><td>——</td></tr><tr><td>cu-item</td><td>子元素</td><td>——</td></tr><tr><td>self</td><td>右侧本人的聊天样式</td><td>——</td></tr><tr><td>main</td><td>聊天内容</td><td>——</td></tr><tr><td>content</td><td>消息框内容</td><td>——</td></tr><tr><td>date</td><td>消息日期</td><td>——</td></tr><tr><td>cu-info</td><td>提示词</td><td>——</td></tr></tbody></table>

### Swiper轮播图

**全屏限高轮播**

> 这是最常见的轮播图，添加类名screen-swiper，宽度为全屏，高度自定义，请直接参考[swiper官方文档](https://uniapp.dcloud.io/component/swiper)，可以通过类名square-dot和round-dot定义小圆点样式

 ![全屏](https://img-blog.csdnimg.cn/20191121153548424.png)

```
1.  <template>
2.  	<view>
3.  		<swiper class="screen-swiper" :class="dotStyle?'square-dot':'round-dot'" :indicator-dots="true" :circular="true"
4.  		 :autoplay="true" interval="5000" duration="500">
5.  			<swiper-item v-for="(item,index) in swiperList" :key="index">
6.  				<image :src="item.url" mode="aspectFill" v-if="item.type=='image'"></image>
7.  				<video :src="item.url" autoplay loop muted :show-play-btn="false" :controls="false" objectFit="cover" v-if="item.type=='video'"></video>
8.  			</swiper-item>
9.  		</swiper>
10.  	</view>
11.  </template>

13.  <script>
14.  	export default {
15.  		data() {
16.  			return {
17.  				swiperList: [{
18.  					id: 0,
19.  					type: 'image',
20.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big84000.jpg'
21.  				}, {
22.  					id: 1,
23.  					type: 'image',
24.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big37006.jpg',
25.  				}, {
26.  					id: 2,
27.  					type: 'image',
28.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big39000.jpg'
29.  				}, {
30.  					id: 3,
31.  					type: 'image',
32.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big10001.jpg'
33.  				}],
34.  				dotStyle: true
35.  			};
36.  		}
37.  	}
38.  </script> 
```

**卡片式轮播** 

> 在swiper标签上添加类名card-swiper，在swiper-item标签内的子元素上添加类名swiper-item，示例中cardSwiper函数的主要作用是切换类名cur，cur的作用是实现轮播图片中间大，两边小的效果，就是transform的scale()

 ![卡片式](https://img-blog.csdnimg.cn/20191121153617987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <swiper class="card-swiper" :class="dotStyle?'square-dot':'round-dot'" :indicator-dots="true" :circular="true"
2.   :autoplay="true" interval="5000" duration="500" @change="cardSwiper" indicator-color="#8799a3"
3.   indicator-active-color="#0081ff">
4.  	<swiper-item v-for="(item,index) in swiperList" :key="index" :class="cardCur==index?'cur':''">
5.  		<view class="swiper-item">
6.  			<image :src="item.url" mode="aspectFill" v-if="item.type=='image'"></image>
7.  			<video :src="item.url" autoplay loop muted :show-play-btn="false" :controls="false" objectFit="cover" v-if="item.type=='video'"></video>
8.  		</view>
9.  	</swiper-item>
10.  </swiper>

12.  cardSwiper(e) {
13.      this.cardCur = e.detail.current
14.  },


```

**堆叠式轮播**

> 堆叠轮播是原生写的，注意类名tower-swiper、tower-item以及swiper-item的配合使用，这主要是通过层级的高低来显示图片的，使用时需要配合js和css。（这种轮播图初始化展示效果如图二，需要触动一下才会展示如图一的效果，可以通过在onload()函数中给变量direction赋值解决）

![堆叠式](https://img-blog.csdnimg.cn/20191121153638109.png) ![bug](https://img-blog.csdnimg.cn/20191121164628849.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <template>
2.  	<view>
3.  		<view class="tower-swiper" @touchmove="TowerMove" @touchstart="TowerStart" @touchend="TowerEnd">
4.  			<view class="tower-item" :class="item.zIndex==1?'none':''" v-for="(item,index) in swiperList" :key="index" :style="[{'--index': item.zIndex,'--left':item.mLeft}]" :data-direction="direction">
5.  				<view class="swiper-item">
6.  					<image :src="item.url" mode="aspectFill" v-if="item.type=='image'"></image>
7.  					<video :src="item.url" autoplay loop muted :show-play-btn="false" :controls="false" objectFit="cover" v-if="item.type=='video'"></video>
8.  				</view>
9.  			</view>
10.  		</view>
11.  	</view>
12.  </template>

14.  <script>
15.  	export default {
16.  		data() {
17.  			return {
18.  				swiperList: [{
19.  					id: 0,
20.  					type: 'image',
21.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big84000.jpg'
22.  				}, {
23.  					id: 1,
24.  					type: 'image',
25.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big37006.jpg',
26.  				}, {
27.  					id: 2,
28.  					type: 'image',
29.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big39000.jpg'
30.  				}, {
31.  					id: 3,
32.  					type: 'image',
33.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big10001.jpg'
34.  				}, {
35.  					id: 4,
36.  					type: 'image',
37.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big25011.jpg'
38.  				}, {
39.  					id: 5,
40.  					type: 'image',
41.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big21016.jpg'
42.  				}, {
43.  					id: 6,
44.  					type: 'image',
45.  					url: 'https://ossweb-img.qq.com/images/lol/web201310/skin/big99008.jpg'
46.  				}],
47.  				towerStart: 0,
48.  				direction: ''
49.  			};
50.  		},
51.  		onLoad() {
52.  			this.TowerSwiper('swiperList');
53.                          this.direction = 'left';
54.  			// 初始化towerSwiper 传已有的数组名即可
55.  		}, 
56.  		methods: {
57.  			// 初始化towerSwiper
58.  			TowerSwiper(name) {
59.  				let list = this[name];
60.  				console.log(list)
61.  				for (let i = 0; i < list.length; i++) {
62.  					list[i].zIndex = parseInt(list.length / 2) + 1 - Math.abs(i - parseInt(list.length / 2))
63.  					list[i].mLeft = i - parseInt(list.length / 2)
64.  				}
65.  				this.swiperList = list
66.  			},
67.  			// towerSwiper触摸开始
68.  			TowerStart(e) {
69.  				console.log(e)
70.  				this.towerStart = e.touches[0].pageX
71.  			},
72.  			// towerSwiper计算方向
73.  			TowerMove(e) {
74.  				this.direction = e.touches[0].pageX - this.towerStart > 0 ? 'right' : 'left'
75.  			},
76.  			// towerSwiper计算滚动
77.  			TowerEnd(e) {
78.  				let direction = this.direction;
79.  				let list = this.swiperList;
80.  				if (direction == 'right') {
81.  					let mLeft = list[0].mLeft;
82.  					console.log(list[0])
83.  					let zIndex = list[0].zIndex;
84.  					for (let i = 1; i < this.swiperList.length; i++) {
85.  						this.swiperList[i - 1].mLeft = this.swiperList[i].mLeft
86.  						this.swiperList[i - 1].zIndex = this.swiperList[i].zIndex
87.  					}
88.  					this.swiperList[list.length - 1].mLeft = mLeft;
89.  					this.swiperList[list.length - 1].zIndex = zIndex;
90.  				} else {
91.  					let mLeft = list[list.length - 1].mLeft;
92.  					let zIndex = list[list.length - 1].zIndex;
93.  					for (let i = this.swiperList.length - 1; i > 0; i--) {
94.  						this.swiperList[i].mLeft = this.swiperList[i - 1].mLeft
95.  						this.swiperList[i].zIndex = this.swiperList[i - 1].zIndex
96.  					}
97.  					this.swiperList[0].mLeft = mLeft;
98.  					this.swiperList[0].zIndex = zIndex;
99.  				}
100.  				this.direction = ""
101.  				this.swiperList = this.swiperList
102.  			},
103.  		}
104.  	}
105.  </script>

107.  <style>
108.  	.tower-swiper .tower-item {
109.  		transform: scale(calc(0.5 + var(--index) / 10));
110.  		margin-left: calc(var(--left) * 100upx - 150upx);
111.  		z-index: var(--index);
112.  	}
113.  </style>


```

**轮播图相关class** 

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>screen-swiper</td><td>全屏限高轮播</td><td>——</td></tr><tr><td>square-dot</td><td>方形指示点</td><td>——</td></tr><tr><td>round-dot</td><td>圆形指示点</td><td>——</td></tr><tr><td>card-swiper</td><td>卡片式轮播</td><td>——</td></tr><tr><td>swiper-item</td><td>滑动切换区域（具体看示例）</td><td>——</td></tr><tr><td>tower-swiper</td><td>堆叠式轮播</td><td>——</td></tr><tr><td>tower-item</td><td>堆叠式轮播子元素</td><td>——</td></tr></tbody></table>

### Modal模态框

> cu-modal和cu-dialog为模态框必选值，所有模态框的大体框架都是cu-modal包裹cu-dialog，cu-dialog内部填充操作部分和信息展示部分，操作部分可以用cu-bar操作条来布局，信息展示就直接写。弹框的显示隐藏是通过添加或移除类名show来实现的，示例中隐藏弹框是绑定tap调用hideModal事件，hideModal函数内执行的语句就是将变量modalName赋值为null，从而移除类名show，你想让用户点击哪里隐藏弹框，就可以把hideModal事件绑定在哪里（比如设置点击遮罩层隐藏弹框，就把hideModal事件绑定在有类名cu-modal的标签上）

**普通窗口**

> 普通窗口就是cu-modal包裹cu-dialog，只做信息的展示，没有交互效果 

 ![普通窗口](https://img-blog.csdnimg.cn/20191122103159939.png)![普通窗口1](https://img-blog.csdnimg.cn/2019112210322147.png)

```


1.  <view class="cu-modal" :class="modalName=='Modal'?'show':''">
2.  	<view class="cu-dialog">
3.  		<view class="cu-bar bg-white justify-end">
4.  			<view class="content">Modal标题</view>
5.  			<view class="action" @tap="hideModal">
6.  				<text class="cuIcon-close text-red"></text>
7.  			</view> 
8.  		</view>
9.  		<view class="padding-xl">
10.  			Modal 内容。
11.  		</view>
12.  	</view>
13.  </view>


```

**底部窗口**

> cu-modal结合bottom-modal实现底部弹窗 

 ![底部窗口](https://img-blog.csdnimg.cn/2019112210324933.png)![底部窗口](https://img-blog.csdnimg.cn/20191122103322308.png)

```


1.  <view class="cu-modal bottom-modal" :class="modalName=='bottomModal'?'show':''">
2.  	<view class="cu-dialog">
3.  		<view class="cu-bar bg-white">
4.  			<view class="action text-green">确定</view>
5.  			<view class="action text-blue" @tap="hideModal">取消</view>
6.  		</view>
7.  		<view class="padding-xl">
8.  			Modal 内容。
9.  		</view>
10.  	</view>
11.  </view>


```

**对话窗口**

> 对话窗口相较于普通窗口底部多了一些用户交互的操作 

 ![对话窗口](https://img-blog.csdnimg.cn/20191122103350116.png)

![对话窗口1](https://img-blog.csdnimg.cn/20191122103404842.png)      ![对话窗口2](https://img-blog.csdnimg.cn/20191122103430284.png)

```


1.  //示例一
2.  <view class="cu-modal" :class="modalName=='DialogModal1'?'show':''">
3.  	<view class="cu-dialog">
4.  		<view class="cu-bar bg-white justify-end">
5.  			<view class="content">Modal标题</view>
6.  			<view class="action" @tap="hideModal">
7.  				<text class="cuIcon-close text-red"></text>
8.  			</view>
9.  		</view>
10.  		<view class="padding-xl">
11.  			Modal 内容。
12.  		</view>
13.  		<view class="cu-bar bg-white justify-end">
14.  			<view class="action">
15.  				<button class="cu-btn line-green text-green" @tap="hideModal">取消</button>
16.  				<button class="cu-btn bg-green margin-left" @tap="hideModal">确定</button>
17.  			</view>
18.  		</view>
19.  	</view>
20.  </view>
21.  //示例二
22.  <view class="cu-modal" :class="modalName=='DialogModal2'?'show':''">
23.  	<view class="cu-dialog">
24.  		<view class="cu-bar bg-white justify-end">
25.  			<view class="content">Modal标题</view>
26.  			<view class="action" @tap="hideModal">
27.  				<text class="cuIcon-close text-red"></text>
28.  			</view>
29.  		</view>
30.  		<view class="padding-xl">
31.  			Modal 内容。
32.  		</view>
33.  		<view class="cu-bar bg-white">
34.  			<view class="action margin-0 flex-sub text-green " @tap="hideModal">
35.  				<text class="cuIcon-moneybag"></text>微信支付</view>
36.  			<view class="action margin-0 flex-sub text-green solid-left" @tap="hideModal">取消</view>
37.  			<view class="action margin-0 flex-sub  solid-left" @tap="hideModal">确定</view>
38.  		</view>
39.  	</view>
40.  </view>


```

**图片窗口**

> 图片窗口用于图片的展示，也可自定义操作 

 ![图片窗口](https://img-blog.csdnimg.cn/2019112210345862.png)![图片窗口1](https://img-blog.csdnimg.cn/20191122103513659.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-modal" :class="modalName=='Image'?'show':''">
2.  	<view class="cu-dialog">
3.  		<view class="bg-img" style="background-image: url('https://ossweb-img.qq.com/images/lol/web201310/skin/big91012.jpg');height:200px;">
4.  			<view class="cu-bar justify-end text-white">
5.  				<view class="action" @tap="hideModal">
6.  					<text class="cuIcon-close "></text>
7.  				</view>
8.  			</view>
9.  		</view>
10.  		<view class="cu-bar bg-white">
11.  			<view class="action margin-0 flex-sub  solid-left" @tap="hideModal">我知道了</view>
12.  		</view>
13.  	</view>
14.  </view>


```

**单选窗口**

> 单选窗口是普通窗口内部结合radio标签编写的 

 ![单选窗口](https://img-blog.csdnimg.cn/20191122103539689.png)![单选窗口1](https://img-blog.csdnimg.cn/2019112210355610.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <view class="cu-modal" :class="modalName=='RadioModal'?'show':''" @tap="hideModal">
2.  	<view class="cu-dialog" @tap.stop="">
3.  		<radio-group class="block" @change="RadioChange">
4.  			<view class="cu-list menu text-left">
5.  				<view class="cu-item" v-for="(item,index) in 5" :key="index">
6.  					<label class="flex justify-between align-center flex-sub">
7.  						<view class="flex-sub">Item {{index +1}}</view>
8.  						<radio class="round" :class="radio=='radio' + index?'checked':''" :checked="radio=='radio' + index?true:false"
9.  						 :value="'radio' + index"></radio>
10.  					</label>
11.  				</view>
12.  			</view>
13.  		</radio-group>
14.  	</view>
15.  </view>


```

**多选窗口**

> 多选窗口的内容是配合grid布局自定义的

 ![多选窗口](https://img-blog.csdnimg.cn/20191122103623885.png)![多选窗口1](https://img-blog.csdnimg.cn/20191122103640441.png)

```


1.  <view class="cu-modal bottom-modal" :class="modalName=='ChooseModal'?'show':''" @tap="hideModal">
2.  	<view class="cu-dialog" @tap.stop="">
3.  		<view class="cu-bar bg-white">
4.  			<view class="action text-blue" @tap="hideModal">取消</view>
5.  			<view class="action text-green" @tap="hideModal">确定</view>
6.  		</view>
7.  		<view class="grid col-3 padding-sm">
8.  			<view v-for="(item,index) in checkbox" class="padding-xs" :key="index">
9.  				<button class="cu-btn orange lg block" :class="item.checked?'bg-orange':'line-orange'" @tap="ChooseCheckbox"
10.  				 :data-value="item.value"> {{item.name}}
11.  					<view class="cu-tag sm round" :class="item.checked?'bg-white text-orange':'bg-orange'" v-if="item.hot">HOT</view>
12.  				</button>
13.  			</view>
14.  		</view>
15.  	</view>
16.  </view>
17.  <script>
18.  	export default {
19.  		data() {
20.  			return {
21.  				modalName: null,
22.  				checkbox: [{value: 0,name: '10元',checked: false,hot: false,}, 
23.                             {value: 1,name: '20元',checked: true,hot: false,},
24.                             {value: 2,name: '30元',checked: true,hot: true,}, 
25.                             {value: 3,name: '60元',checked: false,hot: true,},
26.                             {value: 4,name: '80元',checked: false,hot: false,}, 
27.                             {value: 5,name: '100元',checked: false,hot: false,}
28.                            ]
29.  			};
30.  		},
31.  		methods: {
32.  			showModal(e) {
33.  				this.modalName = e.currentTarget.dataset.target
34.  			},
35.  			hideModal(e) {
36.  				this.modalName = null
37.  			},
38.  			ChooseCheckbox(e) {
39.  				let items = this.checkbox;
40.  				let values = e.currentTarget.dataset.value;
41.  				for (let i = 0, lenI = items.length; i < lenI; ++i) {
42.  					if (items[i].value == values) {
43.  						items[i].checked = !items[i].checked;
44.  						break
45.  					}
46.  				}
47.  			}
48.  		}
49.  	}
50.  </script>


```

**侧边抽屉**

> cu-modal结合drawer-modal，由justify-start和justify-end决定抽屉方向 

![侧边抽屉](https://img-blog.csdnimg.cn/20191122103706277.png)

![侧边抽屉1](https://img-blog.csdnimg.cn/20191122103730687.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)             ![侧边抽屉2](https://img-blog.csdnimg.cn/20191122103748980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  //左侧抽屉
2.  <view class="cu-modal drawer-modal justify-start" :class="modalName=='DrawerModalL'?'show':''" @tap="hideModal">
3.  	<view class="cu-dialog basis-lg" @tap.stop="">
4.  		<view class="cu-list menu text-left">
5.  			<view class="cu-item arrow" v-for="(item,index) in 5" :key="index">
6.  				<view class="content">
7.  					<view>Item {{index +1}}</view>
8.  				</view>
9.  			</view>
10.  		</view>
11.  	</view>
12.  </view>
13.  //右侧抽屉
14.  <view class="cu-modal drawer-modal justify-end" :class="modalName=='DrawerModalR'?'show':''" @tap="hideModal">
15.  	<view class="cu-dialog basis-lg" @tap.stop="" >
16.  		<view class="cu-list menu text-left">
17.  			<view class="cu-item arrow" v-for="(item,index) in 5" :key="index">
18.  				<view class="content">
19.  					<view>Item {{index +1}}</view>
20.  				</view>
21.  			</view>
22.  		</view>
23.  	</view>
24.  </view>


```

**模态框相关class**

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>cu-modal</td><td>模态框必选值</td><td>——</td></tr><tr><td>cu-dialog</td><td>模态框子元素</td><td>——</td></tr><tr><td>bottom-modal</td><td>底部弹框</td><td>——</td></tr><tr><td>drawer-modal</td><td>侧边弹框</td><td>——</td></tr><tr><td>show</td><td>显示弹框</td><td>——</td></tr></tbody></table>

### Steps步骤条

> cu-steps步骤条必选值 

**默认**

>  cu-steps和cu-item配合使用

 ![步骤条1](https://img-blog.csdnimg.cn/2019112213204285.png)

```


1.  <view class="bg-white padding">
2.  	<view class="cu-steps">
3.  		<view class="cu-item" :class="index>basics?'':'text-red'" v-for="(item,index) in basicsList" :key="index">
4.  			<text :class="'cuIcon-' + item.cuIcon"></text> {{item.name}}
5.  		</view>
6.  	</view>
7.  </view>

9.  <script>
10.  	export default {
11.  		data() {
12.  			return {
13.  				basicsList: [{cuIcon: 'usefullfill',name: '开始'}, 
14.                                      {cuIcon: 'radioboxfill',name: '等待'}, 
15.                                      {cuIcon: 'roundclosefill',name: '错误'},
16.                                      {cuIcon: 'roundcheckfill',name: '完成'
17.  				}],
18.  				basics: 0
19.  			};
20.  		},
21.  		methods: {
22.  			BasicsSteps() {
23.  				this.basics= this.basics == this.basicsList.length - 1 ? 0 : this.basics + 1				
24.  			}
25.  		}
26.  	}
27.  </script>


```

>  步骤条的颜色和图标都可以自定义

![步骤条2](https://img-blog.csdnimg.cn/20191122132100746.png)

```


1.  <view class="bg-white padding margin-top-xs">
2.  	<view class="cu-steps">
3.  		<view class="cu-item" :class="index>basics?'':'text-orange'" v-for="(item,index) in basicsList" :key="index">
4.  			<text :class="index>basics?'cuIcon-title':'cuIcon-' + item.cuIcon"></text> {{item.name}}
5.  		</view>
6.  	</view>
7.  </view>


```

> 步骤之间的连接默认是横线，也可以通过类名steps-arrow换成箭头

![步骤条3](https://img-blog.csdnimg.cn/20191122132118650.png)

```


1.  <view class="bg-white padding  margin-top-xs">
2.  	<view class="cu-steps steps-arrow">
3.  		<view class="cu-item" :class="index>basics?'':'text-blue'" v-for="(item,index) in basicsList" :key="index">
4.  			<text :class="'cuIcon-' + item.cuIcon"></text> {{item.name}}
5.  		</view>
6.  	</view>
7.  </view>


```

**数字完成**

> 通过类名num可以设置默认图标为数字，已完成且正确的图标为勾，已完成但错误的图标由类名err定义

 ![步骤条4](https://img-blog.csdnimg.cn/20191122133829775.png)

```


1.  <view class="bg-white padding">
2.  	<view class="cu-steps">
3.  		<view class="cu-item" :class="index>num?'':'text-blue'" v-for="(item,index) in numList" :key="index">
4.  			<text class="num" :class="index==2?'err':''" :data-index="index + 1"></text> {{item.name}}
5.  		</view>
6.  	</view>
7.  </view>


```

**多级显示**

> 多级显示需要配合[scroll-view标签](https://uniapp.dcloud.io/component/scroll-view)使用 ，并增加类名steps-bottom

  ![步骤条5](https://img-blog.csdnimg.cn/20191122132147779.png)![步骤条6](https://img-blog.csdnimg.cn/20191122132209860.png)

```


1.  <scroll-view scroll-x class="bg-white padding response cu-steps steps-bottom" :scroll-into-view="'scroll-' + scroll"
2.   scroll-with-animation>
3.  	<view class="cu-item padding-lr-xl" :class="index>scroll?'':'text-blue'" v-for="(item,index) in 10" :key="index" :id="'scroll-' + index">
4.  		Level {{index + 1}} <text class="num" :data-index="index + 1"></text>
5.  	</view>
6.  </scroll-view>

8.  <script>
9.  	export default {
10.  		data() {
11.  			return {
12.  				scroll: 0
13.  			};
14.  		},
15.  		methods: {
16.  			ScrollSteps() {
17.  				this.scroll= this.scroll == 9 ? 0 : this.scroll + 1				
18.  			}
19.  		}
20.  	}
21.  </script>


```

**步骤条相关class** 

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>cu-steps</td><td>步骤条必选值</td><td>——</td></tr><tr><td>cu-item</td><td>步骤条子元素</td><td>——</td></tr><tr><td>num</td><td>数字步骤条未完成图标</td><td>——</td></tr><tr><td>err</td><td>错误图标</td><td>——</td></tr><tr><td>steps-arrow</td><td>步骤条连接箭头</td><td>——</td></tr><tr><td>steps-bottom</td><td>配合多级步骤条使用（图标在下，文字在上，放在其他步骤条样式会错乱）</td><td>——</td></tr></tbody></table>

扩展插件 
-----

交互组件就到此为止了，不足之处请多多指教，便于我及时更正，承接上一篇基础元素的博客，扩展插件部分我们就在再另写一篇，[ColorUI组件库简易教程之扩展插件](https://blog.csdn.net/miao_yf/article/details/103199773)
