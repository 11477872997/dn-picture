<template>
  <viwe>

    <view class="category_item" v-if="caregory.length>0">

      <navigator 
      class="category_list"
      v-for="item in caregory"
      :key="item.id"
      :url="`/pages/home/home-category/imgCategory/index?id=${item.id}`">
        <view class="cover">
          <image :src=item.cover mode="aspectFill" />
        </view>
        <view class="name">{{item.name}}</view>
      </navigator>

      0
    </view>
    
  </viwe>
</template>

<script>
export default {

  data(){
    return{
      caregory:[],
    }
  },
  mounted(){
    this.getList();
  },
  methods:{

    getList(){ //获取图片数据

      uni.request({
        url:"http://157.122.54.189:9088/image/v1/vertical/category"
      }).then(result => {
        this.caregory = result[1].data.res.category;
        console.log(this.caregory);
      })
    }

  },

} 
</script>

<style lang='scss' scoped>


.category_item {
    display: flex;
    flex-wrap: wrap;
  .category_list {
    position: relative;
      width: 33.33%;
      border: 5rpx solid #fff;
    .cover{
      image {
      height: 240rpx;
    }
    }

    .name {
      position: absolute;
      width: 100%;
      height: 50rpx;
      left: 0;
      bottom: 0;
      color: #fff;
      // css3 渐变
      background-image: linear-gradient(to right top,rgba(0,0,0,.2),rgba(0,0,0,0));
      font-size: 40rpx;
      display: flex;
      align-items: center;
      padding-left: 20rpx;
    }
  }
}


</style>