<template>
	<view class="container">
		<view class="hero" v-for="hero in heroList">
			<navigator :url="getHeroUrl(hero)">
				<image :src="getHead(hero)" :alt="hero.name" mode="widthFix" :lazy-load="true">
			</navigator>
		</view>
	</view>
</template>

<script>
	import heroList from '@/libs/hero_list.json'
	export default {
		onReady() {
			
		},
		data() {
			return {
				heroList:heroList.hero,
				
			}
		},
		methods: {
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
