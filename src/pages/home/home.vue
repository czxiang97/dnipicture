<template>
    <view>
        <view class="home_tab">
            <view class="home_tab_title">
                <view class="title_inner">
                    <uni-segmented-control
                        :values="items"
                        :current="current"
                        @clickItem="onClickItem"
                        style-type="text"
                        active-color="#d4237a"
                    ></uni-segmented-control>
                </view>
                <text class="iconfont icon-search"></text>
            </view>
            <view class="home_tab_content">
                <view v-show="current === 0">
                    <home-recommend />
                </view>
                <view v-show="current === 1">
                    <home-category />
                </view>
                <view v-show="current === 2">
                    <home-new />
                </view>
                <view v-show="current === 3">
                    <home-album />
                </view>
            </view>
        </view>
    </view>
</template>
<script>
import homeAlbum from './home-album';
import homeCategory from './home-category';
import homeNew from './home-new';
import homeRecommend from './home-recommend';
import {uniSegmentedControl} from "@dcloudio/uni-ui";
    export default {
        components: {
            homeAlbum,
            homeCategory,
            homeNew,
            homeRecommend,
            uniSegmentedControl,
        },
        data() {
            return {
                items: [
                    '推荐',
                    '分类',
                    '最新',
                    '专辑'
                ],
                current: 3,
            }
        },
        onLoad() {

        },
        methods: {
            onClickItem(e) {
                if (e.currentIndex != this.current) {
                    this.current = e.currentIndex;
					// 修改页面标题
					uni.setNavigationBarTitle({title: this.items[e.currentIndex]});
                }
            }
        }
    }
</script>

<style lang="scss" scoped>
.home_tab {
    .home_tab_title {
        position: relative;
        .title_inner {
            width: 60%;
            margin: 0 auto;
        }
        .icon-search {
            position: absolute;
            top: 50%;
            right: 40rpx;
            font-size: 36rpx;
            transform: translateY(-50%);
        }
    }
}

</style>