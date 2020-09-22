<style lang="less" scoped>
	.topImg{
		width: 100vw;
	}
	.heroInfo{padding: 20px;}
	.skinItem{text-align: center;}
	.skinTit{text-align: center;}
	.bigImg{width: 100vw; height: 100vh; position: fixed; top: 0; left:0; background: rgba(0,0,0,0.5); display: none;}
</style>

<template>
	<view v-if="this.hero.heroId">
		<image class="topImg" :src="getDefaultUrl()" mode="widthFix"></image>
		<view class="heroInfo">
			<view>{{hero.name}}</view>
			<view>{{hero.shortBio}}</view>
			<view v-for="skin in skins" class="skinItem">
				<image :src="skin.mainImg || skin.chromaImg" mode="widthFix" @click="showBig"></image>
				<view class="skinTit">{{skin.name}}</view>
			</view>
		</view>
		<view class="bigImg">
			
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
				hero:{},
				skins:[]
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
						console.log(e);
					}
				})
			},
			getDefaultUrl(){
				return this.skins[0].sourceImg
			},
			showBig(){
				
			}
		}
	}
</script>
