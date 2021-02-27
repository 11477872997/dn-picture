<template>
  <view>
    <view class="home_tab">
      <view class="home_tab_title">
        <view class="title_inner">
          <uni-segmented-control
            :current="current"
            :values="items.map((v) => v.title)"
            @clickItem="onClickItem"
            styleType="text"
            activeColor="#d4237a"
          ></uni-segmented-control>
        </view>
      </view>
    </view>
    <view class="home_content">
      <view v-if="current === 0"> <HomeRecommend /> </view>
      <view v-if="current === 1"> <HomeCategory /> </view>
      <view v-if="current === 2"> <HomeNew /> </view>
      <view v-if="current === 3"> <HomeAlbum /> </view>
    </view>
  </view>
</template>

<script>
import HomeCategory from "./home-category/index";
import HomeRecommend from "./home-recommend/index";
import HomeNew from "./home-new/index";
import HomeAlbum from "./home-album/index";
import { uniSegmentedControl } from "@dcloudio/uni-ui";

export default {
  components: {
    HomeAlbum, //专辑
    HomeCategory, //分类
    HomeNew, //最新
    HomeRecommend, //推荐
    uniSegmentedControl,
  },
  data() {
    return {
      current: 0,
      items: [
        { title: "推荐" },
        { title: "分类" },
        { title: "最新" },
        { title: "专辑" },
      ],
    };
  },
  methods: {
    onClickItem(e) {
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      }
    },
  },

  onLoad() {
    //   wx.request({
    //     url: 'http://157.122.54.189:9088/image/v1/vertical/category',
    //     success(res){
    //         console.log(res);
    //     }
    //   })

    uni
      .request({
        url: "http://157.122.54.189:9088/image/v1/vertical/category",
      })
      .then((res) => {
        console.log(res);
      });
  },
};
</script>

<style lang="scss">
</style>