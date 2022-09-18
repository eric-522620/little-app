<template>
	<view>
		<!--轮播图区域-->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item, i) in swiperList" :key="i"><!--循环-->
			<!--navigator完成轮播图与具体页面的链接-->
				<navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
					<image :src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				//轮播图的数据列表
				swiperList:[]
			};
		},
		onLoad(){
			//调用方法，获取轮播图数据
			this.getSwiperList()
		},
		methods:{
			async getSwiperList(){
				const {data: res} = await uni.$http.get('/api/public/v1/home/swiperdata')
				//请求失败
				if(res.meta.status !== 200) return uni.$showMsg()//在main.js中对数据请求失败的弹窗进行封装-=】
				//请求成功
				this.swiperList = res.message
				
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
	
</style>
