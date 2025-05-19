# ColorUI扩展组件


<!--more-->

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/miao_yf/article/details/103199773?spm=1001.2014.3001.5501)

**按照惯例，在此奉上[官方示例](https://github.com/weilanwl/ColorUI)！！！**

**目录**

[TOC]



* * *

扩展插件 
-----

### 索引列表

> 索引列表主要由[scroll-view标签](https://uniapp.dcloud.io/component/scroll-view)实现，类名为indexes，具体js逻辑请看源码，有些我没看懂，希望看懂的大佬教教我

![索引](https://img-blog.csdnimg.cn/2019112215285896.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70) 

```
<template>
	<view>
		<view class="cu-bar bg-white search fixed">
			<view class="search-form round">
				<text class="cuIcon-search"></text>
				<input type="text" placeholder="输入搜索的关键词" confirm-type="search"></input>
			</view>
			<view class="action">
				<button class="cu-btn bg-gradual-green shadow-blur round">搜索</button>
			</view>
		</view>
		<scroll-view scroll-y class="indexes" :scroll-into-view="'indexes-'+ listCurID" :style="[{height:'calc(100vh - 50px)'}]"
		 :scroll-with-animation="true" :enable-back-to-top="true">
			<block v-for="(item,index) in list" :key="index">
<!-- 这个'indexItem-' + item.name类名，整个colorUI，我也没找到它的作用，删了也没发现什么影响-->
				<view :class="'indexItem-' + item.name" :id="'indexes-' + item.name" :data-index="item.name">
					<view class="padding">{{item.name}}</view>
					<view class="cu-list menu-avatar no-padding">
						<view class="cu-item" v-for="(items,sub) in 2" :key="sub">
							<view class="cu-avatar round lg">{{item.name}}</view>
							<view class="content">
								<view class="text-grey">{{item.name}}<text class="text-abc">{{list[sub].name}}</text>君</view>
								<view class="text-gray text-sm">
									有{{sub+2}}个主子需要伺候
								</view>
							</view>
						</view>
					</view>
				</view>
			</block>
		</scroll-view>
		<view class="indexBar" :style="[{height:'calc(100vh - 50px)'}]">
			<view class="indexBar-box" @touchstart="tStart" @touchend="tEnd" @touchmove.stop="tMove">
				<view class="indexBar-item" v-for="(item,index) in list" :key="index" :id="index" @touchstart="getCur" @touchend="setCur">
					{{item.name}}</view>
			</view>
		</view>
		<!--选择显示-->
		<view v-show="!hidden" class="indexToast">
			{{listCur}}
		</view>
	</view>
</template>
 
<script>
	export default {
		data() {
			return {
				StatusBar: this.StatusBar,
				CustomBar: this.CustomBar,
				hidden: true,
				listCurID: '',
				list: [],
				listCur: '',
			};
		},
		onLoad() {
			let list = [{}];
			for (let i = 0; i < 26; i++) {
				list[i] = {};
				list[i].name = String.fromCharCode(65 + i);
			}
			this.list = list;
			this.listCur = list[0];
		},
		onReady() {
			let that = this;
			//这两句大概知道什么意思，但是emmm,这个boxTop和barTop具体是什么数值，不是很理解
			uni.createSelectorQuery().select('.indexBar-box').boundingClientRect(function(res) {
				that.boxTop = res.top
			}).exec();
			uni.createSelectorQuery().select('.indexes').boundingClientRect(function(res) {
				that.barTop = res.top
			}).exec()
		},
		methods: {
			//获取文字信息
			getCur(e) {
				this.hidden = false;
				this.listCur = this.list[e.target.id].name;
			},
			setCur(e) {
				this.hidden = true;
				this.listCur = this.listCur
			},
			//滑动选择Item
			tMove(e) {
				console.log(this.boxTop)
				let y = e.touches[0].clientY,
					offsettop = this.boxTop,
					that = this;
				//判断选择区域,只有在选择区才会生效
				if (y > offsettop) {
// 这个num计算结果怎么就是字母列表的下标呢，我没想明白，望大佬指教
					let num = parseInt((y - offsettop) / 20);
					this.listCur = that.list[num].name
				};
			},
 
			//触发全部开始选择
			tStart() {
				this.hidden = false
			},
 
			//触发结束选择
			tEnd() {
				this.hidden = true;
				this.listCurID = this.listCur
			},
//源码中有这个函数，但是这个页面中根本没有用到，删了没影响，我也不明白作者为什么写这个
			indexSelect(e) {
				let that = this;
				let barHeight = this.barHeight;
				let list = this.list;
				let scrollY = Math.ceil(list.length * e.detail.y / barHeight);
				for (let i = 0; i < list.length; i++) {
					if (scrollY < i + 1) {
						that.listCur = list[i].name;
						that.movableY = i * 20
						return false
					}
				}
			} 
		}
	}
</script>
 
<style>
	page {
		padding-top: 100upx;
	}
 
	.indexes {
		position: relative;
	}
 
	.indexBar {
		position: fixed;
		right: 0px;
		bottom: 0px;
		padding: 20upx 20upx 20upx 60upx;
		display: flex;
		align-items: center;
	}
 
	.indexBar .indexBar-box {
		width: 40upx;
		height: auto;
		background: #fff;
		display: flex;
		flex-direction: column;
		box-shadow: 0 0 20upx rgba(0, 0, 0, 0.1);
		border-radius: 10upx;
	}
 
	.indexBar-item {
		flex: 1;
		width: 40upx;
		height: 40upx;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 24upx;
		color: #888;
	}
 
	movable-view.indexBar-item {
		width: 40upx;
		height: 40upx;
		z-index: 9;
		position: relative;
	}
 
	movable-view.indexBar-item::before {
		content: "";
		display: block;
		position: absolute;
		left: 0;
		top: 10upx;
		height: 20upx;
		width: 4upx;
		background-color: #f37b1d;
	}
 
	.indexToast {
		position: fixed;
		top: 0;
		right: 80upx;
		bottom: 0;
		background: rgba(0, 0, 0, 0.5);
		width: 100upx;
		height: 100upx;
		border-radius: 10upx;
		margin: auto;
		color: #fff;
		line-height: 100upx;
		text-align: center;
		font-size: 48upx;
	}
</style>
```

### 微动画

> 微动画我没办法截图展示出来这种动态效果，我就直接给出class了，想要看效果，可以去官方示例去看，这里有几个gif动画，我可以给个图和代码，看一下

![gif](https://img-blog.csdnimg.cn/20191122160230238.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70) 

```
<view class="margin radius bg-gradual-green shadow-blur">
<!-- 这个动图显示不出来，不知道是不是路径问题，还是源文件没了 -->
	<image src="https://image.weilanwl.com/gif/wave.gif" mode="scaleToFill" class="gif-black response" style="height:100upx"></image>
</view>
<view class="margin flex">
	<view class="bg-black flex-sub margin-right radius shadow-lg">
		<image src="https://image.weilanwl.com/gif/loading-black.gif" mode="aspectFit" class="gif-black response" style="height:240upx"></image>
	</view>
	<view class="bg-white flex-sub radius shadow-lg">
		<image src="https://image.weilanwl.com/gif/loading-white.gif" mode="aspectFit" class="gif-white response" style="height:240upx"></image>
	</view>
</view>
<view class="margin flex">
	<view class="bg-gradual-blue flex-sub margin-right radius shadow-lg">
		<image src="https://image.weilanwl.com/gif/rhomb-black.gif" mode="aspectFit" class="gif-black response" style="height:240upx"></image>
	</view>
	<view class="bg-white flex-sub radius shadow-lg">
		<image src="https://image.weilanwl.com/gif/rhomb-white.gif" mode="aspectFit" class="gif-white response" style="height:240upx"></image>
	</view>
</view>
<view class="margin flex">
	<view class="bg-white flex-sub margin-right radius shadow-lg">
		<image src="https://image.weilanwl.com/gif/loading-1.gif" mode="aspectFit" class="gif-white response" style="height:240upx"></image>
	</view>
	<view class="bg-black flex-sub radius shadow-lg">
		<image src="https://image.weilanwl.com/gif/loading-2.gif" mode="aspectFit" class="gif-black response" style="height:240upx"></image>
	</view>
</view>
```

**微动画相关class**

<table border="1" cellpadding="1" cellspacing="1"><thead><tr><th>class</th><th>说明</th><th>可选值</th></tr></thead><tbody><tr><td>animation-{{options}}</td><td>动画效果</td><td>fade / scale-up / scale-down / slide-top / slide-bottom / slide-left / slide-right / shake</td></tr><tr><td>animation-reverse</td><td>反向动画，配合上面的动画效果使用</td><td>&nbsp;</td></tr></tbody></table>

### 全屏抽屉 

> 全屏抽屉效果主要分为三个部分，主要有三个类名，第一个类名DrawerPage，就是第一张图总体外部包裹的类名；第二个类名DrawerClose，用来包裹打开抽屉之后关闭部分，如图二红框框出来的部分；第三个类名DrawerWindow，就是打开的抽屉部分了

 ![抽屉1](https://img-blog.csdnimg.cn/20191122161902426.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)                  ![抽屉2](https://img-blog.csdnimg.cn/20191122161914953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```
<template>
	<view class="bg-gradual-blue">
//全屏主体页面
		<scroll-view scroll-y class="DrawerPage" :class="modalName=='viewModal'?'show':''">
			<cu-custom bgColor="bg-gradual-blue" :isBack="true"><block slot="backText">返回</block>
				<block slot="content">全屏抽屉</block>
			</cu-custom>			
			<view class='padding margin text-center'>
				<view class='cu-btn bg-green lg block shadow radius margin-xl' @tap="showModal" data-target="viewModal">
					打开抽屉
				</view>
			</view>
			<view class="cu-list menu card-menu margin-top-xl margin-bottom-xl shadow-lg">
				<view class="cu-item arrow" v-for="(item,index) in 20" :key="index">
					<view class="content">
						<text class="cuIcon-github text-grey"></text>
						<text class="text-grey">{{index +1}}</text>
					</view>
				</view>
			</view>
			<view class='padding margin text-center'>
				<view class='cu-btn bg-green lg block shadow radius margin-xl' @tap="showModal" data-target="viewModal">
					打开抽屉
				</view>
			</view>
		</scroll-view>
//关闭部分		
		<view class="DrawerClose" :class="modalName=='viewModal'?'show':''" @tap="hideModal">
			<text class="cuIcon-pullright"></text>
		</view>
//抽屉部分		
		<scroll-view scroll-y class="DrawerWindow" :class="modalName=='viewModal'?'show':''">
			<view class="cu-list menu card-menu margin-top-xl margin-bottom-xl shadow-lg">
				<view class="cu-item arrow" v-for="(item,index) in 20" :key="index">
					<view class="content">
						<text class="cuIcon-github text-grey"></text>
						<text class="text-grey">{{index +1}}</text>
					</view>
				</view>
			</view>
		</scroll-view>
	</view>
</template>
 
<script>
	export default {
		data() {
			return {
				modalName:null
			};
		},
		methods: {
			showModal(e) {
				console.log(e)
				this.modalName = e.currentTarget.dataset.target
			},
			hideModal(e) {
				this.modalName = null
			},
//这个函数没用到，我也不知道为什么源码作者写了
			tabSelect(e) {
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
			}
		},
	}
</script>
 
<style>
	page {
		background-image: var(--gradualBlue);
		width: 100vw;
		overflow: hidden;
	}
 
	.DrawerPage {
		position: fixed;
		width: 100vw;
		height: 100vh;
		left: 0vw;
		background-color: #f1f1f1;
		transition: all 0.4s;
	}
 
	.DrawerPage.show {
		transform: scale(0.9, 0.9);
		left: 85vw;
		box-shadow: 0 0 60upx rgba(0, 0, 0, 0.2);
		transform-origin: 0;
	}
 
	.DrawerWindow {
		position: absolute;
		width: 85vw;
		height: 100vh;
		left: 0;
		top: 0;
		transform: scale(0.9, 0.9) translateX(-100%);
		opacity: 0;
		pointer-events: none;
		transition: all 0.4s;
		padding: 100upx 0;
	}
 
	.DrawerWindow.show {
		transform: scale(1, 1) translateX(0%);
		opacity: 1;
		pointer-events: all;
	}
 
	.DrawerClose {
		position: absolute;
		width: 40vw;
		height: 100vh;
		right: 0;
		top: 0;
		color: transparent;
		padding-bottom: 30upx;
		display: flex;
		align-items: flex-end;
		justify-content: center;
		background-image: linear-gradient(90deg, rgba(0, 0, 0, 0.01), rgba(0, 0, 0, 0.6));
		letter-spacing: 5px;
		font-size: 50upx;
		opacity: 0;
		pointer-events: none;
		transition: all 0.4s;
	}
 
	.DrawerClose.show {
		opacity: 1;
		pointer-events: all;
		width: 15vw;
		color: #fff;
	}
 
	.DrawerPage .cu-bar.tabbar .action button.cuIcon {
		width: 64upx;
		height: 64upx;
		line-height: 64upx;
		margin: 0;
		display: inline-block;
	}
 
	.DrawerPage .cu-bar.tabbar .action .cu-avatar {
		margin: 0;
	}
 
	.DrawerPage .nav {
		flex: 1;
	}
 
	.DrawerPage .nav .cu-item.cur {
		border-bottom: 0;
		position: relative;
	}
 
	.DrawerPage .nav .cu-item.cur::after {
		content: "";
		width: 10upx;
		height: 10upx;
		background-color: currentColor;
		position: absolute;
		bottom: 10upx;
		border-radius: 10upx;
		left: 0;
		right: 0;
		margin: auto;
	}
 
	.DrawerPage .cu-bar.tabbar .action {
		flex: initial;
	}
</style>
```

### 垂直导航

> 垂直导航主要有两个[scroll-view标签](https://uniapp.dcloud.io/component/scroll-view)实现，分别是左侧的导航栏部分和右侧的内容部分，左侧导航栏部分使用类名VerticalBox，右侧内容部分使用类名VerticalMain 

![垂直导航](https://img-blog.csdnimg.cn/2019112216375184.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pYW9feWY=,size_16,color_FFFFFF,t_70)

```
<template>
	<view>
		<view class="fixed">
			<cu-custom :isBack="true" bgColor="bg-shadeTop text-white">
				<block slot="backText">返回</block>
				<block slot="content">垂直导航</block>
			</cu-custom>
		</view>
		<swiper class="screen-swiper round-dot" :indicator-dots="true" :circular="true" :autoplay="true" interval="5000"
		 duration="500">
			<swiper-item v-for="(item,index) in 4" :key="index">
				<image :src="'https://ossweb-img.qq.com/images/lol/web201310/skin/big3900'+index+ '.jpg'" mode="aspectFill"></image>
			</swiper-item>
		</swiper>
		<view class="VerticalBox">
			<scroll-view class="VerticalNav nav" scroll-y scroll-with-animation :scroll-top="verticalNavTop" style="height:calc(100vh - 375upx)">
				<view class="cu-item" :class="index==tabCur?'text-green cur':''" v-for="(item,index) in list" :key="index" @tap="TabSelect"
				 :data-id="index">
					Tab-{{item.name}}
				</view>
			</scroll-view>
			<scroll-view class="VerticalMain" scroll-y scroll-with-animation style="height:calc(100vh - 375upx)"
			 :scroll-into-view="'main-'+mainCur" @scroll="VerticalMain">
				<view class="padding-top padding-lr" v-for="(item,index) in list" :key="index" :id="'main-'+index">
					<view class="cu-bar solid-bottom bg-white">
						<view class="action">
							<text class="cuIcon-title text-green"></text> Tab-{{item.name}}</view>
					</view>
					<view class="cu-list menu-avatar">
						<view class="cu-item">
							<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big10001.jpg);"></view>
							<view class="content">
								<view class="text-grey">凯尔</view>
								<view class="text-gray text-sm flex">
									<text class="text-cut">
										<text class="cuIcon-infofill text-red  margin-right-xs"></text>
										我已天理为凭，踏入这片荒芜，不再受凡人的枷锁遏制。我已天理为凭，踏入这片荒芜，不再受凡人的枷锁遏制。
									</text> </view>
							</view>
							<view class="action">
								<view class="text-grey text-xs">22:20</view>
								<view class="cu-tag round bg-grey sm">5</view>
							</view>
						</view>
						<view class="cu-item">
							<view class="cu-avatar round lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/img/champion/Taric.png);">
								<view class="cu-tag badge">99+</view>
							</view>
							<view class="content">
								<view class="text-grey">
									<text class="text-cut">瓦洛兰之盾-塔里克</text>
									<view class="cu-tag round bg-orange sm">战士</view>
								</view>
								<view class="text-gray text-sm flex">
									<text class="text-cut">
										塔里克是保护者星灵，用超乎寻常的力量守护着符文之地的生命、仁爱以及万物之美。塔里克由于渎职而被放逐，离开了祖国德玛西亚，前去攀登巨神峰寻找救赎，但他找到的却是来自星界的更高层的召唤。现在的塔里克与古代巨神族的神力相融合，以瓦洛兰之盾的身份，永不疲倦地警惕着阴险狡诈的虚空腐化之力。
									</text>
								</view>
							</view>
							<view class="action">
								<view class="text-grey text-xs">22:20</view>
								<view class="cuIcon-notice_forbid_fill text-gray"></view>
							</view>
						</view>
						<view class="cu-item ">
							<view class="cu-avatar radius lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/img/champion/Morgana.png);"></view>
							<view class="content">
								<view class="text-pink"><text class="text-cut">莫甘娜</text></view>
								<view class="text-gray text-sm flex"> <text class="text-cut">凯尔，你被自己的光芒变的盲目！</text></view>
							</view>
							<view class="action">
								<view class="text-grey text-xs">22:20</view>
								<view class="cu-tag round bg-red sm">5</view>
							</view>
						</view>
						<view class="cu-item grayscale">
							<view class="cu-avatar radius lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big81007.jpg);"></view>
							<view class="content">
								<view><text class="text-cut">伊泽瑞尔</text>
									<view class="cu-tag round bg-orange sm">断开连接...</view>
								</view>
								<view class="text-gray text-sm flex"> <text class="text-cut"> 等我回来一个打十个</text></view>
							</view>
							<view class="action">
								<view class="text-grey text-xs">22:20</view>
								<view class="cu-tag round bg-red sm">5</view>
							</view>
						</view>
						<view class="cu-item cur">
							<view class="cu-avatar radius lg" style="background-image:url(https://ossweb-img.qq.com/images/lol/web201310/skin/big81020.jpg);">
								<view class="cu-tag badge"></view>
							</view>
							<view class="content">
								<view>
									<text class="text-cut">瓦罗兰大陆-睡衣守护者-新手保护营</text>
									<view class="cu-tag round bg-orange sm">6人</view>
								</view>
								<view class="text-gray text-sm flex">
									<text class="text-cut"> 伊泽瑞尔：<text class="cuIcon-locationfill text-orange margin-right-xs"></text> 传送中...</text></view>
							</view>
							<view class="action">
								<view class="text-grey text-xs">22:20</view>
								<view class="cuIcon-notice_forbid_fill text-gray"></view>
							</view>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>
 
<script>
	export default {
		data() {
			return {
				list: [],
				tabCur: 0,
				mainCur: 0,
				verticalNavTop: 0,
				load: true
			};
		},
		onLoad() {
			uni.showLoading({
				title: '加载中...',
				mask: true
			});
			let list = [{}];
			for (let i = 0; i < 26; i++) {
				list[i] = {};
				list[i].name = String.fromCharCode(65 + i);
				list[i].id = i;
			}
			this.list = list;
			this.listCur = list[0];
		},
		onReady() {
			uni.hideLoading()
		},
		methods: {
			TabSelect(e) {
				this.tabCur = e.currentTarget.dataset.id;
				this.mainCur = e.currentTarget.dataset.id;
				this.verticalNavTop = (e.currentTarget.dataset.id - 1) * 50
			},
//希望有大佬给我讲解一下这个双向联动函数
			VerticalMain(e) {
				// #ifdef MP-ALIPAY
				   return false  //支付宝小程序暂时不支持双向联动 
				// #endif
				let that = this;
				let tabHeight = 0;
				if (this.load) {
					for (let i = 0; i < this.list.length; i++) {
						let view = uni.createSelectorQuery().select("#main-" + this.list[i].id);
						view.fields({
							size: true
						}, data => {
							this.list[i].top = tabHeight;
							tabHeight = tabHeight + data.height;
							this.list[i].bottom = tabHeight;
						}).exec();
					}
					this.load = false
				}
				let scrollTop = e.detail.scrollTop + 10;
				for (let i = 0; i < this.list.length; i++) {
					if (scrollTop > this.list[i].top && scrollTop < this.list[i].bottom) {
						this.verticalNavTop = (this.list[i].id - 1) * 50
						this.tabCur = this.list[i].id
						console.log(scrollTop)
						return false
					}
				}
			}
		},
	}
</script>
 
<style>
	.fixed {
		position: fixed;
		z-index: 99;
	}
 
	.VerticalNav.nav {
		width: 200upx;
		white-space: initial;
	}
 
	.VerticalNav.nav .cu-item {
		width: 100%;
		text-align: center;
		background-color: #fff;
		margin: 0;
		border: none;
		height: 50px;
		position: relative;
	}
 
	.VerticalNav.nav .cu-item.cur {
		background-color: #f1f1f1;
	}
 
	.VerticalNav.nav .cu-item.cur::after {
		content: "";
		width: 8upx;
		height: 30upx;
		border-radius: 10upx 0 0 10upx;
		position: absolute;
		background-color: currentColor;
		top: 0;
		right: 0upx;
		bottom: 0;
		margin: auto;
	}
 
	.VerticalBox {
		display: flex;
	}
 
	.VerticalMain {
		background-color: #f1f1f1;
		flex: 1;
	}
</style>
```

colorUI的简易教程差不多就到这了，欢迎各位大佬的指教！！！
