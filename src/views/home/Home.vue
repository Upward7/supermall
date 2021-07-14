<template>
  <div id="home" ref="wrapper">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <tab-control
      :titles="['流行', '新款', '精选']"
      @tabClick="tabClick"
      ref="tabControl1"
      class="tabcontrol"
      v-show="isTabFixed"
    ></tab-control>
    <scroll
      class="content"
      ref="scroll"
      :probe-type="3"
      @scroll="contentScroll"
      :pull-up-load="true"
      @pullingUp="loadMore"
    >
      <home-swiper
        :banners="banners"
        @swiperImageLoad="swiperImageLoad"
      ></home-swiper>
      <recommend-view :recommends="recommends"></recommend-view>
      <feature-view></feature-view>
      <tab-control
        :titles="['流行', '新款', '精选']"
        @tabClick="tabClick"
        ref="tabControl2"
      ></tab-control>
      <goods-list :goods="showGoods"></goods-list>
    </scroll>

    <!-- 监听组件的原生事件，需给对应事件加上.native -->
    <back-top @click.native="backClick" v-show="isShowBackTop" />
  </div>
</template>

<script>
import HomeSwiper from "./childComps/HomeSwiper";
import RecommendView from "./childComps/RecommendView";
import FeatureView from "./childComps/FeatureView";

import NavBar from "components/common/navbar/NavBar";
import TabControl from "components/content/tabControl/TabControl";
import GoodsList from "components/content/goods/GoodsList";
import Scroll from "components/common/scroll/Scroll";
import BackTop from "components/content/backTop/BackTop";

import { getHomeMultidata, getHomeGoods } from "network/home";
import {itemListenerMixin} from "common/mixin"

export default {
  components: {
    HomeSwiper,
    RecommendView,
    FeatureView,
    NavBar,
    TabControl,
    GoodsList,
    Scroll,
    BackTop,
  },
  mixins: [itemListenerMixin],
  data() {
    return {
      // result: null
      banners: [],
      recommends: [],
      goods: {
        pop: { page: 0, list: [] },
        new: { page: 0, list: [] },
        sell: { page: 0, list: [] },
      },
      currentType: "pop",
      isShowBackTop: false,
      tabOffsetTop: 0,
      isTabFixed: false,
      saveY: 0,
    };
  },
  computed: {
    showGoods() {
      return this.goods[this.currentType].list;
    },
  },
  destroyed() {
    console.log('home destroyed');
  },
  activated() {
    this.$refs.scroll.refresh()   
    // 时间不能设置为0
    this.$refs.scroll.scrollTo(0,this.saveY,2)
  },
  deactivated() {
    // 保存Y值
    this.saveY = this.$refs.scroll.getScrollY()

    // 取消事件监听
    this.$bus.$off('itemImageLoad', this.itemImgListener)
  },
  created() {
    // 1. 请求多个数据
    this.getHomeMultidata();
    // 2. 请求商品数据
    this.getHomeGoods("pop");
    this.getHomeGoods("new");
    this.getHomeGoods("sell");
  },
  // $refs用于获取指定的dom元素，在created阶段是获取不到的
  mounted() {
    // // img标签确实被挂载，但其中的图片还没有占据高度
    // // 监听item中图片加载完成
    // // 防抖操作
    // const refresh = debounce(this.$refs.scroll.refresh, 10);
    // // 对监听事件进行保存
    // this.itemImgListener = () => {
    //   // this.$refs.scroll.refresh()
    //   refresh();
    // }
    // this.$bus.$on("itemImageLoad", this.itemImgListener);
  },
  methods: {
    // 事件监听相关的方法
    tabClick(index) {
      switch (index) {
        case 0:
          this.currentType = "pop";
          break;
        case 1:
          this.currentType = "new";
          break;
        case 2:
          this.currentType = "sell";
      }
      this.$refs.tabControl1.currentIndex = index;
      this.$refs.tabControl2.currentIndex = index;
    },
    backClick() {
      this.$refs.scroll.scrollTo(0, 0);
    },
    contentScroll(position) {
      // console.log(position);
      // 判断BackTop是否显示
      this.isShowBackTop = -position.y > 1000;

      // 决定tabControl是否吸顶(position: fixed)
      this.isTabFixed = -position.y > this.tabOffsetTop;
    },
    loadMore() {
      // console.log('上拉加载更多');
      this.getHomeGoods(this.currentType);

      this.$refs.scroll.scroll.refresh();
    },
    swiperImageLoad() {
      // 获取tabControl的offsetTop
      // 所有组件都有一个属性$el: 用于获取组件中的元素
      // console.log(this.$refs.tabControl.$el.offsetTop);
      this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop;
    },
    // 网络请求相关的方法
    getHomeMultidata() {
      getHomeMultidata().then((res) => {
        // this.result = res;
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    },
    getHomeGoods(type) {
      const page = this.goods[type].page + 1;
      getHomeGoods(type, page).then((res) => {
        // console.log(res);
        this.goods[type].list.push(...res.data.list);
        this.goods[type].page += 1;

        this.$refs.scroll.finishPullUp();//数据已加载完毕
      });
    },
  },
};
</script>

<style scoped>
#home {
  /* padding-top: 44px; */
  /* viewport height */
  height: 100vh;
  position: relative;
}

.home-nav {
  background-color: var(--color-tint);
  color: #fff;

  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9;
}

.content {
  position: absolute;

  left: 0;
  right: 0;
  top: 44px;
  bottom: 49px;
}

.tabcontrol {
  top: 44px;
  position: relative;
  z-index: 9;
}

/* .content {
  height: calc(100% - 93px);
  margin-top: 44px;
} */
</style>