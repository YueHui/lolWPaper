<template>
	<view>
		<view>版本:{{heroListInfo.version}}  更新时间:{{heroListInfo.fileTime}}</view>
		<button type="default" @click="loadHeroList">更新</button>
		<view class="container">
			<view class="hero" v-for="hero in heroListInfo.hero" >
				<navigator :url="getHeroUrl(hero)">
					<image :src="getHead(hero)" :alt="hero.name" mode="widthFix" :lazy-load="true">
				</navigator>
			</view>
		</view>
		<view v-if="!heroListInfo.hero">
			加载中...
		</view>
	</view>
</template>

<script>
	export default {
		mounted() {
			try{
				const heroList = uni.getStorageSync('heroList');
				if(heroList){
					this.heroListInfo = heroList;
				}else{
					this.loadHeroList();
				}
				
			}catch(e){
				uni.showToast({
					icon:'none',
					title:"加载数据错误"
				});
			}
		},
		data() {
			return {
				heroListInfo:{},
			}
		},
		methods: {
			loadHeroList(){
				uni.showToast({
					icon:'loading',
					title:"加载中..."
				});
				uni.request({
					url:'https://game.gtimg.cn/images/lol/act/img/js/heroList/hero_list.js',
					success(res){
						//{hero,version,fileName,fileTime}
						this.heroListInfo = res.data;
						uni.setStorageSync('heroList',res.data)
					}
					
				})
			},
			getHead(hero){
				return `//game.gtimg.cn/images/lol/act/img/champion/${hero.alias}.png`
			},
			getHeroUrl(hero){
				return `/pages/hero/hero?id=${hero.heroId}`
			}
		}
	}
</script>

<style lang="less">
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;
		display: flex;
		flex-wrap: wrap;
	}
	.hero{
		width: 25%; height: auto; padding: 5px; box-sizing: border-box;
		image{width: 100%;}
	}
</style>
