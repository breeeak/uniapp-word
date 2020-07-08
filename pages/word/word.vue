<template >
	<view class="container">
		<!-- 小程序头部兼容 -->
		<!-- #ifdef MP -->
		<view class="mp-search-box">
			<input @click="naviageToPage('/pages/discover/search')" class="ser-input" type="text" value="搜索课程" disabled />
		</view>
		<!-- #endif -->
		<!-- #ifndef MP -->
		<view class="status_bar">
			<!-- 这里是状态栏  空出-->
		</view>
		<!-- #endif -->
		<!-- 学习进度 -->
		<view class="padding bg-white">
			<view class="cu-progress round striped active ">
				<view class="bg-green" :style="[{ width:loading?progresses[0]+'%':''}]">{{progresses[0]>10?progresses[0]+'%已学':''}}</view>
				<view class="bg-cyan" :style="[{ width:loading?progresses[1]+'%':''}]">{{progresses[1]>10?progresses[1]+'%学习中':''}}</view>
				<view class="bg-grey" :style="[{ width:loading?progresses[2]+'%':''}]">{{progresses[2]>10?progresses[2]+'%未学':''}}</view>
			</view>
		</view>
		<!-- 近日学习情况 -->
		<scroll-view scroll-y class="page">
			<view class="nav-list">
				<navigator hover-class='none' :url="'/pages/word/' + item.page" class="nav-li" navigateTo :class="'bg-'+item.color"
				 :style="[{animation: 'show ' + ((index+1)*0.3+1) + 's 1'}]" v-for="(item,index) in elements" :key="index">
					<view class="nav-title">{{item.title}} <text class="text-tag">{{index%2?"词":"分"}}</text></view>
					<view class="nav-name">{{item.name}}</view>
					<text :class="'cuIcon-' + item.cuIcon"></text>
				</navigator>	
			</view>
			<view class="cu-tabbar-height"></view>
		</scroll-view>

	</view>
</template>

<script>
	export default {
		name: "basics",
		data() {
			return {
				ColorList: this.ColorList,
				loading: false,
				progresses: [
					10, 10, 80
				],
				elements: [{
						title: '1220',
						name: '今日学习',
						page: 'statistics',
						color: 'pink',
						cuIcon: 'newsfill'
					},
					{
						title: '122',
						name: '今日掌握',
						page: 'wordList',
						color: 'green',
						cuIcon: 'colorlens'
					},
				],

			}
		},
		onLoad: function() {
			let that = this;
			setTimeout(function() {
				that.loading = true
			}, 500)
		},
		methods: {
			//点击进页面
			naviageToPage(page) {
				uni.navigateTo({
					url: page
				})
			}
		}
	}
</script>

<style lang="scss">
	.status_bar {
		height: $navigation-bar-height+25upx;
		width: 100%;
	}
	.text-tag{
		margin-right: 65upx;
		margin-top: 10upx;
		font-size:24upx
	}
</style>
