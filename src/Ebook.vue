<template>
  <div class="ebook">
    <TitleBar :ifTitleAndMenuShow="ifTitleAndMenuShow" />
    <div
      ref="readWrapper"
      class="read-wrapper"
    >
      <div id="read"></div>
      <div class="mask">
        <div
          class="left"
          @click="prevPage"
        ></div>
        <div
          @click="toggleTitleAndMenu"
          class="center"
        ></div>
        <div
          class="right"
          @click="nextPage"
        ></div>
      </div>
    </div>
    <MenuBar
      :ifTitleAndMenuShow="ifTitleAndMenuShow"
      :fontSizeList="fontSizeList"
      :defaultFontSize="defaultFontSize"
      :themeList="themeList"
      :defaultTheme="defaultTheme"
      :bookAvailable="bookAvailable"
      :navigation="navigation"
      @jumpTo="jumpTo"
      @setTheme="setTheme"
      @setFontSize="setFontSize"
      @onProgressChange="onProgressChange"
      ref="menuBar"
    />
  </div>
</template>

<script>
import Epub from 'epubjs'
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'

const DOWNLOAD_URL = '/static/2018_Book_AgileProcessesInSoftwareEngine.epub'

export default {
  name: 'Ebook',
  components: {
    TitleBar,
    MenuBar
  },
  data () {
    return {
      ifTitleAndMenuShow: false,
      bookAvailable: false,
      defaultFontSize: 16,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      defaultTheme: 0,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              color: '#000',
              background: '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              color: '#000',
              background: '#ceeaba'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              color: '#fff',
              background: '#000'
            }
          }
        },
        {
          name: 'gold',
          style: {
            body: {
              color: '#000',
              background: 'rgb(241, 236, 226)'
            }
          }
        }
      ],
      navigation: {}
    }
  },
  methods: {
    jumpTo(href) {
      this.rendition.display(href)
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu() {
      // 隐藏标题栏和菜单栏
      this.ifTitleAndMenuShow = false
      // 隐藏菜单栏弹出的设置栏
      this.$refs.menuBar.hideSetting()
      // 隐藏目录
      this.$refs.menuBar.hideContent()
    },
    onProgressChange (progress) {
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    setTheme (index) {
      this.themes.select(this.themeList[index].name)
      this.defaultTheme = index
    },
    setFontSize (fontSize) {
      this.defaultFontSize = fontSize
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
      }
    },
    toggleTitleAndMenu () {
      this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow
    },
    prevPage () {
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    nextPage () {
      if (this.rendition) {
        this.rendition.next()
      }
    },
    showEpub () {
      this.book = new Epub(DOWNLOAD_URL)
      const { clientWidth, clientHeight } = this.$refs.readWrapper
      this.rendition = this.book.renderTo('read', {
        method: 'default',
        width: clientWidth,
        height: clientHeight
      })
      this.rendition.display()
      this.themes = this.rendition.themes
      this.registerTheme()
      this.setTheme(this.defaultTheme)
      this.book.ready.then(() => {
        this.navigation = this.book.navigation
        return this.book.locations.generate()
      }).then(result => {
        this.locations = this.book.locations
        this.bookAvailable = true
      })
    },
    registerTheme () {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    }
  },
  mounted () {
    process.nextTick(() => {
      this.showEpub()
    })
  }
}
</script>

<style lang="scss" scoped>
@import '~@/assets/styles/mixin.scss';

.ebook {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
  .read-wrapper {
    position: absolute;
    top: px2rem(48);
    bottom: px2rem(48);
    width: 100%;
    margin: auto;
    box-sizing: border-box;
    // overflow: hidden;
    .mask {
      display: flex;
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 100;
      .left {
        flex: 0 0 px2rem(100);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
}
</style>
