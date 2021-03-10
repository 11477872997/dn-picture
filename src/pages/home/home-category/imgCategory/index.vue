<template>
  <view class="ategory_box">
    <view class="category_tab">
      <view class="category_tab_title">
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
    <scroll-view 
    @scrolltolower=handleScrolltolower
    enable-flex scroll-y  
    class="category_content">
      <view class="cate_item" v-for="(item,index) in vertical" :key="item.id">
        <GoDetail :list=vertical :index=index :params=params :Imgid=Imgid >
        <image :src="item.thumb" mode="widthFix" />
        </GoDetail>
      </view>
    </scroll-view>
  </view>
</template>

<script>
import { uniSegmentedControl } from "@dcloudio/uni-ui";
import goDetail from "@/components/goDetail"
export default {
  components: {
    uniSegmentedControl,
    goDetail
  },
  data() {
    return {
      Imgid: "",
      items: [
        { title: "最新", order: "new" },
        { title: "热门", order: "hot" },
      ],
      current: 0,
      params: {
        limit: 10,
        skip: 0,
        order: "new",
      },
      vertical: [],
      hasMore:true,
    };
  },
  onLoad(id) {
    this.Imgid = id.id;
    // this.id = "4e4d610cdf714d2966000000";
    this.getList();
    // uni.showLoading({
    //   title: '加载中',
    // })

    // console.log(id);
  },
  methods: {

    getList() { //请求
      this.request({
          url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.Imgid}/vertical`,
          data: this.params,
        })
        .then((result) => {

          if(result.data.res.vertical.length === 0){ //没有数据了
            uni.showToast({
              title:'没有数据了',
               icon:'none'
            })
            this.hasMore = false;
            return;
          }

          console.log(result);

          this.vertical = [...this.vertical, ...result.data.res.vertical];
          // console.log(this.vertical);
        });
    },
    onClickItem(e) { //切换按钮
      if (this.current !== e.currentIndex) {
        this.current = e.currentIndex;
      } else {
        return;
      }
      this.params.order = this.items[e.currentIndex].order;
      this.params.skip = 0;
      this.vertical = [];
      this.getList();
    },
    handleScrolltolower(){ //触底
    
      if(this.hasMore){
        this.params.skip += this.params.limit;
        this.getList();
      }else{
        uni.showToast({
          title:'没有数据了',
          icon:'none'
        })
      }
    
    }

  },
};
</script>

<style lang="scss" scoped>
.ategory_box {
  .category_tab {
    position: relative;
    .category_tab_title {
      .title_inner {
        width: 60%;
        margin: 0 auto;
      }
    }
  }

  .category_content {
    display: flex;
    flex-wrap: wrap;
    height: calc(100vh - 36px);

    .cate_item {
      width: 33.33%;
      border: 2px solid #fff;
      image {
      }
    }
  }
}
</style>