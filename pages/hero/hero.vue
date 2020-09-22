<style lang="less" scoped>
	.topImg{
		width: 100vw;
	}
	.heroInfo{padding: 20px;}
	.skinItem{text-align: center;}
	.skinTit{text-align: center;}
	.bigImg{
		width: 100vw; height: 100vh; position: absolute; top: 0; left:0;
		canvas{width: 100%; height:100%; background: #000;}
		button{position: absolute; bottom: 0; left: 0;}
	}
</style>

<template>
	<view v-if="this.hero.heroId">
		<image class="topImg" :src="getDefaultUrl()" mode="widthFix"></image>
		<view class="heroInfo">
			<view>{{hero.name}}</view>
			<view>{{hero.shortBio}}</view>
			<view v-for="skin in skins" class="skinItem">
				<image :id="skin.mainImg" :src="skin.mainImg || skin.chromaImg" mode="widthFix" @click="showBig(skin.mainImg)"></image>
				<view class="skinTit">{{skin.name}}</view>
			</view>
		</view>
		<view v-if="showBigImg" class="bigImg">
			<canvas  canvas-id="canvas" id="canvas"></canvas>
			<button type="default" @click="closeBigImg">关闭</button>
		</view>
	</view>
</template>

<script>
	import heroList from '@/libs/hero_list.json';
	export default {
		onLoad(option) {
			const hero = heroList.hero.find(h=>h.heroId = option.id);
			this.getHeroInfo(option.id);
		},
		data() {
			return {
				sysInfo:uni.getSystemInfoSync(),
				hero:{},
				skins:[],
				showBigImg:false,
				bigSrc:''
			};
		},
		watch:{
			hero:function(newHero,oldHero){
				uni.setNavigationBarTitle({
					title: newHero.name || "无"
				})
			}
		},
		methods:{
			getHeroInfo(heroId){
				const _this = this;
				uni.request({
					url:`http://game.gtimg.cn/images/lol/act/img/js/hero/${heroId}.js`,
					success(res) {
						// console.log(res);
						_this.hero = res.data.hero;
						_this.skins = res.data.skins;
					},
					fail(e) {
						uni.showToast({
							title:"请求失败",
							duration:2000
						})
					}
				})
			},
			getDefaultUrl(){
				return this.skins[0].sourceImg
			},
			showBig(url){
				this.showBigImg = true;
				this.bigSrc = url;
				this.drawCanvs(url);
			},
			closeBigImg(){
				this.showBigImg = false;
				this.bigSrc = '';
			},
			drawCanvs(url){
				const ctx = uni.createCanvasContext('canvas');
				const img = uni.getImageInfo({
					src:url,
					success:(image)=>{
						console.log(image.width)
						ctx.drawImage(image,0,0,200,200);
					}
				})
				
			}
		}
	}
</script>
