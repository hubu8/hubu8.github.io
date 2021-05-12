# ColorUI扩展组件


<!--more-->

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/miao_yf/article/details/103199773?spm=1001.2014.3001.5501)

**按照惯例，在此奉上[官方示例](https://github.com/weilanwl/ColorUI)！！！**

**目录**

[扩展插件](#t0) 

[索引列表](#t1)

[微动画](#t2)

[全屏抽屉](#t3) 

[垂直导航](#t4)

* * *

扩展插件 
-----

### 索引列表

> 索引列表主要由[scroll-view标签](https://uniapp.dcloud.io/component/scroll-view)实现，类名为indexes，具体js逻辑请看源码，有些我没看懂，希望看懂的大佬教教我

![索引](https://img-blog.csdnimg.cn/2019112215285896.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70) 

```


1.  <template>
2.  	<view>
3.  		<view class="cu-bar bg-white search fixed">
4.  			<view class="search-form round">
5.  				<text class="cuIcon-search"></text>
6.  				<input type="text" placeholder="输入搜索的关键词" confirm-type="search"></input>
7.  			</view>
8.  			<view class="action">
9.  				<button class="cu-btn bg-gradual-green shadow-blur round">搜索</button>
10.  			</view>
11.  		</view>
12.  		<scroll-view scroll-y class="indexes" :scroll-into-view="'indexes-'+ listCurID" :style="[{height:'calc(100vh - 50px)'}]"
13.  		 :scroll-with-animation="true" :enable-back-to-top="true">
14.  			<block v-for="(item,index) in list" :key="index">
15.  <!-- 这个'indexItem-' + item.name类名，整个colorUI，我也没找到它的作用，删了也没发现什么影响-->
16.  				<view :class="'indexItem-' + item.name" :id="'indexes-' + item.name" :data-index="item.name">
17.  					<view class="padding">{{item.name}}</view>
18.  					<view class="cu-list menu-avatar no-padding">
19.  						<view class="cu-item" v-for="(items,sub) in 2" :key="sub">
20.  							<view class="cu-avatar round lg">{{item.name}}</view>
21.  							<view class="content">
22.  								<view class="text-grey">{{item.name}}<text class="text-abc">{{list[sub].name}}</text>君</view>
23.  								<view class="text-gray text-sm">
24.  									有{{sub+2}}个主子需要伺候
25.  								</view>
26.  							</view>
27.  						</view>
28.  					</view>
29.  				</view>
30.  			</block>
31.  		</scroll-view>
32.  		<view class="indexBar" :style="[{height:'calc(100vh - 50px)'}]">
33.  			<view class="indexBar-box" @touchstart="tStart" @touchend="tEnd" @touchmove.stop="tMove">
34.  				<view class="indexBar-item" v-for="(item,index) in list" :key="index" :id="index" @touchstart="getCur" @touchend="setCur">
35.  					{{item.name}}</view>
36.  			</view>
37.  		</view>
38.  		<!--选择显示-->
39.  		<view v-show="!hidden" class="indexToast">
40.  			{{listCur}}
41.  		</view>
42.  	</view>
43.  </template>

45.  <script>
46.  	export default {
47.  		data() {
48.  			return {
49.  				StatusBar: this.StatusBar,
50.  				CustomBar: this.CustomBar,
51.  				hidden: true,
52.  				listCurID: '',
53.  				list: [],
54.  				listCur: '',
55.  			};
56.  		},
57.  		onLoad() {
58.  			let list = [{}];
59.  			for (let i = 0; i < 26; i++) {
60.  				list[i] = {};
61.  				list[i].name = String.fromCharCode(65 + i);
62.  			}
63.  			this.list = list;
64.  			this.listCur = list[0];
65.  		},
66.  		onReady() {
67.  			let that = this;
68.  			//这两句大概知道什么意思，但是emmm,这个boxTop和barTop具体是什么数值，不是很理解
69.  			uni.createSelectorQuery().select('.indexBar-box').boundingClientRect(function(res) {
70.  				that.boxTop = res.top
71.  			}).exec();
72.  			uni.createSelectorQuery().select('.indexes').boundingClientRect(function(res) {
73.  				that.barTop = res.top
74.  			}).exec()
75.  		},
76.  		methods: {
77.  			//获取文字信息
78.  			getCur(e) {
79.  				this.hidden = false;
80.  				this.listCur = this.list[e.target.id].name;
81.  			},
82.  			setCur(e) {
83.  				this.hidden = true;
84.  				this.listCur = this.listCur
85.  			},
86.  			//滑动选择Item
87.  			tMove(e) {
88.  				console.log(this.boxTop)
89.  				let y = e.touches[0].clientY,
90.  					offsettop = this.boxTop,
91.  					that = this;
92.  				//判断选择区域,只有在选择区才会生效
93.  				if (y > offsettop) {
94.  // 这个num计算结果怎么就是字母列表的下标呢，我没想明白，望大佬指教
95.  					let num = parseInt((y - offsettop) / 20);
96.  					this.listCur = that.list[num].name
97.  				};
98.  			},

100.  			//触发全部开始选择
101.  			tStart() {
102.  				this.hidden = false
103.  			},

105.  			//触发结束选择
106.  			tEnd() {
107.  				this.hidden = true;
108.  				this.listCurID = this.listCur
109.  			},
110.  //源码中有这个函数，但是这个页面中根本没有用到，删了没影响，我也不明白作者为什么写这个
111.  			indexSelect(e) {
112.  				let that = this;
113.  				let barHeight = this.barHeight;
114.  				let list = this.list;
115.  				let scrollY = Math.ceil(list.length * e.detail.y / barHeight);
116.  				for (let i = 0; i < list.length; i++) {
117.  					if (scrollY < i + 1) {
118.  						that.listCur = list[i].name;
119.  						that.movableY = i * 20
120.  						return false
121.  					}
122.  				}
123.  			} 
124.  		}
125.  	}
126.  </script>

128.  <style>
129.  	page {
130.  		padding-top: 100upx;
131.  	}

133.  	.indexes {
134.  		position: relative;
135.  	}

137.  	.indexBar {
138.  		position: fixed;
139.  		right: 0px;
140.  		bottom: 0px;
141.  		padding: 20upx 20upx 20upx 60upx;
142.  		display: flex;
143.  		align-items: center;
144.  	}

146.  	.indexBar .indexBar-box {
147.  		width: 40upx;
148.  		height: auto;
149.  		background: #fff;
150.  		display: flex;
151.  		flex-direction: column;
152.  		box-shadow: 0 0 20upx rgba(0, 0, 0, 0.1);
153.  		border-radius: 10upx;
154.  	}

156.  	.indexBar-item {
157.  		flex: 1;
158.  		width: 40upx;
159.  		height: 40upx;
160.  		display: flex;
161.  		align-items: center;
162.  		justify-content: center;
163.  		font-size: 24upx;
164.  		color: #888;
165.  	}

167.  	movable-view.indexBar-item {
168.  		width: 40upx;
169.  		height: 40upx;
170.  		z-index: 9;
171.  		position: relative;
172.  	}

174.  	movable-view.indexBar-item::before {
175.  		content: "";
176.  		display: block;
177.  		position: absolute;
178.  		left: 0;
179.  		top: 10upx;
180.  		height: 20upx;
181.  		width: 4upx;
182.  		background-color: #f37b1d;
183.  	}

185.  	.indexToast {
186.  		position: fixed;
187.  		top: 0;
188.  		right: 80upx;
189.  		bottom: 0;
190.  		background: rgba(0, 0, 0, 0.5);
191.  		width: 100upx;
192.  		height: 100upx;
193.  		border-radius: 10upx;
194.  		margin: auto;
195.  		color: #fff;
196.  		line-height: 100upx;
197.  		text-align: center;
198.  		font-size: 48upx;
199.  	}
200.  </style>


```

### 微动画

> 微动画我没办法截图展示出来这种动态效果，我就直接给出class了，想要看效果，可以去官方示例去看，这里有几个gif动画，我可以给个图和代码，看一下

![gif](https://img-blog.csdnimg.cn/20191122160230238.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70) 

```


1.  <view class="margin radius bg-gradual-green shadow-blur">
2.  <!-- 这个动图显示不出来，不知道是不是路径问题，还是源文件没了 -->
3.  	<image src="https://image.weilanwl.com/gif/wave.gif" mode="scaleToFill" class="gif-black response" style="height:100upx"></image>
4.  </view>
5.  <view class="margin flex">
6.  	<view class="bg-black flex-sub margin-right radius shadow-lg">
7.  		<image src="https://image.weilanwl.com/gif/loading-black.gif" mode="aspectFit" class="gif-black response" style="height:240upx"></image>
8.  	</view>
9.  	<view class="bg-white flex-sub radius shadow-lg">
10.  		<image src="https://image.weilanwl.com/gif/loading-white.gif" mode="aspectFit" class="gif-white response" style="height:240upx"></image>
11.  	</view>
12.  </view>
13.  <view class="margin flex">
14.  	<view class="bg-gradual-blue flex-sub margin-right radius shadow-lg">
15.  		<image src="https://image.weilanwl.com/gif/rhomb-black.gif" mode="aspectFit" class="gif-black response" style="height:240upx"></image>
16.  	</view>
17.  	<view class="bg-white flex-sub radius shadow-lg">
18.  		<image src="https://image.weilanwl.com/gif/rhomb-white.gif" mode="aspectFit" class="gif-white response" style="height:240upx"></image>
19.  	</view>
20.  </view>
21.  <view class="margin flex">
22.  	<view class="bg-white flex-sub margin-right radius shadow-lg">
23.  		<image src="https://image.weilanwl.com/gif/loading-1.gif" mode="aspectFit" class="gif-white response" style="height:240upx"></image>
24.  	</view>
25.  	<view class="bg-black flex-sub radius shadow-lg">
26.  		<image src="https://image.weilanwl.com/gif/loading-2.gif" mode="aspectFit" class="gif-black response" style="height:240upx"></image>
27.  	</view>
28.  </view>


```

**微动画相关class**

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>animation-{{options}}</td><td>动画效果</td><td>fade / scale-up / scale-down / slide-top / slide-bottom / slide-left / slide-right / shake</td></tr><tr><td>animation-reverse</td><td>反向动画，配合上面的动画效果使用</td><td>&nbsp;</td></tr></tbody></table>

### 全屏抽屉 

> 全屏抽屉效果主要分为三个部分，主要有三个类名，第一个类名DrawerPage，就是第一张图总体外部包裹的类名；第二个类名DrawerClose，用来包裹打开抽屉之后关闭部分，如图二红框框出来的部分；第三个类名DrawerWindow，就是打开的抽屉部分了

 ![抽屉1](https://img-blog.csdnimg.cn/20191122161902426.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)                  ![抽屉2](https://img-blog.csdnimg.cn/20191122161914953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <template>
2.  	<view class="bg-gradual-blue">
3.  //全屏主体页面
4.  		<scroll-view scroll-y class="DrawerPage" :class="modalName=='viewModal'?'show':''">
5.  			<cu-custom bgColor="bg-gradual-blue" :isBack="true"><block slot="backText">返回</block>
6.  				<block slot="content">全屏抽屉</block>
7.  			</cu-custom>			
8.  			<view class='padding margin text-center'>
9.  				<view class='cu-btn bg-green lg block shadow radius margin-xl' @tap="showModal" data-target="viewModal">
10.  					打开抽屉
11.  				</view>
12.  			</view>
13.  			<view class="cu-list menu card-menu margin-top-xl margin-bottom-xl shadow-lg">
14.  				<view class="cu-item arrow" v-for="(item,index) in 20" :key="index">
15.  					<view class="content">
16.  						<text class="cuIcon-github text-grey"></text>
17.  						<text class="text-grey">{{index +1}}</text>
18.  					</view>
19.  				</view>
20.  			</view>
21.  			<view class='padding margin text-center'>
22.  				<view class='cu-btn bg-green lg block shadow radius margin-xl' @tap="showModal" data-target="viewModal">
23.  					打开抽屉
24.  				</view>
25.  			</view>
26.  		</scroll-view>
27.  //关闭部分		
28.  		<view class="DrawerClose" :class="modalName=='viewModal'?'show':''" @tap="hideModal">
29.  			<text class="cuIcon-pullright"></text>
30.  		</view>
31.  //抽屉部分		
32.  		<scroll-view scroll-y class="DrawerWindow" :class="modalName=='viewModal'?'show':''">
33.  			<view class="cu-list menu card-menu margin-top-xl margin-bottom-xl shadow-lg">
34.  				<view class="cu-item arrow" v-for="(item,index) in 20" :key="index">
35.  					<view class="content">
36.  						<text class="cuIcon-github text-grey"></text>
37.  						<text class="text-grey">{{index +1}}</text>
38.  					</view>
39.  				</view>
40.  			</view>
41.  		</scroll-view>
42.  	</view>
43.  </template>

45.  <script>
46.  	export default {
47.  		data() {
48.  			return {
49.  				modalName:null
50.  			};
51.  		},
52.  		methods: {
53.  			showModal(e) {
54.  				console.log(e)
55.  				this.modalName = e.currentTarget.dataset.target
56.  			},
57.  			hideModal(e) {
58.  				this.modalName = null
59.  			},
60.  //这个函数没用到，我也不知道为什么源码作者写了
61.  			tabSelect(e) {
62.  				this.TabCur = e.currentTarget.dataset.id;
63.  				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
64.  			}
65.  		},
66.  	}
67.  </script>

69.  <style>
70.  	page {
71.  		background-image: var(--gradualBlue);
72.  		width: 100vw;
73.  		overflow: hidden;
74.  	}

76.  	.DrawerPage {
77.  		position: fixed;
78.  		width: 100vw;
79.  		height: 100vh;
80.  		left: 0vw;
81.  		background-color: #f1f1f1;
82.  		transition: all 0.4s;
83.  	}

85.  	.DrawerPage.show {
86.  		transform: scale(0.9, 0.9);
87.  		left: 85vw;
88.  		box-shadow: 0 0 60upx rgba(0, 0, 0, 0.2);
89.  		transform-origin: 0;
90.  	}

92.  	.DrawerWindow {
93.  		position: absolute;
94.  		width: 85vw;
95.  		height: 100vh;
96.  		left: 0;
97.  		top: 0;
98.  		transform: scale(0.9, 0.9) translateX(-100%);
99.  		opacity: 0;
100.  		pointer-events: none;
101.  		transition: all 0.4s;
102.  		padding: 100upx 0;
103.  	}

105.  	.DrawerWindow.show {
106.  		transform: scale(1, 1) translateX(0%);
107.  		opacity: 1;
108.  		pointer-events: all;
109.  	}

111.  	.DrawerClose {
112.  		position: absolute;
113.  		width: 40vw;
114.  		height: 100vh;
115.  		right: 0;
116.  		top: 0;
117.  		color: transparent;
118.  		padding-bottom: 30upx;
119.  		display: flex;
120.  		align-items: flex-end;
121.  		justify-content: center;
122.  		background-image: linear-gradient(90deg, rgba(0, 0, 0, 0.01), rgba(0, 0, 0, 0.6));
123.  		letter-spacing: 5px;
124.  		font-size: 50upx;
125.  		opacity: 0;
126.  		pointer-events: none;
127.  		transition: all 0.4s;
128.  	}

130.  	.DrawerClose.show {
131.  		opacity: 1;
132.  		pointer-events: all;
133.  		width: 15vw;
134.  		color: #fff;
135.  	}

137.  	.DrawerPage .cu-bar.tabbar .action button.cuIcon {
138.  		width: 64upx;
139.  		height: 64upx;
140.  		line-height: 64upx;
141.  		margin: 0;
142.  		display: inline-block;
143.  	}

145.  	.DrawerPage .cu-bar.tabbar .action .cu-avatar {
146.  		margin: 0;
147.  	}

149.  	.DrawerPage .nav {
150.  		flex: 1;
151.  	}

153.  	.DrawerPage .nav .cu-item.cur {
154.  		border-bottom: 0;
155.  		position: relative;
156.  	}

158.  	.DrawerPage .nav .cu-item.cur::after {
159.  		content: "";
160.  		width: 10upx;
161.  		height: 10upx;
162.  		background-color: currentColor;
163.  		position: absolute;
164.  		bottom: 10upx;
165.  		border-radius: 10upx;
166.  		left: 0;
167.  		right: 0;
168.  		margin: auto;
169.  	}

171.  	.DrawerPage .cu-bar.tabbar .action {
172.  		flex: initial;
173.  	}
174.  </style>


```

### 垂直导航

> 垂直导航主要有两个[scroll-view标签](https://uniapp.dcloud.io/component/scroll-view)实现，分别是左侧的导航栏部分和右侧的内容部分，左侧导航栏部分使用类名VerticalBox，右侧内容部分使用类名VerticalMain 

![垂直导航](https://img-blog.csdnimg.cn/2019112216375184.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```


1.  <template>
2.  	<view>
3.  		<view class="fixed">
4.  			<cu-custom :isBack="true" bgColor="bg-shadeTop text-white">
5.  				<block slot="backText">返回</block>
6.  				<block slot="content">垂直导航</block>
7.  			</cu-custom>
8.  		</view>
9.  		<swiper class="screen-swiper round-dot" :indicator-dots="true" :circular="true" :autoplay="true" interval="5000"
10.  		 duration="500">
11.  			<swiper-item v-for="(item,index) in 4" :key="index">
12.  				<image :src="'https://ossweb-img.qq.com/images/lol/web201310/skin/big3900'+index+ '.jpg'" mode="aspectFill"></image>
13.  			</swiper-item>
14.  		</swiper>
15.  		<view class="VerticalBox">
16.  			<scroll-view class="VerticalNav nav" scroll-y scroll-with-animation :scroll-top="verticalNavTop" style="height:calc(100vh - 375upx)">
17.  				<view class="cu-item" :class="index==tabCur?'text-green cur':''" v-for="(item,index) in list" :key="index" @tap="TabSelect"
18.  				 :data-id="index">
19.  					Tab-{{item.name}}
20.  				</view>
21.  			</scroll-view>
22.  			<scroll-view class="VerticalMain" scroll-y scroll-with-animation style="height:calc(100vh - 375upx)"
23.  			 :scroll-into-view="'main-'+mainCur" @scroll="VerticalMain">
24.  				<view class="padding-top padding-lr" v-for="(item,index) in list" :key="index" :id="'main-'+index">
25.  					<view class="cu-bar solid-bottom bg-white">
26.  						<view class="action">
27.  							<text class="cuIcon-title text-green"></text> Tab-{{item.name}}</view>
28.  					</view>
29.  					<view class="cu-list menu-avatar">
30.  						<view class="cu-item">
31.  							<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big10001.jpg);"></view>
32.  							<view class="content">
33.  								<view class="text-grey">凯尔</view>
34.  								<view class="text-gray text-sm flex">
35.  									<text class="text-cut">
36.  										<text class="cuIcon-infofill text-red  margin-right-xs"></text>
37.  										我已天理为凭，踏入这片荒芜，不再受凡人的枷锁遏制。我已天理为凭，踏入这片荒芜，不再受凡人的枷锁遏制。
38.  									</text> </view>
39.  							</view>
40.  							<view class="action">
41.  								<view class="text-grey text-xs">22:20</view>
42.  								<view class="cu-tag round bg-grey sm">5</view>
43.  							</view>
44.  						</view>
45.  						<view class="cu-item">
46.  							<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/img/champion/Taric.png);">
47.  								<view class="cu-tag badge">99+</view>
48.  							</view>
49.  							<view class="content">
50.  								<view class="text-grey">
51.  									<text class="text-cut">瓦洛兰之盾-塔里克</text>
52.  									<view class="cu-tag round bg-orange sm">战士</view>
53.  								</view>
54.  								<view class="text-gray text-sm flex">
55.  									<text class="text-cut">
56.  										塔里克是保护者星灵，用超乎寻常的力量守护着符文之地的生命、仁爱以及万物之美。塔里克由于渎职而被放逐，离开了祖国德玛西亚，前去攀登巨神峰寻找救赎，但他找到的却是来自星界的更高层的召唤。现在的塔里克与古代巨神族的神力相融合，以瓦洛兰之盾的身份，永不疲倦地警惕着阴险狡诈的虚空腐化之力。
57.  									</text>
58.  								</view>
59.  							</view>
60.  							<view class="action">
61.  								<view class="text-grey text-xs">22:20</view>
62.  								<view class="cuIcon-notice_forbid_fill text-gray"></view>
63.  							</view>
64.  						</view>
65.  						<view class="cu-item ">
66.  							<view class="cu-avatar radius lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/img/champion/Morgana.png);"></view>
67.  							<view class="content">
68.  								<view class="text-pink"><text class="text-cut">莫甘娜</text></view>
69.  								<view class="text-gray text-sm flex"> <text class="text-cut">凯尔，你被自己的光芒变的盲目！</text></view>
70.  							</view>
71.  							<view class="action">
72.  								<view class="text-grey text-xs">22:20</view>
73.  								<view class="cu-tag round bg-red sm">5</view>
74.  							</view>
75.  						</view>
76.  						<view class="cu-item grayscale">
77.  							<view class="cu-avatar radius lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big81007.jpg);"></view>
78.  							<view class="content">
79.  								<view><text class="text-cut">伊泽瑞尔</text>
80.  									<view class="cu-tag round bg-orange sm">断开连接...</view>
81.  								</view>
82.  								<view class="text-gray text-sm flex"> <text class="text-cut"> 等我回来一个打十个</text></view>
83.  							</view>
84.  							<view class="action">
85.  								<view class="text-grey text-xs">22:20</view>
86.  								<view class="cu-tag round bg-red sm">5</view>
87.  							</view>
88.  						</view>
89.  						<view class="cu-item cur">
90.  							<view class="cu-avatar radius lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big81020.jpg);">
91.  								<view class="cu-tag badge"></view>
92.  							</view>
93.  							<view class="content">
94.  								<view>
95.  									<text class="text-cut">瓦罗兰大陆-睡衣守护者-新手保护营</text>
96.  									<view class="cu-tag round bg-orange sm">6人</view>
97.  								</view>
98.  								<view class="text-gray text-sm flex">
99.  									<text class="text-cut"> 伊泽瑞尔：<text class="cuIcon-locationfill text-orange margin-right-xs"></text> 传送中...</text></view>
100.  							</view>
101.  							<view class="action">
102.  								<view class="text-grey text-xs">22:20</view>
103.  								<view class="cuIcon-notice_forbid_fill text-gray"></view>
104.  							</view>
105.  						</view>
106.  					</view>
107.  				</view>
108.  			</scroll-view>
109.  		</view>
110.  	</view>
111.  </template>

113.  <script>
114.  	export default {
115.  		data() {
116.  			return {
117.  				list: [],
118.  				tabCur: 0,
119.  				mainCur: 0,
120.  				verticalNavTop: 0,
121.  				load: true
122.  			};
123.  		},
124.  		onLoad() {
125.  			uni.showLoading({
126.  				title: '加载中...',
127.  				mask: true
128.  			});
129.  			let list = [{}];
130.  			for (let i = 0; i < 26; i++) {
131.  				list[i] = {};
132.  				list[i].name = String.fromCharCode(65 + i);
133.  				list[i].id = i;
134.  			}
135.  			this.list = list;
136.  			this.listCur = list[0];
137.  		},
138.  		onReady() {
139.  			uni.hideLoading()
140.  		},
141.  		methods: {
142.  			TabSelect(e) {
143.  				this.tabCur = e.currentTarget.dataset.id;
144.  				this.mainCur = e.currentTarget.dataset.id;
145.  				this.verticalNavTop = (e.currentTarget.dataset.id - 1) * 50
146.  			},
147.  //希望有大佬给我讲解一下这个双向联动函数
148.  			VerticalMain(e) {
149.  				// #ifdef MP-ALIPAY
150.  				   return false  //支付宝小程序暂时不支持双向联动 
151.  				// #endif
152.  				let that = this;
153.  				let tabHeight = 0;
154.  				if (this.load) {
155.  					for (let i = 0; i < this.list.length; i++) {
156.  						let view = uni.createSelectorQuery().select("#main-" + this.list[i].id);
157.  						view.fields({
158.  							size: true
159.  						}, data => {
160.  							this.list[i].top = tabHeight;
161.  							tabHeight = tabHeight + data.height;
162.  							this.list[i].bottom = tabHeight;
163.  						}).exec();
164.  					}
165.  					this.load = false
166.  				}
167.  				let scrollTop = e.detail.scrollTop + 10;
168.  				for (let i = 0; i < this.list.length; i++) {
169.  					if (scrollTop > this.list[i].top && scrollTop < this.list[i].bottom) {
170.  						this.verticalNavTop = (this.list[i].id - 1) * 50
171.  						this.tabCur = this.list[i].id
172.  						console.log(scrollTop)
173.  						return false
174.  					}
175.  				}
176.  			}
177.  		},
178.  	}
179.  </script>

181.  <style>
182.  	.fixed {
183.  		position: fixed;
184.  		z-index: 99;
185.  	}

187.  	.VerticalNav.nav {
188.  		width: 200upx;
189.  		white-space: initial;
190.  	}

192.  	.VerticalNav.nav .cu-item {
193.  		width: 100%;
194.  		text-align: center;
195.  		background-color: #fff;
196.  		margin: 0;
197.  		border: none;
198.  		height: 50px;
199.  		position: relative;
200.  	}

202.  	.VerticalNav.nav .cu-item.cur {
203.  		background-color: #f1f1f1;
204.  	}

206.  	.VerticalNav.nav .cu-item.cur::after {
207.  		content: "";
208.  		width: 8upx;
209.  		height: 30upx;
210.  		border-radius: 10upx 0 0 10upx;
211.  		position: absolute;
212.  		background-color: currentColor;
213.  		top: 0;
214.  		right: 0upx;
215.  		bottom: 0;
216.  		margin: auto;
217.  	}

219.  	.VerticalBox {
220.  		display: flex;
221.  	}

223.  	.VerticalMain {
224.  		background-color: #f1f1f1;
225.  		flex: 1;
226.  	}
227.  </style>


```

colorUI的简易教程差不多就到这了，欢迎各位大佬的指教！！！
