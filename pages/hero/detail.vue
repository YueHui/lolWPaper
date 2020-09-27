
<style lang="less" scoped>
	canvas{width: 100vw; height:100vh; background: #000;}
	button{position: absolute; bottom: 0; left: 0;}
</style>

<template>
	<view id="contain">
		<canvas canvas-id="canvas" :prop="bigSrc" :change:prop="stage.showImage" disable-scroll @touchstart="stage.touchStart" @touchmove="stage.touchMove" @touchend="stage.touchEnd" ></canvas>
		<button type="default" @click="goBack">返回</button>
	</view>
</template>

<script>
	export default {
		onLoad() {
			this.bigSrc = uni.getStorageSync('bigSrc');
			console.log(this.bigSrc);
		},
		data() {
			return {
				bigSrc:''
			};
		},
		methods:{
			goBack(){
				uni.navigateBack({
					
				})
			}
		}
	}
</script>

<script module="stage" lang="renderjs">
	let stage,img,startX,startY,originTouches;
	
	export default {
		data(){
			return {
				show:false
			}
		},
		mounted() {
			const script = document.createElement('script')
			// view 层的页面运行在 www 根目录，其相对路径相对于 www 计算
			
			script.onload = loadEaseljs.bind(this);
			script.src = 'static/hammer.min.js';
			document.head.append(script);
			
			function loadEaseljs(){
				const script = document.createElement('script')
				// view 层的页面运行在 www 根目录，其相对路径相对于 www 计算
				
				script.onload = async ()=>{
					
					stage = new createjs.Stage(document.getElementsByTagName('canvas')[0]);
					stage.enableMouseOver();
					const bigImg = await this.loadImg(this.bigSrc);
					img = new createjs.Bitmap(bigImg);
					img.regX = bigImg.width/2;
					img.regY = bigImg.height/2;
					img.x = bigImg.width/2;
					img.y = bigImg.height/2;
					
					img.originX = bigImg.width/2;
					img.originY = bigImg.height/2;
					img.originScale = 1;
					stage.addChild(img);
					stage.update();
					// this.initHammer();
				}
				script.src = 'static/easeljs.min.js';
				document.head.append(script);
			}
		},
		methods:{
			touchStart(e){
				if(e.touches.length === 1){
					startX = e.touches[0].x;
					startY = e.touches[0].y;
				}else{
					originTouches = e.touches;
				}
				
			},
			touchMove(e){
				if(e.touches.length === 1){
					img.x = img.originX + e.touches[0].x - startX;
					img.y = img.originY + e.touches[0].y - startY;
					stage.update();
				}else{
					console.log(this.getScale(e.touches));
					img.scaleX = img.scaleY = (img.originScale + this.getScale(e.touches));
					stage.update();
				}
			},
			touchEnd(e){
				img.originX = img.x;
				img.originY = img.y;
				img.originScale = img.scaleX;
			},
			getScale(posList){
				let d1 = this.getDistance(originTouches[0],originTouches[1]);
				let d2 = this.getDistance(posList[0],posList[1]);
				return (d1/d2) * (d1<d2?-1:1);
			},
			getDistance(p1,p2){
				const dx = Math.abs(p1.x - p2.x);
				const dy = Math.abs(p1.y - p2.y);
				return Math.sqrt(dx*dx + dy*dy)
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
			}
			
		}
	}
	
</script>


