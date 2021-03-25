<template>
    <scroll-view @scrolltolower="handleToLower" class="recommend_view" scroll-y v-if="recommends.length > 0">
		<!-- 推荐 start -->
		<view class="recommend_wrap">
			<view
				class="recommend_item"
				v-for="item in recommends"
				:key="item.id"
			>
				<image mode="widthFix" :src="item.thumb"></image>
			</view>
		</view>
		<!-- 推荐 end -->
		<!-- 月份 start -->
		<view class="monthes_wrap">
			<view class="monthes_title">
				<view class="monthes_title_info">
					<view class="monthes_info">
						<text> {{ monthes.DD }} </text>
						/ {{ monthes.MM }} 月
					</view>
					<view class="monthes_text">{{ monthes.title }}</view>
				</view>
				<view class="monthes_title_more">更多 > </view>
			</view>
			<view class="monthes_content">
				<view class="monthes_item"
				v-for="item in monthes.items"
				:key="item.id"
				>
					<image mode="aspectFill" :src="item.thumb+item.rule.replace('$<Height>', 360)"></image>
				</view>
			</view>
		</view>
		<!-- 月份 end -->
		
		<!-- 热门 start -->
		<view class="hots_wrap">
			<view class="hots_title">
				<text> 热门 </text>
			</view>
			<view class="hots_content">
				<view class="hots_item"
				v-for="item in hots"
				:key="itme.id"
				>
					<image mode="widthFix" :src="item.thumb"></image>
				</view>
			</view>
		</view>
		<!-- 热门 end -->
	</scroll-view>
</template>
<script>
	import moment from 'moment';
    export default {
        data() {
            return {
                // 推荐列表
                recommends: [],
				monthes: {},
				hots: [],
				params: {
					limit: 15,
					order: 'hot',
					skip: 0
				},
				hasMore: true,	// 是否还有下一页
				hasLoading: false,	// 当前页的数据是否已经加载完成
            }
        },
        mounted() {
            // this.getList();
        },
		methods: {
			// 获取数据
			getList() {
				this.hasLoading = true;
				this.request({
				    url: 'http://157.122.54.189:9088/image/v3/homepage/vertical',
				    data: this.params
				})
				.then(result => {
					if (result.res.vertical.length < this.params.limit) {
						this.hasMore = false;
						this.hasLoading = false;
						return;
					}
					if (this.recommends.length === 0) {	// 第一次请求
						// 推荐模块
						this.recommends = result.res.homepage[1].items;
						// 月份模块
						this.monthes = result.res.homepage[2];
						// 将时间戳 改成 需要的格式 moment.js
						this.monthes.MM = moment(this.monthes.stime).format('MM');
						this.monthes.DD = moment(this.monthes.stime).format('DD');
						console.log(this.monthes);
					}
					// 获取热门数据列表
					this.hots = [...this.hots, ...result.res.vertical];
					
					// 修改加载状态
					this.hasLoading = false;
				})
			},
			// 滚动条触底事件
			handleToLower() {
				/*
					修改参数 skip += limit
					重新发送请求
					请求成功，拼接数据
				*/
			   if (this.hasLoading) return;	// 如果上一页数据还在加载中，直接返回
			   if (this.hasMore) {
				   this.params.skip += this.params.limit;
				   this.getList();
			   } else {
					// 弹窗提示用户
					uni.showToast({
					   title: '没有数据了',
					   icon: "none"
					});
					this.hasLoading = true;
				}
			}
		}
    }
</script>
<style lang="scss" scoped>
	// 
	.recommend_view {
		height: calc(100vh - 41px);
	}
    .recommend_wrap {
        display: flex;
        flex-wrap: wrap;
        margin: 10rpx 0;
        .recommend_item {
            width: 50%;
            border: 5rpx solid #fff;
        }
    }
    .monthes_wrap {
        .monthes_title {
            display: flex;
            justify-content: space-between;
			align-items: center;
			padding: 0 20rpx;
            .monthes_title_info {
                display: flex;
                align-items: center;
                color: $themeColor;
                font-size: 30rpx;
                font-weight: 600;
                .monthes_info {
                    text {
                        font-size: 36rpx;
                    }
                }
                .monthes_text {
                    font-size: 34rpx;
                    color: #666;
                    margin-left: 30rpx;
                }
            }

            .monthes_title_more {
                font-size: 24rpx;
                color: $themeColor;
            }
        }

        .monthes_content {
			display: flex;
			flex-wrap: wrap;
			margin: 20rpx 0;
			.monthes_item {
				width: 33.33%;
				border: 5rpx solid #fff;
			}
        }
    }
	.hots_wrap {
		margin: 0 10rpx;
		.hots_title {
			margin: 10rpx 0;
			text {
				padding-left: 10rpx;
				border-left: 5rpx solid $themeColor;
				font-size: 24rpx;
				font-weight: 600;
			}
		}
		.hots_content {
			display: flex;
			flex-wrap: wrap;
			.hots_item {
				width: 33.33%;
				image {
					border: 5rpx solide #fff;
				}
			}

		}
	}
</style>