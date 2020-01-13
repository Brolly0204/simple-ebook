<template>
  <div class="menu-bar">
    <transition name="slide-up">
      <div
        v-show="ifTitleAndMenuShow"
        class="menu-wrapper"
        :class="{'hide-box-shadow': ifSettingShow || !ifTitleAndMenuShow}"
      >
        <div
          class="icon-wrapper"
          @click="showSetting(3)"
        >
          <span class="icon icon-menu"></span>
        </div>
        <div
          class="icon-wrapper"
          @click="showSetting(2)"
        >
          <span class="icon icon-progress"></span>
        </div>
        <div
          class="icon-wrapper"
          @click="showSetting(1)"
        >
          <span class="icon icon-bright"></span>
        </div>
        <div
          class="icon-wrapper"
          @click="showSetting(0)"
        >
          <span class="icon icon-a">A</span>
        </div>
      </div>

    </transition>
    <transition name="slide-up">
      <div
        v-show="ifSettingShow"
        class="setting-wrapper"
      >
        <div
          v-if="showTag === 0"
          class="setting-font-size"
        >
          <div
            class="preview"
            :style="{
            fontSize: fontSizeList[0].fontSize + 'px'
          }"
          >A</div>
          <div class="select">
            <div
              class="select-wrapper"
              v-for="(item, index) of fontSizeList"
              :key="index"
              @click="setFontSize(item.fontSize)"
            >
              <div class="line"></div>
              <div class="point-wrapper">
                <div
                  class="point"
                  v-show="defaultFontSize === item.fontSize"
                >
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div
            class="preview"
            :style="{
            fontSize: fontSizeList[fontSizeList.length - 1].fontSize + 'px'
          }"
          >A</div>
        </div>

        <div
          class="setting-theme"
          v-else-if="showTag === 1"
        >
          <div
            class="setting-theme-item"
            v-for="(theme, index) of themeList"
            :key="index"
            :class="{selected: index === defaultTheme }"
            @click="setTheme(index)"
          >
            <div
              class="preview"
              :style="{ background: theme.style.body.background}"
            ></div>
            <div class="text">{{theme.name}}</div>
          </div>
        </div>
        <div
          class="setting-progress"
          v-else-if="showTag === 2"
        >
          <div class="progress-wrapper">
            <input
              ref="progress"
              type="range"
              class="progress"
              max="100"
              min="0"
              step="1"
              v-model="progress"
              :disabled="!bookAvaliable"
              @change.stop="onProgressChange($event.target.value)"
              @input.stop="onProgressInput($event.target.value)"
            >
          </div>
          <div class="text-wrapper">
            <span>{{bookAvaliable ? progress + '%' : '加载中...'}}</span>
          </div>
        </div>
      </div>
    </transition>
    <content-view
      :ifShowContent="ifShowContent"
      v-show="ifShowContent"
      :navigation="navigation"
      :bookAvailable="bookAvailable"
      @jumpTo="jumpTo"
    ></content-view>
    <transition name="fade">
      <div
        class="content-mask"
        v-show="ifShowContent"
        @click="hideContent"
      ></div>
    </transition>
  </div>
</template>

<script>
import ContentView from '@/components/Content'

export default {
  components: {
    ContentView
  },
  props: {
    ifTitleAndMenuShow: {
      type: Boolean
    },
    fontSizeList: {
      type: Array,
      default: () => []
    },
    defaultFontSize: {
      type: Number
    },
    themeList: {
      type: Array,
      default: () => []
    },
    defaultTheme: {
      type: Number,
      default: 0
    },
    bookAvailable: {
      type: Boolean,
      default: false
    },
    navigation: Object
  },
  watch: {
    ifTitleAndMenuShow(newVal) {
      if (!newVal) {
        this.hideSetting()
      }
    }
  },
  data() {
    return {
      showTag: 0,
      progress: 0,
      ifSettingShow: false,
      ifShowContent: false
    }
  },
  methods: {
    hideContent() {
      this.ifShowContent = false
    },
    // 跳转方法，调用父组件方法
    jumpTo(target) {
      this.$emit('jumpTo', target)
    },
    onProgressChange(progress) {
      this.$emit('onProgressChange', progress)
      console.log('value', progress)
    },
    onProgressInput(progress) {
      console.log('value', progress)
      this.$refs.progress.style.backgroundSize = `${progress}% 100%`
    },
    setTheme(index) {
      this.$emit('setTheme', index)
    },
    setFontSize(fontSize) {
      this.$emit('setFontSize', fontSize)
    },
    showSetting(tag) {
      if (tag === 3) {
        this.ifSettingShow = false
        this.ifShowContent = true
      } else {
        this.ifSettingShow = true
      }
      this.showTag = tag
    },
    // 显示设置面板
    hideSetting() {
      this.ifSettingShow = false
    }
  }
}
</script>

<style lang="scss" scoped>
@import '~@/assets/styles/mixin.scss';

.menu-bar {
  .slide-up-enter-active,
  .slide-up-leave-active {
    transition: all 0.3s linear;
  }
  .slide-up-enter,
  .slide-up-leave-to {
    transform: translate3d(0, px2rem(108), 0);
  }
  .menu-wrapper {
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    height: px2rem(45);
    display: flex;
    background-color: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    z-index: 101;
    &.hide-box-shadow {
      box-shadow: none;
    }
    .icon-wrapper {
      flex: 1;
      @include center;
      .icon-progress {
        font-size: px2rem(28);
      }
      .icon-bright {
        font-size: px2rem(24);
      }
    }
  }
  .setting-wrapper {
    position: absolute;
    left: 0;
    bottom: px2rem(45);
    width: 100%;
    height: px2rem(60);
    background: white;
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
    z-index: 101;
    .setting-font-size {
      display: flex;
      height: 100%;
      .preview {
        flex: 0 0 px2rem(40);
        @include center;
        &:first-child {
          transform: translate3d(50%, 0, 0);
        }
        &:last-child {
          transform: translate3d(-50%, 0, 0);
        }
      }
      .select {
        display: flex;
        flex: 1;
        .select-wrapper {
          flex: 1;
          display: flex;
          align-items: center;
          &:first-child {
            .line {
              &:first-child {
                border-top: none;
              }
            }
          }
          &:last-child {
            .line {
              &:last-child {
                border-top: none;
              }
            }
          }
          .line {
            flex: 1;
            height: 0;
            border-top: px2rem(1) solid #cccccc;
          }
          .point-wrapper {
            position: relative;
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
            border-left: px2rem(1) solid #cccccc;
            .point {
              position: absolute;
              top: -10px;
              left: -10px;
              width: px2rem(20);
              height: px2rem(20);
              border-radius: 50%;
              border: px2rem(1) solid #cccccc;
              box-shadow: 0 px2rem(3) px2rem(3) rgba(0, 0, 0, 0.15);
              background-color: white;
              @include center;
              .small-point {
                width: px2rem(5);
                height: px2rem(5);
                background: black;
                border-radius: 50%;
              }
            }
          }
        }
      }
    }

    .setting-theme {
      display: flex;
      height: 100%;
      &-item {
        flex: 1;
        display: flex;
        flex-direction: column;
        padding: px2rem(5);
        box-sizing: border-box;
        &.selected {
          .preview {
            border: px2rem(2) solid orange;
          }
          .text {
            color: #333;
          }
        }
        .preview {
          flex: 1;
          border: px2rem(1) solid #ccc;
        }
        .text {
          flex: 0 0 px2rem(30);
          font-size: px2rem(16);
          color: #ccc;
          @include center;
        }
      }
    }
    .setting-progress {
      position: relative;
      width: 100%;
      height: 100%;
      .progress-wrapper {
        width: 100%;
        height: 100%;
        @include center;
        padding: 0 px2rem(30);
        box-sizing: border-box;
        .progress {
          width: 100%;
          -webkit-appearance: none;
          height: px2rem(2);
          background: -webkit-linear-gradient(#999, #999) no-repeat, #ddd;
          background-size: 0 100%;
          &:focus {
            outline: none;
          }
          &::-webkit-slider-thumb {
            -webkit-appearance: none;
            height: px2rem(20);
            width: px2rem(20);
            border-radius: 50%;
            background: white;
            box-shadow: 0 4px 4px 0 rgba(0, 0, 0, 0.15);
            border: px2rem(1) solid #ddd;
          }
        }
      }
      .text-wrapper {
        position: absolute;
        left: 0;
        bottom: 0;
        width: 100%;
        color: #333;
        font-size: px2rem(12);
        text-align: center;
      }
    }
  }

  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: 100%;
    background: rgba(51, 51, 51, .8);
  }
}
</style>
