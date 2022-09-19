<template>
	<view>
		<!--轮播图区域-->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item, i) in swiperList" :key="i"><!--循环-->
			<!--navigator完成轮播图与具体页面的链接-->
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id"><!--为轮播图各自添加url地址-->
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>
		
		<!--分类导航区域   到时候换成-->
		<view class="nav-list">
			<view class="nav-item" v-for="(item,i) in navList" :key="i"
			@click="navClickHandler(item)">
				<image :src="item.image_src" class="nav-image"></image>
			</view>
		</view>
		
		<!--楼层区-->
		<!--楼层的容器-->
		<view class="floor-list">
			<!--每个楼层的item-->
			<view class="floor-item" v-for="(item,i) in floorList" :key="i">
				<!--楼层标题-->
				<image :src="item.floor_title.image_src" class="floor-title"></image>
				<!--楼层图片区-->
				<view class="floor-img-box">
					<!--大图-->
					<navigator class="legt-img-box" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width+'rpx'}" mode="widthFix"></image>
					</navigator>
					<!--小图-->
					<view class="right-img-box">
						<navigator class="right-img-item" v-for="(item2,i2) in item.product_list" :key="i2" v-if="i2 !== 0" :url="item2.url">
							<image :src="item2.image_src" :style="{width:item2.image_width+'rpx'}" mode="widthFix" ></image>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				//轮播图的数据列表
				swiperList:[],
				//分类导航数据列表
				navList:[],
				//楼层数据
				floorList:[],
			};
		},
		onLoad(){
			//调用方法，获取轮播图数据
			this.getSwiperList(),
			this.getNavList(),
			this.getFloorList()
		},
		methods:{
			async getSwiperList(){
				const {data: res} = await uni.$http.get('/api/public/v1/home/swiperdata')
				//请求失败
				if(res.meta.status !== 200) return uni.$showMsg()//在main.js中对数据请求失败的弹窗进行封装-=】
				//请求成功
				this.swiperList = res.message
			},
			
			async getNavList(){
				const {data: res} = await uni.$http.get('/api/public/v1/home/catitems')
				if(res.meta.status !== 200)return uni.$showMSg()
				this.navList = res.message
			},
			
			navClickHandler(item){
				if(item.name === '分类'){
					uni.switchTab({
						url: '/pages/shop/shop'
					})
				}
			},
			//获取首页楼层数据
			async getFloorList(){
				const {data: res} = await uni.$http.get('/api/public/v1/home/floordata')
				if(res.meta.status!== 200)return uni.$showMSg()
				
				//对数据进行处理
				res.message.forEach(floor =>{
					floor.product_list.forEach(prod=>{
							prod.url='/subpkg/goods_list/goods_list?'+prod.navigator_url.split('?')[1]//为楼层图片添加URL地址，点击后可跳转
					})
				})
				this.floorList = res.message
			}
			
		}
		
	}
</script>

<style lang="scss">
	//轮播图块
	swiper{
		height: 330rpx;//高度330rpx的框
		
		.swiper-item,//内部的swiper-item和image填满
		image{
			width: 100%;
			height: 100%;
		}
	}
	
	.nav-list{
		display: flex;
		justify-content:space-around;
		margin: 15px 0;
		.nav-image{
			width:128rpx;
			height:140rpx;
		}
	}
	
	.floor-title{
		height: 60rpx;
		width: 100%;
		display: flex;
	}
	
	.right-img-box{
		display: flex;
		flex-wrap: wrap;//横向排列
		justify-content: space-around;//小图片分散对齐
	}
	
	.floor-img-box{
		display: flex;
		padding-left: 10rpx;
	}
</style>
