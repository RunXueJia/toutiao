<template>
	<div class="home-container">
		<!-- 导航栏 -->
		<van-nav-bar class="page-nav-bar" fixed>
			<van-button
				to="/search"
				class="search-btn"
				slot="title"
				type="info"
				size="small"
				round
				icon="search"
			>搜索</van-button>
		</van-nav-bar>
		<!-- /导航栏 -->
		<van-tabs class="channel-tabs" v-model="active" swipeable animated border>
			<van-tab v-for="obj in Channels" :key="obj.id" :title="obj.name">
				<ArticleList :channel="obj"></ArticleList>
			</van-tab>

			<!-- 右侧自定义内容 -->

			<template #nav-right>
				<!-- 占位元素 -->
				<div class="placeholder"></div>
				<!-- 右侧按钮 -->
				<div class="hamburger-btn" @click="editShow=true">
					<i class="toutiao toutiao-gengduo"></i>
				</div>
			</template>
		</van-tabs>
		<!-- 频道编辑弹出层 -->
		<van-popup
			v-model="editShow"
			closeable
			close-icon-position="top-left"
			position="bottom"
			:style="{ height: '100%' }"
		>
			<ChannelEdit
				@changeIndex="changeIndex"
				@changeTab="changeTab"
				:userChannels="Channels"
				:activeIndex="active"
			></ChannelEdit>
		</van-popup>
	</div>
</template>

<script>
	import ArticleList from "./components/index.vue";
	import ChannelEdit from "./components/setEdit.vue";
	import { getChannelsApi } from "@/api/home";
	import { mapGetters } from "vuex";
	import { getItem } from "@/utils/storage";
	export default {
		name: "HomeIndex",
		components: {
			ArticleList,
			ChannelEdit,
		},
		props: {},
		data() {
			return {
				active: 0,
				Channels: [],
				editShow: false,
			};
		},
		computed: {
			...mapGetters(["token"]),
		},
		watch: {},
		created() {
			this.initData();
		},
		mounted() {},
		methods: {
			//根据登陆状态获取频道
			async initData() {
				if (this.token) {
					try {
						const { data } = await getChannelsApi();
						this.Channels = data.data.channels;
						// console.log(data);
					} catch (error) {
						this.$toast.fail("获取用户频道列表失败");
					}
				} else {
					if (getItem("TOUTIAO-USERCHANNELS"))
						return (this.Channels = getItem("TOUTIAO-USERCHANNELS"));
					try {
						const { data } = await getChannelsApi();
						this.Channels = data.data.channels;
					} catch (error) {
						this.$toast.fail("获取用户频道列表失败");
					}
				}
			},
			//跳转tab
			changeTab(index) {
				this.active = index;
				this.editShow = false;
			},
			//更新索引
			changeIndex() {
				this.active--;
			},
		},
	};
</script>

<style scoped lang="less">
	.home-container {
		.van-nav-bar {
			background-color: #3296fa;
			.van-icon {
				color: #fff;
			}
		}
		padding-top: 175px;
		padding-bottom: 100px;
		/deep/ .van-nav-bar__title {
			max-width: unset;
		}
		.search-btn {
			width: 555px;
			height: 64px;
			background-color: #5babfb;
			border: none;
			font-size: 28px;
			.van-icon {
				font-size: 32px;
			}
		}
		/deep/ .channel-tabs {
			.van-tabs__wrap {
				position: fixed;
				top: 92px;
				z-index: 1;
				left: 0;
				right: 0;
				height: 82px;
			}

			.van-tab {
				border-right: 1px solid #edeff3;
				min-width: 200px;
				font-size: 30px;
				color: #777777;
			}

			.van-tab--active {
				color: #333333;
			}

			.van-tabs__nav {
				padding-bottom: 0;
			}

			.van-tabs__line {
				bottom: 8px;
				width: 31px !important;
				height: 6px;
				background-color: #3296fa;
			}

			.placeholder {
				flex-shrink: 0;
				width: 66px;
				height: 82px;
			}

			.hamburger-btn {
				position: fixed;
				right: 0;
				display: flex;
				justify-content: center;
				align-items: center;
				width: 66px;
				height: 82px;
				background-color: #fff;
				background-color: rgba(255, 255, 255, 0.902);
				i.toutiao {
					font-size: 33px;
				}
				&:before {
					content: "";
					position: absolute;
					left: 0;
					width: 1px;
					height: 58px;
					background-image: url(~@/assets/gradient-gray-line.png);
					background-size: contain;
				}
			}
		}
	}
</style>