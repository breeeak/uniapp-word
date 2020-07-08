<template>
	<view>
		<view class="user-section">
			<image class="bg" src="/static/user/user-bg.jpg"></image>
			<view class="user-info-box">
				<view class="portrait-box">
					<image class="portrait" :src="userInfo.avatarUrl || '/static/user/missing-face.png'"></image>
				</view>
				<view class="info-box">
					<text @click="toLogin" class="username">{{ hasLogin? (userInfo.nickname || '未设置昵称') : '立即登录' }}</text>
				</view>
			</view>
			<view class="vip-card-box">
				<image class="card-bg" src="/static/user/vipCard.png" mode="scaleToFill"></image>
				<view class="b-btn animation-fade">
					{{isVip ? 'VIP界面' : '立即开通'}}
				</view>
				<view class="tit">
					<text class="yticon icon-iLinkapp-"></text>
					{{isVip ? 'VIP用户' : '普通用户'}}
				</view>
				<text class="e-b">{{isVip ? '会员专享AI服务' : '继续下拉进入VIP'}}</text>
			</view>
		</view>

		<view class="cover-container" :style="[{
				transform: coverTransform,
				transition: coverTransition
			}]"
		 @touchstart="coverTouchstart" @touchmove="coverTouchmove" @touchend="coverTouchend">
			<image class="arc" src="/static/user/arc.png"></image>
			<view class="success-section">
					<view class="cu-bar bg-white solid-bottom ">
						<view class="action">
							<text class="cuIcon-title text-orange text-lg ">我的成就</text> 
						</view>
					</view>
					<view class="cu-list grid col-4 no-border">
						<view class="cu-item" v-for="(item,index) in achievementList" :key="index">
							<view :class="['cuIcon-' + item.cuIcon,'text-' + item.color]">
							</view>
							<text class="text-bold">{{item.name}}</text>
							<text class="text-sm">{{item.count}}</text>
						</view>
					</view>		
			</view>
			<view class="success-section">
					<view class="cu-bar bg-white solid-bottom">
						<view class="action">
							<text class="cuIcon-title text-pink text-lg">我的账户</text> 
						</view>
					</view>
					<view class="cu-list grid col-4 no-border">
						<view class="cu-item" v-for="(item,index) in accountList" :key="index">
							<view :class="['cuIcon-' + item.cuIcon,'text-' + item.color]">
							</view>
							<text class="text-bold">{{item.name}}</text>
							<text class="text-sm">{{item.count}}</text>
						</view>
					</view>		
			</view>
			
			<view class="cu-list menu arrow ">
				<view class="cu-item arrow" >
					<view class="content">
						<text class="cuIcon-questionfill text-grey"></text>
						<text class="text-grey">意见反馈</text>
					</view>
				</view>
				<view class="cu-item arrow" >
					<view class="content">	
						<text class="cuIcon-creativefill text-grey"></text>
						<text class="text-grey">进阶帮助</text>
					</view>
				</view>
				<view class="cu-item arrow" >
					<view class="content">
						<text class="cuIcon-repairfill text-grey"></text>
						<text class="text-grey">关于我们</text>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import listCell from '@/components/mix-list-cell';
	import {
		mapState
	} from 'vuex';
	let startY = 0,
		moveY = 0,
		pageAtTop = true;
	export default {
		components: {
			listCell
		},
		data() {
			return {
				coverTransform: 'translateY(0px)',
				coverTransition: '0s',
				moving: false,
				footprintList: [],
				isVip: false,
				achievementList: [{
					cuIcon: 'read',
					color: 'red',
					badge: 120,
					name: '掌握单词',
					count: '1'
				}, {
					cuIcon: 'appreciate',
					color: 'orange',
					badge: 1,
					name: '学习时长',
					count: '1'
				}, {
					cuIcon: 'calendar',
					color: 'yellow',
					badge: 0,
					name: '打卡日历',
					count: '1'
				}, {
					cuIcon: 'list',
					color: 'olive',
					badge: 22,
					name: '我的单词书',
					count: '2'
				}],
				accountList: [{
					cuIcon: 'medal',
					color: 'red',
					badge: 120,
					name: 'VIP会员',
					count: '1'
				}, {
					cuIcon: 'recharge',
					color: 'purple',
					badge: 1,
					name: '我的积分',
					count: '1'
				}, {
					cuIcon: 'redpacket',
					color: 'pink',
					badge: 0,
					name: '我的红包',
					count: '1'
				}, {
					cuIcon: 'forward',
					color: 'grey',
					badge: 22,
					name: '邀请用户',
					count: '2'
				}],
			}
		},
		onShow() {
			this.isVip = this.$api.isVip()
			this.loadFootprint()
		},
		onLoad() {},
		// #ifndef MP
		onNavigationBarButtonTap(e) {
			const index = e.index;
			if (index === 0) {
				this.navTo('/pages/set/set');
			} else if (index === 1) {
				// #ifdef APP-PLUS
				const pages = getCurrentPages();
				const page = pages[pages.length - 1];
				const currentWebview = page.$getAppWebview();
				currentWebview.hideTitleNViewButtonRedDot({
					index
				});
				// #endif
				uni.navigateTo({
					url: '/pages/notice/notice'
				})
			}
		},
		// #endif
		computed: {
			...mapState(['hasLogin', 'userInfo'])
		},
		methods: {
			async loadFootprint() {
				const that = this
				that.$api.request('footprint', 'getAllFootprint').then(res => {
					that.footprintList = res.data
				})
			},

			deleteFootprint(item) {
				const that = this
				uni.showModal({
					title: '删除？',
					content: '您确定要删除此足迹吗？',
					success: (e) => {
						if (e.confirm) {
							that.$api.request('footprint', 'deleteFootprint', {
								footprintId: item.id
							}).then(res => {
								that.loadFootprint()
							})
						}
					}
				})
			},

			toLogin() {
				if (!this.hasLogin) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},

			logout() {
				const that = this
				uni.showModal({
					title: '询问',
					content: '您确定要退出吗？',
					cancelText: '取消',
					confirmText: '确定',
					success: (e) => {
						if (e.confirm) {
							that.$store.commit('logout')
							that.$api.logout()
						}
					}
				})
			},

			/**
			 * 统一跳转接口,拦截未登录路由
			 * navigator标签现在默认没有转场动画，所以用view
			 */
			navTo(url) {
				if (!this.hasLogin) {
					url = '/pages/public/login';
				}
				uni.navigateTo({
					url
				})
			},

			/**
			 *  会员卡下拉和回弹
			 *  1.关闭bounce避免ios端下拉冲突
			 *  2.由于touchmove事件的缺陷（以前做小程序就遇到，比如20跳到40，h5反而好很多），下拉的时候会有掉帧的感觉
			 *    transition设置0.1秒延迟，让css来过渡这段空窗期
			 *  3.回弹效果可修改曲线值来调整效果，推荐一个好用的bezier生成工具 http://cubic-bezier.com/
			 */
			coverTouchstart(e) {
				if (pageAtTop === false) {
					return;
				}
				this.coverTransition = 'transform .1s linear';
				startY = e.touches[0].clientY;
			},
			coverTouchmove(e) {
				moveY = e.touches[0].clientY;
				let moveDistance = moveY - startY;
				if (moveDistance < 0) {
					this.moving = false;
					return;
				}
				this.moving = true;
				if (moveDistance >= 80 && moveDistance < 100) {
					moveDistance = 80;
				}

				if (moveDistance > 0 && moveDistance <= 80) {
					this.coverTransform = `translateY(${moveDistance}px)`;
				}
			},
			coverTouchend() {
				if (this.moving === false) {
					return;
				}
				this.moving = false;
				this.coverTransition = 'transform 0.3s cubic-bezier(.21,1.93,.53,.64)';
				this.coverTransform = 'translateY(0px)';
			}
		}
	}
</script>
<style lang='scss'>
	%flex-center {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	%section {
		background: #fff;
		border-radius: 10upx;
	}

	.user-section {
		height: 520upx;
		padding: 100upx 30upx 0;
		position: relative;

		.bg {
			position: absolute;
			left: 0;
			top: 0;
			width: 100%;
			height: 100%;
			filter: blur(1px);
			opacity: .7;
		}
	}

	.user-info-box {
		height: 180upx;
		display: flex;
		align-items: center;
		position: relative;
		z-index: 1;

		.portrait {
			width: 130upx;
			height: 130upx;
			border: 5upx solid #fff;
			border-radius: 50%;
		}

		.username {
			font-size: $font-lg + 6upx;
			color: $font-color-dark;
			margin-left: 20upx;
		}
	}

	.vip-card-box {
		display: flex;
		flex-direction: column;
		color: #f7d680;
		height: 240upx;
		background: linear-gradient(left, rgba(0, 0, 0, .7), rgba(0, 0, 0, .8));
		border-radius: 16upx 16upx 0 0;
		overflow: hidden;
		position: relative;
		padding: 20upx 24upx;

		.card-bg {
			position: absolute;
			top: 20upx;
			right: 0;
			width: 380upx;
			height: 260upx;
		}

		.b-btn {
			position: absolute;
			right: 20upx;
			top: 16upx;
			width: 132upx;
			height: 40upx;
			text-align: center;
			line-height: 40upx;
			font-size: 22upx;
			color: #36343c;
			border-radius: 20px;
			background: linear-gradient(left, #f9e6af, #ffd465);
			z-index: 1;
		}

		.tit {
			font-size: $font-base+2upx;
			color: #f7d680;
			margin-bottom: 28upx;

			.yticon {
				color: #f6e5a3;
				margin-right: 16upx;
			}
		}

		.e-b {
			font-size: $font-sm;
			color: #d8cba9;
			margin-top: 10upx;
		}
	}

	.cover-container {
		background: $page-color-base;
		margin-top: -150upx;
		padding: 0 30upx;
		position: relative;
		background: #f5f5f5;
		padding-bottom: 20upx;

		.arc {
			position: absolute;
			left: 0;
			top: -34upx;
			width: 100%;
			height: 36upx;
		}
	}

	.tj-sction {
		@extend %section;

		.tj-item {
			@extend %flex-center;
			flex-direction: column;
			height: 140upx;
			font-size: $font-sm;
			color: #75787d;
		}

		.num {
			font-size: $font-lg;
			color: $font-color-dark;
			margin-bottom: 8upx;
		}
	}

	.access-section {
		@extend %section;
		padding: 28upx 0;
		margin-top: 20upx;

		.order-item {
			@extend %flex-center;
			width: 120upx;
			height: 120upx;
			border-radius: 10upx;
			font-size: $font-sm;
			color: $font-color-dark;
		}

		.yticon {
			font-size: 48upx;
			margin-bottom: 18upx;
			color: #fa436a;
		}

		.icon-shouhoutuikuan {
			font-size: 44upx;
		}
	}

	
</style>
