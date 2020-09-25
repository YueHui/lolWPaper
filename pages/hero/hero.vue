<style lang="less" scoped>
	.topImg{
		width: 100vw;
	}
	.heroInfo{padding: 20px;}
	.skinItem{text-align: center;}
	.skinTit{text-align: center;}
	.bigImg{
		width: 100vw; height: 100vh; position: fixed; top: 0; left:0; display: none;
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
				<image :id="skin.mainImg" :src="skin.mainImg || skin.chromaImg" mode="widthFix" @click="stage.showImage(skin.mainImg)"></image>
				<view class="skinTit">{{skin.name}}</view>
			</view>
		</view>
		<view class="bigImg" id="bigImg">
			<canvas  canvas-id="canvas" id="canvas"></canvas>
			<button type="default" button-id="closeBtn">关闭</button>
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
			},
			closeBigImg(){
				this.showBigImg = false;
				this.bigSrc = '';
			}
		}
	}
</script>

<script module="stage" lang="renderjs">
	let stage;
	export default {
		mounted() {
			const script = document.createElement('script')
			// view 层的页面运行在 www 根目录，其相对路径相对于 www 计算
			
			script.onload = ()=>{
				// this.initHammer();
			}
			script.src = 'static/hammer.min.js';
			document.head.append(script);
			
			
		},
		methods:{
			initHammer(){
				const hammer = new Hammer(document.getElementById('canvas'));
				hammer.on('pan',function(e){
					console.log(e);
				})
			},
			loadImg(url){
				return new Promise((resolve,reject)=>{
					const image = new Image();
					image.onload=function(){
						resolve(image);
					}
					image.onerror=function(e){
						console.log(e);
						reject();
					}
					image.src=url;
				})
				
			},
			closeBigImg(){
				console.log('close');
				document.getElementById("bigImg").style.display = "none";
			},
			async showImage(url){
				console.log("showImage");
				document.getElementById("bigImg").style.display = "block";
				const image = await this.loadImg(url);
				const canvas = document.getElementsByTagName('canvas')[0];
				
				const ctx = canvas.getContext('2d');
				ctx.drawImage(image,0,0);
				
				this.initHammer();
				
				document.querySelector('[button-id=closeBtn]').click=()=>{
					console.log('close');
					this.closeBigImg()
				}
			},
			
		}
	}
</script>
