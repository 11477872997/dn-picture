<template>
  <view class="AlbumDetails">
    <!-- 标题区域 -->
    <view class="Detail">
      <view class="TitleImg">
        <image :src="album.cover" mode="widthFix" />
      </view>

      <view class="TitleInfo">
        <view class="title">{{ album.name }}</view>
        <view class="btn">关注专辑</view>
      </view>
    </view>

    <!-- 人物介绍 -->
    <view class="album_author">
      <view class="character_introduction">
        <image :src="album.user.avatar" mode="widthFix" />

        <view class="album_author_desc">{{ album.user.name }}</view>
      </view>

      <view class="author_name">
        <text>{{ album.desc }}</text>
      </view>
    </view>

    <!-- 专辑列表 -->
    <view class="album_list">
      <view class="album_item" v-for="(item,index) in wallpaper" :key="item.id">
      <go-detail :list='wallpaper' :index=index>
        <image :src="item.thumb+item.rule.replace('$<Height>',360)" mode="aspectFill" />
      </go-detail>
      </view>
    </view>
  </view>
</template>

<script>
import GoDetail from "@/components/goDetail";
export default {
  components:{
    GoDetail
  },
  data() {
    return {
      parmas: {
        limit: 30,
        order: "new",
        skip: 0,
        first: 1, //代表第一次请求
      },
      id: -1, //index
      album: {}, //专辑介绍
      wallpaper: [], //图片
      hasMore:true,
    };
  },
  onLoad(options) {
    this.id = options.id;
    // this.id = "5d5f8e45e7bce75ae7fb8278";
    this.getList();
    console.log(123321);
  },
  onReachBottom(){ //触底函数

    if(this.hasMore){
      this.parmas.first = 0;
      this.parmas.skip += this.parmas.limit; //
      this.getList();
    }else{
      uni.showToast({
        title:'没有数据',
        icon:'none'
      })
    }
  
  },



  methods: {

  getList() {
    uni.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.parmas,
      })
      .then((result) => {


        if(Object.keys(this.album).length == 0){
          this.album = result[1].data.res.album;
        }

        if(result[1].data.res.wallpaper.length == 0){
          this.hasMore = false;
          uni.showToast({
            title:'没有数据了',
            icon:'none'
          })
          return;

        }

        this.wallpaper = [...this.wallpaper,...result[1].data.res.wallpaper];

      });
  },
},
  
};
</script>

<style lang="scss" scoped>
.Detail {
  position: relative;
  .TitleImg {
    image {
    }
  }

  .TitleInfo {
    position: absolute;
    bottom: 0;
    left: 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    padding: 20rpx 30rpx;
    font-size: 36rpx;
    color: #fff;
    .title {
    }

    .btn {
      font-size: 30rpx;
      background-color: $color;
      padding: 10rpx 20rpx;
      border-radius: 15rpx;
    }
  }
}

.album_author {
  color: #000;
  padding: 0 15rpx;
  .character_introduction {
    display: flex;
    padding: 10rpx 0;
    align-items: center;
    font-weight: 700;
    image {
      width: 50rpx;
    }

    .album_author_desc {
      padding-left: 15rpx;
    }
  }

  .author_name {
    text {
      color: rgb(126, 121, 121);
    }
  }
}

.album_list {
  display: flex;
  flex-wrap: wrap;
  padding-top:30rpx;
  .album_item {
    width: 33.33%;
    border: 1rpx solid #fff;
    image {
      height: 160rpx;
    }
  }
}
</style>