<template>
  <div class="circle-progress" :style="{ width: (size || 40) + 'px', height: (size || 40) + 'px' }">
    <div class="content-box" :class="[ imgUrl && 'img-box' ]">
      <img class="avatar" :src="imgUrl"  v-if="imgUrl"/>
      <i class="fa" :class="icon" v-else></i>
    </div>
    <div class="circle-box">
      <div class="left">
        <div class="circle" :style="{ transform: 'rotateZ(' + leftProgress + 'deg)' }"></div>
      </div>
      <div class="right">
        <div class="circle" :style="{ transform: 'rotateZ(' + rightProgress + 'deg)' }"></div>
      </div>
    </div>
  </div>
</template>

<script>
  /**
   * 圆形进度条UI版
   *
   * 优先级：imgUrl > icon
   * 注意：旋转时长为0.1s
   *
   * imgUrl: 图片路径 String
   * icon: 图标的class类（基于font awesome）String
   * progress: 进度条 Int
   * size: 图形大小(Px) Int
   *
   */
  export default {
    props: ['imgUrl', 'icon', 'progress', 'size'],

    computed: {
      rightProgress () {
        const { progress } = this;
        if (progress > 50) return 0;
        return (-180 + progress * 3.6);
      },

      leftProgress () {
        const { progress, rightProgress } = this;
        if (progress < 50 || rightProgress) return -180;
        if (progress >= 100) return 0;
        return (-180 + (progress - 50) * 3.6);
      }
    }
  }
</script>

<style lang='scss' rel='stylesheet/scss' scoped>
  .circle-progress {
    position: relative;
    .content-box {
      box-sizing: border-box;
      z-index: 20;
      position: relative;
      display: -webkit-flex;
      display: flex;
      top: 3px;
      left: 3px;
      width: calc(100% - 6px);
      height: calc(100% - 6px);
      color: #FFFFFF;
      border-radius: 50%;
      border: 2px solid #FFFFFF;
      background-color: #0096ff;
      &.img-box {
        background-color: #FFFFFF;
      }
      .fa {
        margin: auto;
        font-size: 18px;
      }
      .avatar {
        z-index: 30;
        position: relative;
        display: block;
        width: 100%;
        height: 100%;
        border-radius: 50%;
      }
    }
    .circle-box {
      z-index: 10;
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      border-radius: 50%;
      background-color: rgba(0, 150, 255, 0.3);
      .right,
      .left {
        position: relative;
        display: block;
        float: left;
        width: 50%;
        height: 100%;
        overflow: hidden;
        .circle {
          position: relative;
          width: 100%;
          height: 100%;
          background-color: #0096ff;
          transition: all 0.2s linear;
          -webkit-transition: all 0.2s linear;
        }
      }
      .left {
        .circle {
          transform: rotateZ(-180deg);
          -webkit-transform: rotateZ(-180deg);
          transform-origin: 100% 50%;
          -webkit-transform-origin: 100% 50%;
          border-radius: 20px 0 0 20px;
        }
      }
      .right {
        .circle {
          transform: rotateZ(-180deg);
          -webkit-transform: rotateZ(-180deg);
          transform-origin: 0 50%;
          -webkit-transform-origin: 0 50%;
          border-radius: 0 20px 20px 0;
        }
      }
    }
  }
</style>