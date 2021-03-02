<template>
  <view class="ImgDetails">

    <view class="DetailsTitle">

      <!-- 人物信息 -->
      <view class="UserInfo">
        <view class="Useravatar">
          <image :src="imgDetail.user.avatar" mode="widthFix" />
        </view>
        <view class="UserItem">
          <view class="UserName">{{ imgDetail.user.name }}</view>
          <viwe class="UserDistanceTime">{{ imgDetail.cnTime }}</viwe>
        </view>
      </view>
      <!-- 主图 -->
      <view class="BigPicture">
          <SwiperAction @swiperAction="handleswiperAction">
        <image 
        :src=imgDetail.thumb
         />
          </SwiperAction>
      </view>
      <!-- 互动 -->
      <view class="Interactive">

        <view class="Like">
          <text class="iconfont icondianzan">{{imgDetail.rank}}</text>
        </view>
        <view class="favorites">
          <text class="iconfont iconshoucang">收藏</text>
        </view>
        
      </view>

    </view>

    <!-- 热门 -->
    <view
      class="comment hot"
      v-if="hot.length"
    >
      <view class="comment_title">
        <text class="iconfont iconhot1"></text>
        <text class="comment_text">最热评论</text>
      </view>
      <view class="comment_list">
        <view
          class="comment_item"
          v-for="item in hot"
          :key="item.id"
        >
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户头像 -->
            <view class="user_icon">
              <image
                mode="widthFix"
                :src="item.user.avatar"
              ></image>
            </view>
            <!-- 用户名称 -->
            <view class="user_name">
              <view class="user_nickname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
                v-for=" item2 in  item.user.title"
                :key="item2.icon"
                :src="item2.icon"
              ></image>
            </view>
          </view>
          <!-- 评论数据 -->
          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan">{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>
    <!-- 最新评论 comment new -->
    <view
      class="comment new"
      v-if="comment.length"
    >
      <view class="comment_title">
        <text class="iconfont iconpinglun"></text>
        <text class="comment_text">最新评论</text>
      </view>
      <view class="comment_list">
        <view
          class="comment_item"
          v-for="item in comment"
          :key="item.id"
        >
          <!-- 用户信息 -->
          <view class="comment_user">
            <!-- 用户头像 -->
            <view class="user_icon">
              <image
                mode="widthFix"
                :src="item.user.avatar"
              ></image>
            </view>
            <!-- 用户名称 -->
            <view class="user_name">
              <view class="user_nickname">{{item.user.name}}</view>
              <view class="user_time">{{item.cnTime}}</view>
            </view>
            <!-- 用户徽章 -->
            <view class="user_badge">
              <image
                v-for=" item2 in  item.user.title"
                :key="item2.icon"
                :src="item2.icon"
              ></image>
            </view>
          </view>
          <!-- 评论数据 -->
          <view class="comment_desc">
            <view class="comment_content">{{item.content}}</view>
            <view class="comment_like">
              <text class="iconfont icondianzan">{{item.size}}</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <!-- 下载按钮 -->
    <view class="download" @click=handleDownload>
      <view class="btn">下载图片</view>
    </view>


  </view>
</template>

<script>
import moment from 'moment';
import swiperAction from "@/components/swiperAction";
//设置中文
moment.locale("zh-cn");
export default {
  /*
组件封装目的
创建一个全局共享的图片详情组件
1.创建一个插槽组件
2.嵌套在需要点击加载图片详情页面的外侧
3.循环的时候接受图片数据和index并储存在全局变量 
4.然后在图片 点击的时候实现跳转
 */
components:{swiperAction},
  data() {
    return {
      imgDetail: {}, //图片数据
      imgIndex: Number, //图片索引
      comment:[], //最新评论
      hot:[], //热门评论
    };
  },
  onLoad() {
    const {imgIndex} = getApp().globalData;
    this.imgIndex = imgIndex;
    this.getData();
  },
  methods: {
    getData() { //获取标题数据
      const { imgList } = getApp().globalData; //接受图片数据
      this.imgDetail = imgList[this.imgIndex]; //页面数据
      this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow(); //距离时间
      this.getContent(this.imgDetail.id);  //索引id
    },

    getContent(id){ //评论数据

      uni.request({
        url:`http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
      }).then(result => {
        // console.log(result[1].data.res);
        result[1].data.res.comment.forEach(v => (v.cnTime = moment(v.atime * 1000).fromNow()));
        result[1].data.res.hot.forEach( v=> {(v.cnTime = moment(v.atime * 1000).fromNow())})

        this.hot = result[1].data.res.hot; //最热
        this.comment = result[1].data.res.comment; //最新

      }
      )
      
    },
    handleswiperAction(e){

      const {imgList} = getApp().globalData;

      if(e.direction === 'left' && this.imgIndex < imgList.length - 1){ //说明往左滑动
        this.imgIndex++;
        this.getData();
      }else if(e.direction === 'right' && this.imgIndex > 0 ){ //右滑
        this.imgIndex--;
        this.getData()
      }else{
        uni.showToast({
          title:'没有数据了',
          icon:'none'
        })
      }
     
    },
        // 点击下载图片 async await promise
    async handleDownload() {
      // uni.downloadFile
      // uni.saveImageToPhotosAlbum

      await uni.showLoading({
        title:"下载中"
      })

      // 1 将远程文件下载到小程序的内存中 tempFilePath
      const result1 = await uni.downloadFile({ url: this.imgDetail.img });
      const { tempFilePath } = result1[1];

      //  2 将小程序内存中的临时文件下载到本地上
      const result2 = await uni.saveImageToPhotosAlbum({ filePath: tempFilePath });
      // console.log(result2);
      // 3 提示用户下载成功
      // console.log("下载城市");

      uni.hideLoading();
      await uni.showToast({
        title:"下载成功"
        // icon
      })
    }
  },
};
</script>

<style lang="scss" scoped>

/* 标题介绍 */
.DetailsTitle {

  .UserInfo {
    display: flex;
    padding: 30rpx;
    // height: 70rpx;
    .Useravatar {
      padding: 0 20rpx;
      image {
      width: 88rpx;
      border-radius: 50%;
      }
    }

    .UserItem {
      padding-left: 20rpx;
      align-items: center;
      .UserName {
        color: #000;
        font-weight: 600;
      }

      .UserDistanceTime {
         color: #ccc;
         font-size: 24rpx;
         padding: 10rpx 0;
      }
    }
  }


  .Interactive {
    display: flex;
    border-bottom: 1px solid rgb(119, 119, 119);
    padding: 15rpx 0;
  .Like {
    flex: 1;
    display: flex;
    justify-content: center;    
    align-items: center;
    text {

    }
  }

  .favorites {
        flex: 1;
    display: flex;
    justify-content: center;    
    align-items: center;
    text {

    }
  }
}


}

.comment {
  .comment_title {
    padding: 15rpx;
    .iconfont {
      color: red;
      font-size: 40rpx;
    }

    .comment_text {
      font-weight: 600;
      font-size: 28rpx;
      color: #666;
      margin-left: 10rpx;
    }
  }

  .comment_list {
    .comment_item {
      border-bottom: 15rpx solid #eee;
      // 用户信息
      .comment_user {
        display: flex;
        padding: 20rpx 0;
        .user_icon {
          width: 15%;
          display: flex;
          justify-content: center;
          align-items: center;
          image {
            width: 90%;
          }
        }

        .user_name {
          flex: 1;
          .user_nickname {
            color: #777;
          }

          .user_time {
            color: #ccc;
            font-size: 24rpx;
            padding: 5rpx;
          }
        }

        .user_badge {
          image {
            width: 40rpx;
            height: 40rpx;
            display: inline-block;
          }
        }
      }
      // 评论数据
      .comment_desc {
        display: flex;
        padding: 30rpx 0;
        .comment_content {
          flex: 1;
          padding-left: 15%;
          color: #000;
        }

        .comment_like {
          text-align: right;
          .icondianzan {
          }
        }
      }
    }
  }
}

// 最新评论
.new {
  .iconpinglun {
    color: aqua !important;
  }
}
//按钮
.download{
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20rpx;
  .btn{
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: rgb(253, 2, 128);
    color: #fff;
    width: 85%;
    padding:  30rpx 0;
    border-radius: 10rpx;
    font-size: 60rpx;
  }
}

</style>



