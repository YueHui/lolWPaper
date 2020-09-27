
<style lang="less" scoped>
	canvas{width: 100vw; height:100vh; background: #000;}
	button{position: absolute; bottom: 0; left: 0;}
	.slider{position: absolute; left: 100px; bottom: 0; width: 50%;}
	.download{right: 0; left: auto;}
</style>

<template>
	<view id="contain">
		<canvas canvas-id="canvas" :prop="bigSrc" :change:prop="stage.showImage" disable-scroll @touchstart="stage.touchStart" @touchmove="stage.touchMove" @touchend="stage.touchEnd" ></canvas>
		<button type="default" @click="goBack">返回</button>
		<button type="default" @click="download" class="download">下载</button>
	</view>
</template>

<script>
	export default {
		onLoad() {
			this.bigSrc = uni.getStorageSync('bigSrc');
		},
		data() {
			return {
				bigSrc:''
			};
		},
		methods:{
			goBack(){
				uni.navigateBack({});
			},
			download(){
				uni.canvasToTempFilePath({
					canvasId:"canvas",
					success(res) {
						uni.saveImageToPhotosAlbum({
							filePath:res.tempFilePath,
							success({savedFilePath}) {
								uni.showToast({
									icon:'none',
									title:`保存成功`
								})
							},
							fail(e){
								uni.showToast({
									icon:'none',
									title:'保存失败'
								});
							}
						})
					}
				})
			}
		}
	}
</script>

<script module="stage" lang="renderjs">
	let stage,img,startX,startY,originTouches,filter;
	
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
					
					const bigWidth = bigImg.width*2;
					const bigHeight = bigImg.height *2;
					
					const bgImg = new createjs.Bitmap(bigImg);
					bgImg.regX = bigImg.width/2;
					bgImg.regY = bigImg.height/2;
					bgImg.x = (window.innerWidth-bigWidth)/2;
					bgImg.y = (window.innerHeight-bigHeight)/2;
					filter = new createjs.BlurFilter(20, 20, 1);
					bgImg.filters = [filter];
					bgImg.cache(0,0,bigImg.width,bigImg.height);
					bgImg.scaleX = bgImg.scaleY = 2;
					
					img = new createjs.Bitmap(bigImg);
					img.width = bigImg.width;
					img.height = bigImg.height;
					img.regX = bigImg.width/2;
					img.regY = bigImg.height/2;
					img.x = (window.innerWidth - bigImg.width)/2;
					img.y = (window.innerHeight - bigImg.height)/2;
					
					img.originX = img.x;
					img.originY = img.y;
					img.originScale = 1;
					stage.addChild(bgImg);
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
					let scale = img.originScale + this.getScale(e.touches);
					img.scaleX = img.scaleY = Math.min(3,Math.max(0.2,scale));
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
				return d1<d2?(1-d1/d2):(d2/d1-1);
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
						reject();
					}
					image.src=url;
				})
			}
			
		}
	}
	
</script>


