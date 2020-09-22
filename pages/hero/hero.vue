<template>
	<view v-if="this.hero.heroId">
		<image :src="getHeadUrl()" mode="heightFix"></image>
		<view>{{hero.name}}</view>
		<view>{{hero.shortBio}}</view>
		<view v-for="skin in skinList">
			<image :src="getSkinUrl(skin.skinId)" mode="widthFix"></image>
			<view>{{skin.name}}</view>
		</view>
		
	</view>
</template>

<script>
	import heroList from '@/libs/hero_list.json';
	import skinList from '@/libs/cuskin_list.json';
	export default {
		onLoad(option) {
			const hero = heroList.hero.find(h=>h.heroId = option.id);
			uni.setNavigationBarTitle({
			    title: hero.name
			});
			this.getHeroInfo(option.id);
		},
		data() {
			return {
				hero:{},
				skinList:[]
			};
		},
		methods:{
			getHeroInfo(heroId){
				const _this = this;
				uni.request({
					url:`http://game.gtimg.cn/images/lol/act/img/js/hero/${heroId}.js`,
					header:{
						"User-Agent":"Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.102 Mobile Safari/537.36",
						"Host":"kongyuehui.com",
						"Accept":"*/*"
					},
					success(res) {
						console.log(res);
						_this.hero = res.data.hero;
						const skinKeys = Object.keys(skinList.cuskin);
						const _skinList = [];
						for(let skinId in skinList.cuskin){
							const skin = skinList.cuskin[skinId];
							if(skin.isChromas==="false" && skin.name.includes(res.data.hero.title)){
								skin.skinId = skinId;
								_skinList.push(skin);
							}
						}
						console.log(_skinList);
						_this.skinList = _skinList;
						
					},
					fail(e) {
						console.log(e);
					}
				})
			},
			getHeadUrl(){
				return `https://game.gtimg.cn/images/lol/act/img/champion/${this.hero.alias}.png`
			},
			getSkinUrl(skin){
				return `https://game.gtimg.cn/images/lol/act/img/skinloading/${skin}.jpg`;
			}
		}
	}
</script>

<style lang="less">

</style>
