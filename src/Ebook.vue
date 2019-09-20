<template>
  <div class="ebook">
    <title-bar :ifTitleAndMenuShow="ifTitleAndMenuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <!--      这个mask DOM元素控制点击事件 上翻下翻显示导航-->
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleAndMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar
      ref="menuBar"
      :ifTitleAndMenuShow="ifTitleAndMenuShow"
      :fontSizeList="fontSizeList"
      @setFontSize="setFontSize"
      :defaultFontSize="defaultFontSize"
    ></menu-bar>
  </div>
</template>

<script>
  import TitleBar from '@/components/TitleBar';
  import MenuBar from '@/components/MenuBar';
  import Epub from 'epubjs';

  const DOWNLOAD_URL = `/static/2018_Book_AgileProcessesInSoftwareEngine.epub`;
  global.ePub = Epub;
  export default {
    name: "Ebook",
    data() {
      return {
        ifTitleAndMenuShow: false,
        fontSizeList: [
          {fontSize: 12},
          {fontSize: 14},
          {fontSize: 16},
          {fontSize: 18},
          {fontSize: 20},
          {fontSize: 22},
          {fontSize: 24}],
        defaultFontSize: 16
      }
    },
    components: {
      TitleBar,
      MenuBar
    },
    mounted() {
      this.showEpub()
    },
    methods: {
      showEpub() {
        this.book = new Epub(DOWNLOAD_URL);
        // 通过Book.renderTo生成Rendition对象
        this.rendition = this.book.renderTo('read', { //第一个参数是dom的id
          width: window.innerWidth,
          height: window.innerHeight,
          // 兼容iOS
          method: 'default'
        });
        // 通过Rendtion.display渲染电子书
        this.rendition.display();
        console.log(this.book);
      },
      toggleTitleAndMenu() {
        this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow;
        if (!this.ifTitleAndMenuShow) {
          this.$refs.menuBar.hideSetting()
        }
      },
      setFontSize(size) {
        this.defaultFontSize = size
      },
      //上一页
      prevPage() {
        if (this.rendition) {
          this.rendition.prev();
        }
      },
      // 下一页
      nextPage() {
        if (this.rendition) {
          this.rendition.next()
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import 'assets/styles/global';

  .ebook {
    position: relative;

    .read-wrapper {
      .mask {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        /*background-color: red;*/
        display: flex;

        .left, .right {
          flex: 0 0 px2rem(100);
          /*background: #42b983;*/
        }
      }

      .center {
        flex: 1;
        /*background: goldenrod;*/
      }
    }
  }
</style>
