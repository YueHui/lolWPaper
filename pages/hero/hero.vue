<style lang="less" scoped>
	.topImg{
		width: 100vw;
	}
	.heroInfo{padding: 20px;}
	.skinItem{text-align: center;}
	.skinTit{text-align: center;}
	
</style>

<template>
	<view v-if="this.hero.heroId">
		<image class="topImg" :src="getDefaultUrl()" mode="widthFix"></image>
		<view class="heroInfo">
			<view>{{hero.name}}</view>
			<view>{{hero.shortBio}}</view>
			<view v-for="skin in skins" class="skinItem">
				<image :id="skin.mainImg" :src="skin.mainImg || skin.chromaImg" mode="widthFix" @click="showBig(skin)"></image>
				<view class="skinTit">{{skin.name}}</view>
			</view>
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
			showBig(skin){
				uni.setStorageSync("bigSrc",skin.mainImg || skin.chromaImg);
				uni.navigateTo({
					url:`/pages/hero/detail`
				})
			},
		}
	}
</script>

