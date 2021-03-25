<template>
    <scroll-view class="album_scroll_vivew" scroll-y @scrolltolower="handleToLower">
		<!-- 轮播图 start -->
		<view class="album_swiper">
			<swiper indicator-dots autoplay circular>
				<swiper-item
				v-for="item in banner"
				:key="item.id"
				>
					<image :src="item.thumb"></image>
				</swiper-item>
			</swiper>
		</view>
		<!-- 轮播图 end -->
		
		<!-- 列表 start -->
		<view class="album_list">
			<navigator class="album_item"
			v-for="item in album"
			:key="item.id"
			:url="`/pages/album/index?id=${item.id}`"
			>
				<view class="album_img">
					<image mode="widthFix" :src="item.cover"></image>
				</view>
				<view class="album_info">
					<view class="">
						<text class="album_name">{{ item.name }}</text>
						<text class="album_desc">{{ item.desc }}</text>
					</view>
					<view class="album_btn">
						<view class="album_attention">+ 关注</view>
					</view>
				</view>
			</navigator>
		</view>
		<!-- 列表 end -->
    </scroll-view>
</template>
<script>
    export default {
		data() {
			return {
				params: {
					limit: 15,
					order: 'new',
					skip: 0
				},
				banner: [],
				album: [],
				hasMore: true,	// 是否还有下一页
				hasLoading: false,	// 当前页的数据是否已经加载完成
			}
		},
		mounted() {
			this.getList();
		},
		methods: {
			getList() {
				this.hasLoading = true;
				this.request({
					url: 'http://157.122.54.189:9088/image/v1/wallpaper/album',
					data: this.params
				})
				.then(result => {
					this.banner = result.res.banner;
					
					if (result.res.album.length === 0 ) {
						this.hasMore = false;
						// 修改加载状态
						this.hasLoading = false;
						return;
					}
					if (this.banner.length === 0) {	// 第一次请求
						this.banner = result.res.banner;
					}
					// 获取热门数据列表
					this.album = [...this.album, ...result.res.album];
					
					// 修改加载状态
					this.hasLoading = false;
				})
				
			},
			handleToLower() {
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
			},
		},
    }
</script>
<style lang="scss" scoped>
	.album_scroll_vivew {
		height: calc(100vh - 36px);
	}
    .album_swiper {
		swiper {
			height: calc(750rpx / 2.3);
			swiper-item {
				image {
					height: 100%;
				}
			}
		}
	}
	.album_list {
		margin: 10rpx;
		.album_item {
			display: flex;
			justify-content: space-between;
			padding: 10rpx 0;
			border-bottom: 1px solid #ccc;
			.album_img {
				flex: 1;
				padding: 10rpx;
				image {
					height: 90%;
				}
			}
			.album_info {
				flex: 2;
				display: flex;
				flex-direction: column;
				justify-content: space-between;
				margin-left: 10rpx;
				overflow: hidden;
				.album_name {
					display: block;
					font-size: 30rpx;
					font-weight: 600;
					color: #333;
				}
				.album_desc {
					display: block;
					width: 100%;
					font-size: 24rpx;
					color: #666;
					overflow: hidden;
					text-overflow: ellipsis;
					white-space: nowrap;
				}
				.album_btn {
					display: flex;
					justify-content: flex-end;
					padding: 10rpx;
					.album_attention {
						padding: 5rpx;
						font-size: 24rpx;
						color: #ff0000;
						border: 1px solid #ff0000;
					}
				}

			}
			
		}

	}

</style>