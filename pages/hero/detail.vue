
<style lang="less" scoped>
	canvas{width: 100vw; height:100vh; background: #000;}
	button{position: absolute; bottom: 0; left: 0;}
</style>

<template>
	<view id="contain">
		<canvas canvas-id="canvas" :prop="bigSrc" :change:prop="stage.showImage" disable-scroll ></canvas>
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
	let stage,img;
	
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
					img.x = 0;
					img.y = 0;
					img.startX = 0;
					img.startY = 0;
					stage.addChild(img);
					stage.update();
					this.initHammer();
				}
				script.src = 'static/easeljs.min.js';
				document.head.append(script);
			}
		},
		methods:{
			initHammer(){
				console.log("init");
				const contain = document.getElementById('contain');
				const hammer = new Hammer(contain);
				console.log({
					regx:img.regX,
					regy:img.regY,
					x:img.x,
					y:img.y
				});
				hammer.on('pan',function(e){
					
					console.log({
						x:img.x,
						y:img.y,
						deltaX:e.deltaX,
						deltaY:e.deltaY
					});
					
					
					img.x = img.startX + e.deltaX;
					img.y = img.startY + e.deltaY;
					stage.update();
					if(e.isFinal){
						img.startX = img.x;
						img.startY = img.y;
					}
					
				})
				
				hammer.on('pinch',function(e){
					console.log(e.scale);
					img.scaleX = img.scaleY = e.scale;
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
			}
			
		}
	}
	
</script>


