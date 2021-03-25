<template>
	<view>
		<!-- 专辑背景 start -->
		<view class="album_bg">
			<image mode="widthFix" :src="album.cover"></image>
			<view class="album_info">
				<view class="album_name">{{ album.name }}</view>
				<view class="album_btn">关注专辑</view>
			</view>
		</view>
		<!-- 专辑背景 end -->

		<!-- 专辑作者 start -->
		<view class="album_author">
			<view class="album_author_info">
				<image mode="widthFix" :src="album.user.avatar"></image>
				<view class="auther_name">{{ album.user.name }}</view>
			</view>
			<view class="album_auther_desc">
				<text>{{ album.desc }}</text>
			</view>
		</view>
		<!-- 专辑作者 end -->
		<!-- 列表 start -->
		<view class="album list">
			<view class="album_item" v-for="item in wallpaper" :key="item.id">
				<image
					mode="widthFix"
					@click="previewImage(item.thumb)"
					:src="item.thumb + item.rule.replace('$<Height>', 360)"
				></image>
			</view>
		</view>
		<!-- 列表 end -->
	</view>
</template>
<script>
export default {
	data() {
		return {
			params: {
				limit: 15,
				order: "new",
				skip: 0,
				first: 1,
			},
			id: -1,
			album: {},
			wallpaper: [],
			previewUrls: [],
		};
	},
	onLoad(options) {
		this.id = options.id;
		this.getList();
	},
	methods: {
		getList() {
			this.request({
				url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
				data: this.params,
			}).then((result) => {
				console.log(result);
				this.album = result.res.album;
				this.wallpaper = result.res.wallpaper;
				result.res.wallpaper.forEach(item => {
					this.previewUrls.push(item.thumb)
				});
			});
		},
		previewImage(current) {
			uni.previewImage({
				current,
				urls: this.previewUrls
			});
		}
	},
};
</script>
<style lang="scss" scoped>
.album_bg {
	position: relative;
	img {
	}

	.album_info {
		position: absolute;
		bottom: 0;
		left: 0;
		display: flex;
		justify-content: space-between;
		align-items: center;
		width: 100%;
		height: 80rpx;
		padding: 0 15rpx;
		color: #fff;
		font-size: 28rpx;
		.album_name {
			font-size: 40rpx;
		}

		.album_btn {
			display: flex;
			justify-content: center;
			align-items: center;
			width: 152rpx;
			height: 60rpx;
			background-color: $themeColor;
			border-radius: 10rpx;
			line-height: 60rpx;
		}
	}
}
.album_author {
	margin: 10px;
	.album_author_info {
		display: flex;
		align-items: flex-start;
		image {
			width: 50rpx;
			height: 50rpx;
		}

		.auther_name {
			margin-left: 30rpx;
			margin-bottom: 16rpx;
			font-size: 28rpx;
			color: #333;
		}
	}

	.album_auther_desc {
		text-indent: 2em;
		text {
			font-size: 28rpx;
		}
	}
}
.album.list {
	display: flex;
	flex-wrap: wrap;
	.album_item {
		width: 33.33%;
		border: 3px solid #fff;
		image {
		}
	}
}
</style>