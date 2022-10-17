<template>
  <div id="home" class="wrapper">
    <Nav-Bar class="home-nav"><div slot="center">购物街</div></Nav-Bar>
    <Tab-Control
        class="tab-control"
        :titles="['流行', '新款', '精选']"
        @tabClick="tabClick"
        ref="tabControl1"
        v-show="isTabFixed"
      />
    <Scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll" :pull-up-load="true" @pullingUp="loadMore">
      <Home-Swiper :banners="banners" @swiperImageLoad="swiperImageLoad" />
      <Recomend-View :recommends="recommends" />
      <Feature-View />
      <Tab-Control
        :titles="['流行', '新款', '精选']"
        @tabClick="tabClick"
        ref="tabControl2"
      />
      <Goods-List :goods="showGoods"><slot></slot></Goods-List>
    </Scroll>

    <BackTop @click.native="backClick" v-show="isShowBackTop" />
  </div>
</template>  

<script>
// 全局组件
import NavBar from "components/common/navbar/NavBar";
import TabControl from "components/content/tabControl/TabControl";
import GoodsList from "components/content/goods/GoodsList";
import Scroll from "components/common/scroll/Scroll";
import BackTop from "components/content/backTop/BackTop";

// 子组件
import HomeSwiper from "./childComps/HomeSwiper";
import RecomendView from "./childComps/RecomendView";
import FeatureView from "./childComps/FeatureView";

// 方法
import { getHomeMultidata, getHomeGoods } from "network/home";

export default {
  name: "Home",
  components: {
    NavBar,
    TabControl,
    GoodsList,
    HomeSwiper,
    RecomendView,
    FeatureView,
    Scroll,
    BackTop,
  },
  data() {
    return {
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
      isTabFixed: false
    };
  },
  computed: {
    // 1. 图片加载完成的事件监听
    showGoods() {
      return this.goods[this.currentType].list;
    },
  },
  created() {
    // 1. 请求多个数据
    this.getHomeMultidata();

    // 2. 请求商品数据
    this.getHomeGoods("pop");
    this.getHomeGoods("new");
    this.getHomeGoods("sell");
  },
  mounted() {
    // 1. 图片加载完成的事件监听

    // 2. 获取tabControl的offsetTop
    // 所有的组件都有一个属性$el: 用于获取组件中的元素
  },
  methods: {
    /**
     *  事件监听相关的方法
     */
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
          break;
      }
      this.$refs.tabControl1.currentIndex = index;
      this.$refs.tabControl2.currentIndex = index;
    },

    // 返回顶部
    backClick() {
      this.$refs.scroll.scrollTo(0, 0, 500)
    },

    // 监听页面滚动
    contentScroll(position) {
      // 1. 判断BackTop是否显示
      this.isShowBackTop = (-position.y) > 650 ? true : false 

      // 2. 决定tabControl是否吸顶(position: fixed)
      this.isTabFixed = (-position.y) > this.tabOffsetTop
    },

    // 上拉加载更多
    loadMore() {
      console.log('加载更多');
      this.getHomeGoods(this.currentType)

      // 重新计算可滚动的高度
      // this.$refs.scroll.scroll.refresh()
    },

    // 监听轮播图的图片加载完成后
    swiperImageLoad() {
      // 获取tabControl的offsetTop
      this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop
    },

    /**
     *  网络请求相关的方法
     */
    getHomeMultidata() {
      getHomeMultidata().then((res) => {
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    },

    getHomeGoods(type) {
      const page = this.goods[type].page + 1;
      getHomeGoods(type, page).then((res) => {
        this.goods[type].list.push(...res.data.list);
        this.goods[type].page += 1;
        // 继续调用 上拉加载更多
        this.$refs.scroll.finishPullUp()
      });
    },
  },
};
</script>

<style scoped>
#home {
  height: 100vh;
  position: relative;
}

.home-nav {
  background-color: var(--color-tint);
  color: #fff;
  
  /* 在使用浏览器原生滚动时，为了让导航不跟随一起滚动 */
/* position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9; */
}

.tab-control {
  position: relative;
  z-index: 9;
}

.content {
  height: calc(100vh - 93px);
  overflow: hidden;
  /* margin-top: 44px; */
}
</style>