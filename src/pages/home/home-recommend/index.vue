<template>
  <scroll-view
    @scrolltolower="handleToLower"
    class="recommend_view"
    scroll-y
    v-if="recommend.length > 0"
  >
    <!-- 推荐开始 -->
    <view class="recommend_wrap">
      <view class="recommend_item" v-for="item in recommend" :key="item.id">
        <image mode="widthFix" :src="item.thumb" />
      </view>
    </view>
    <!-- 推荐结束 -->

    <!-- 月份开始 -->
    <view class="monthes_wrap">
      <!-- 月份标题 -->
      <view class="monthes_title">
        <view class="monthes_title_info">
          <text> {{monthes.DD}}/{{monthes.MM }}月</text> {{ monthes.title.replace('。',' ') }}
        </view>
        <view class="monthes_text">更多 > </view>
      </view>

      <!-- 月份主体 -->
      <view class="monthes_content">
        <view class="monthes_item" v-for="item in monthes.items" :key="item.id">
          <image
            mode="aspectFill"
            :src="item.thumb + item.rule.replace('$<Height>', 360)"
          />
        </view>
      </view>
    </view>
    <!-- 月份结束 -->
    <!-- 热门开始 -->

    <view class="hot_main">
      <!-- 热门标题 -->
      <view class="hot_title_content">
        <view class="title_box"></view>
        <text class="titletext">热门</text>
      </view>
      <!-- 热门主体 -->
      <view class="hot_content">
        <view class="hot_item" v-for="item in hots" :key="item.id">
          <image mode="aspectFill" :src="item.thumb" />
        </view>
      </view>
    </view>

    <!-- 热门结束 -->
  </scroll-view>
</template>

<script>
import moment from "moment";
export default {
  data() {
    return {
      recommend: [],
      monthes: {},
      hots: [],
      params: {
        //请求参数
        limit: 30,
        order: "hot",
        skip: 0,
      },
      hasMore: true,
    };
  },
  methods: {
    getList() {
      //获取数据
      this.request({
        url: `http://157.122.54.189:9088/image/v3/homepage/vertical`,
        data: this.params,
      }).then((result) => {
        if (result.data.res.vertical.length === 0) {
          //说明没有数据了
          this.hasMore = false;
          uni.showToast({
            title: "没有数据了",
            icon: "none",
          });
        }

        if (this.recommend.length === 0) {
          //条件通过说明第一次请求,以免造成性能浪费

          this.recommend = result.data.res.homepage[1].items; //推荐数据
          this.monthes = result.data.res.homepage[2]; //月份

          this.monthes.DD = moment(this.monthes.stime).format("DD");
          this.monthes.MM = moment(this.monthes.stime).format("MM");
          // console.log(this.monthes);
        }

        this.hots = [...this.hots, ...result.data.res.vertical]; //热门
      });
    },

    handleToLower() {
      //触底函数

      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有数据了",
          icon: "none",
        });
      }
    },
  },
  onLoad() {},
  mounted() {
    this.getList();

    uni.setNavigationBarTitle({title:'推荐'});
  },
};
</script>

<style lang='scss' scoped>
/* 推荐 */
.recommend_view {
  height: calc(100vh - 36px);
}
.recommend_wrap {
  display: flex;
  flex-wrap: wrap;
  .recommend_item {
    width: 50%;
    border: 3rpx solid #fff;
  }
}
/* 月份 */
.monthes_wrap {
  .monthes_title {
    display: flex;
    justify-content: space-between;
    padding: 20rpx;
    color: $color;
    font-weight: 600;
    .monthes_title_info {
      display: flex;
      align-items: center;
      font-size: 30rpx;
      text {
        font-size: 36rpx;
        padding-right: 10rpx;
      }
    }

    .monthes_text {
      font-size: 34rpx;
    }
  }

  .monthes_content {
    display: flex;
    flex-wrap: wrap;
    .monthes_item {
      width: 33.33%;
      border: 2rpx solid #fff;
      image {
      }
    }
  }
}
/* 热门 */
.hot_main {
  .hot_title_content {
    display: flex;
    align-items: center;
    padding: 10rpx 20rpx;
    .title_box {
      width: 30rpx;
      height: 40rpx;
      background-color: $color;
    }

    text.titletext {
      font-size: 36rpx;
      padding-left: 15rpx;
      font-weight: 600;
    }
  }

  .hot_content {
    display: flex;
    flex-wrap: wrap;
    .hot_item {
      width: 33.33%;
      border: 1px solid #fff;
    }
  }
}
</style>