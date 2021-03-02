<template>
  <view 
  @touchstart="handleTouchStart" 
  @touchend="handleTouchEnd">

    <slot></slot>


    </view>
</template>

<script>
export default {
  data() {
    return {
      startTime:0,
      startX:0,
      startY:0

    };
  },
  methods: {
    handleTouchStart(e) {
        this.startTime = Date.now();
        this.startX = e.changedTouches[0].clientX;
        this.startY = e.changedTouches[0].clientY;

    },
    handleTouchEnd(e) {


        let endX = e.changedTouches[0].clientX;
        let endY = e.changedTouches[0].clientY;

        if(Date.now() - this.startTime > 2000){ //判断手指停留时间
          console.log('不合格');
          return;
        }
        
        let direction = '';
        
      // 先判断用户滑动的距离 是否合法 合法：判断滑动的方向  注意 距离要加上绝对值
      if (Math.abs(endX - this.startX) > 10  && Math.abs(endY - this.startY) < 100) {
        // 滑动方向！！！
        direction = endX - this.startX > 0 ? "right" : "left"; //大于零说明是往右边滑动
      } else {
        return;
      }

        // console.log(direction);
        this.$emit('swiperAction',{direction})



      
    },
  },
};
</script>

<style>

</style>