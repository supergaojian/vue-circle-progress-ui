<template>
  <div class="circle-progress" :style="{ width: (size || 40) + 'px', height: (size || 40) + 'px' }">
    <div class="content-box" :class="[ content.type == 'img' && 'img-box' ]" :style="{ backgroundColor: mainColor }">
      <img class="avatar" :src="content.url"  v-if="content.type == 'img'"/>
      <i class="fa" :class="iconClass"  v-if="content.type == 'icon'"></i>
      <span class="text" v-if="content.type == 'text'">{{ content.text }}</span>
    </div>
    <div class="circle-box" :style="{ backgroundColor: bgColor }">
      <div class="left">
        <div class="circle" :class="[ !progress && 'reset' ]" :style="{ transform: 'rotateZ(' + leftProgress + 'deg)', backgroundColor: mainColor, borderRadius: leftRadius }"></div>
      </div>
      <div class="right">
        <div class="circle" :class="[ !progress && 'reset' ]" :style="{ transform: 'rotateZ(' + rightProgress + 'deg)', backgroundColor: mainColor, borderRadius: rightRadius }"></div>
      </div>
    </div>
  </div>
</template>

<script>
  /**
   * 圆形进度条
   *
   * content: 内部显示内容 Object required
   *      eq: { type: 'img', url: '' }
   *          { type: 'text', text: '' }
   *          { type: 'icon', before: '', running: '' } => （基于font awesome，实参为class类）
   *
   * type: 展示形式 String required
   *      'static' => 静态展示由外部控制展示
   *      'auto' => 动态展示当传入duration不为0时自动进行倒计时展示
   *
   * progress: 进度条 Int（0 - 100，静态展示中必须传入）
   * duration: 运行时长 Int（动态展示中必须传入）
   *
   * size: 图形大小 Int （单位为px，默认为40px）
   * color: 背景颜色 String （单位为十六进制，默认为#0096ff）
   *
   * 注意：在动态展示形式中duration变化会触发重新倒计时
   *
   */
  export default {
    props: ['content', 'type', 'duration', 'progress', 'size', 'color'],

    data () {
      return {
        iconClass: this.content.before || 'fa-play', // icon情况下显示的状态
        autoProgress: 0, // 动态展示使用进度
        interval: null
      }
    },

    computed: {
      isAuto () {
        return this.type == 'auto';
      },

      current () {
        return this.isAuto ? this.autoProgress : this.progress;
      },

      rightProgress () {
        const { current } = this;
        if (current > 50) return 0;
        return (-180 + current * 3.6);
      },

      leftProgress () {
        const { current, rightProgress } = this;
        if (current < 50 || rightProgress) return -180;
        if (current >= 100) return 0;
        return (-180 + (current - 50) * 3.6);
      },

      leftRadius () {
        const { size } = this;
        return (size / 2) + 'px 0 0 ' + (size / 2) + 'px';
      },

      rightRadius () {
        const { size } = this;
        return '0 ' + (size / 2) + 'px ' + (size / 2) + 'px 0';
      },

      mainColor () {
        const { color } = this;
        if (!color) return 'rgba(0,150,255,1)';
        let rgb = this.formatColor(color);
        return 'rgba(' + rgb + ',1)';
      },

      bgColor () {
        const { color } = this;
        if (!color) return 'rgba(0,150,255,0.3)';
        let rgb = this.formatColor(color);
        return 'rgba(' + rgb + ',0.3)';
      }
    },

    watch: {
      duration (val) {
        if (!this.isAuto) return;

        const { content } = this;
        this.autoProgress = 0;
        content.type == 'icon' && (this.iconClass = content.before || 'fa-play');
        this.interval && clearInterval(this.interval);
        if (val > 0) {
          this.setProgress()
        }
      }
    },

    methods: {
      setProgress () {
        if (!this.isAuto) return;

        const { duration, content } = this;
        let start = 0;
        this.iconClass = content.running || 'fa-stop';
        this.interval = setInterval(() => {
          start += 1;
          this.autoProgress = Math.ceil(start * 10 / duration);
          if (this.autoProgress >= 101) {
            content.type == 'icon' && (this.iconClass = content.before || 'fa-play');
            clearInterval(this.interval)
          }
        }, 100)
      },

      formatColor (color) {
        color = color.replace(/#/g, '');
        let rgb = [];
        rgb.push(parseInt(color.substring(0, 2), 16));
        rgb.push(parseInt(color.substring(2, 4), 16));
        rgb.push(parseInt(color.substring(4, 6), 16));
        return rgb.join(',');
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
      &.img-box {
        background-color: #FFFFFF !important;
      }
      .fa,
      .text {
        margin: auto;
        font-size: 16px;
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
          transition: all 0.2s linear;
          -webkit-transition: all 0.2s linear;
          &.reset {
            transition: all 0s linear !important;
            -webkit-transition: all 0s linear !important;
          }
        }
      }
      .left {
        .circle {
          transform: rotateZ(-180deg);
          -webkit-transform: rotateZ(-180deg);
          transform-origin: 100% 50%;
          -webkit-transform-origin: 100% 50%;
        }
      }
      .right {
        .circle {
          transform: rotateZ(-180deg);
          -webkit-transform: rotateZ(-180deg);
          transform-origin: 0 50%;
          -webkit-transform-origin: 0 50%;
          border-radius: 0 100% 100% 0;
        }
      }
    }
  }
</style>