<template>
	<div id="my-spc" class="my-container">
		<!-- <h2>我的信息</h2>
		<router-link to="/login">去登录</router-link>-->
		<!-- 未登录 -->
		<div class="header not-login" v-if="!token">
			<div class="login-btn" @click="$router.push('/login')">
				<img class="mobile-img" src="~@/assets/mobile.png" />
				<span class="text">登录&nbsp;/&nbsp;注册</span>
			</div>
		</div>
		<!-- 已登录 -->
		<div class="header user-info" v-else>
			<!-- 用户信息 -->
			<div class="base-info">
				<div class="left">
					<van-image class="avatar" round fit="cover" :src="userInfo.photo" />
					<span class="name">{{userInfo.name}}</span>
				</div>
				<div class="right">
					<van-button @click="$router.push('/my/edit')" size="mini" round>编辑资料</van-button>
				</div>
			</div>
			<!-- 用户数据 -->
			<div class="data-stats">
				<div class="data-item">
					<span class="count">{{userInfo.art_count}}</span>
					<span class="text">头条</span>
				</div>
				<div class="data-item">
					<span class="count">{{userInfo.follow_count}}</span>
					<span class="text">关注</span>
				</div>
				<div class="data-item">
					<span class="count">{{userInfo.like_count}}</span>
					<span class="text">喜欢</span>
				</div>
				<div class="data-item">
					<span class="count">{{userInfo.fans_count}}</span>
					<span class="text">粉丝</span>
				</div>
			</div>
		</div>
		<!-- 二宫格 -->
		<van-grid :column-num="2" class="grid-nav" clickable>
			<van-grid-item
				@click="$router.push('/my/collect')"
				class="grid-item"
				icon-prefix="toutiao"
				icon="shoucang"
				text="收藏"
			/>
			<van-grid-item
				@click="$router.push('/my/history')"
				class="grid-item"
				icon-prefix="toutiao"
				icon="lishi"
				text="历史"
			/>
		</van-grid>
		<!-- 链接 -->
		<van-cell-group>
			<van-cell title="消息通知" is-link />
			<van-cell class="mb-9" title="小智同学" is-link />
			<van-cell @click="quit" v-if="token" class="logout-cell" clickable title="退出登录" />
		</van-cell-group>
	</div>
</template>

<script>
	// import { removeItem } from "@/utils/storage.js";
	import { getUserInfo } from "@/api/User";
	import { mapGetters, mapMutations } from "vuex";
	export default {
		name: "My",
		computed: {
			...mapGetters(["token"]),
		},
		data() {
			return {
				userInfo: {},
			};
		},

		mounted() {},

		methods: {
			...mapMutations(["setUser"]),
			//获取用户信息
			async initData() {
				try {
					const { data } = await getUserInfo();
					// console.log(data);
					this.userInfo = data.data;
				} catch (error) {
					// console.log(error);
					// this.$toast.fail("登陆已过期,请重新登录");
					// this.setUser({});
				}
			},
			//退出
			async quit() {
				try {
					await this.$dialog.confirm({
						title: "确认退出吗",
						message: "退出会清楚资料",
					});
					// removeItem("TOUTIAO-USER");
					// this.$store.commit("setUser", {});
					this.setUser({});
				} catch (error) {}
			},
		},
		created() {
			if (this.token) this.initData();
		},
	};
</script>

<style lang="less" scoped>
	.my-container {
		.header {
			height: 361px;
			background: url("~@/assets/banner.png");
			background-size: cover;
		}
		.not-login {
			display: flex;
			justify-content: center;
			align-items: center;
			.login-btn {
				display: flex;
				flex-direction: column;
				justify-content: center;
				align-items: center;
				.mobile-img {
					width: 132px;
					height: 132px;
					margin-bottom: 15px;
				}
				.text {
					font-size: 28px;
					color: #fff;
				}
			}
		}
		.user-info {
			.base-info {
				height: 231px;
				padding: 76px 32px 23px;
				box-sizing: border-box;
				display: flex;
				justify-content: space-between;
				align-items: center;
				.left {
					display: flex;
					align-items: center;
					.avatar {
						width: 132px;
						height: 132px;
						border: 4px solid #fff;
						margin-right: 23px;
					}
					.name {
						font-size: 30px;
						color: #fff;
					}
				}
			}
			.data-stats {
				display: flex;
				.data-item {
					height: 130px;
					flex: 1;
					display: flex;
					flex-direction: column;
					justify-content: center;
					align-items: center;
					color: #fff;
					.count {
						font-size: 36px;
					}
					.text {
						font-size: 23px;
					}
				}
			}
		}
		/deep/.grid-nav {
			.grid-item {
				.van-grid-item__text {
					margin-top: 0;
				}
				height: 141px;
				i.toutiao {
					font-size: 45px;
				}
				.toutiao-shoucang {
					color: #eb5253;
				}
				.toutiao-lishi {
					color: #ff9d1d;
				}
				span.text {
					font-size: 28px;
				}
			}
		}
		.logout-cell {
			text-align: center;
			color: #d86262;
		}
		.mb-9 {
			margin-bottom: 9px;
		}
	}
</style>