<template>
  <scroll-view scroll-y @scrolltolower="handleToLower" class="AlbumBody">
    <!-- 轮播图 -->
    <view class="AlbumSwiper">
      <swiper autoplay indicator-dots circular>
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb" />
        </swiper-item>
      </swiper>
    </view>

    <!-- 专辑 -->
    <view class="AlbumList">
      <navigator 
      class="AlbumItem" 
      v-for="item in album" 
      :key="item.id"
      :url="`/pages/home/home-album/AlbumDetails/index?id=${item.id}`"
      >
        <view class="AlbumImg">
          <image :src="item.cover" mode="aspectFill" />
        </view>

        <view class="ItemInfo">
          <view class="AlbumName">{{ item.name }}</view>
          <view class="AlbumText">{{ item.desc }}</view>

          <view class="AlbumBtn">
            <view class="attention">关注</view>
          </view>
        </view>
      </navigator>
    </view>
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: "new",
        skip: 0,
      },
      banner: [], //轮播图
      album: [], //专辑
      hasMore: true,
    };
  },
  methods: {
    getList() {
      //获取请求

      this.request({
        url: "http://157.122.54.189:9088/image/v1/wallpaper/album",
        data: this.params,
      }).then((result) => {
        
        if (result.data.res.album.length === 0) {
          //数据了
          this.hasMore = false;
          uni.showToast({
            title: "没有数据了",
            icon: "none",
          });
          return;
        }
        if (this.banner.length === 0) {//第一次访问
          this.banner = result.data.res.banner;
        }

        this.album = [...this.album, ...result.data.res.album];

        // console.log(this.params.skip);
      });
    },
    handleToLower() {
      //触底

      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: "没有数据",
          icon: "none",
        });
      }
    },
  },

  mounted() {
    this.getList();
    uni.setNavigationBarTitle({ title: "专辑" });
  },
};
</script>

<style lang='scss' scoped>
.AlbumBody {
  height: calc(100vh - 36px);
}

/* 轮播图 */
.AlbumSwiper {
  swiper {
    // 750rpx 326.0869565217392
    // height: calc(750rpx / 2.3 );
    height: 326.1rpx;
    image {
      height: 100%;
    }
  }
}
/* 专辑列表 */
.AlbumList {
  padding: 10rpx;
  .AlbumItem {
    display: flex;
    border: 1px solid #fff;
    // padding:10rpx 0;
    .AlbumImg {
      flex: 1;
      padding: 10rpx 0;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .ItemInfo {
      flex: 2;
      padding: 0 10rpx 0 0;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      .AlbumName {
        font-weight: 600;
        font-size: 34rpx;
        color: #000;
      }

      .AlbumText {
        // padding: 10rpx 0;
        font-size: 30rpx;
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .AlbumBtn {
        padding: 10rpx;
        display: flex;
        justify-content: flex-end;
        .attention {
          color: $color;
          border: 1rpx solid $color;
          padding: 10rpx;
        }
      }
    }
  }
}
</style>