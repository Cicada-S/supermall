<template>
  <div class="home">
    <Nav-Bar class="home-nav"><div slot="center">购物街</div></Nav-Bar>
    
    <Scroll class="content" ref="scroll" :probe-type="3" @scroll="contentScroll" :pull-up-load="true" @pullingUp="loadMore">
      <Home-Swiper :banners="banners" />
      <Recomend-View :recommends="recommends" />
      <Feature-View />
      <Tab-Control
        class="tab-control"
        :titles="['流行', '新款', '精选']"
        @tabClick="tabClick" />
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
      isShowBackTop: false
    };
  },
  created() {
    // 1. 请求多个数据
    this.getHomeMultidata();

    // 2. 请求商品数据
    this.getHomeGoods("pop");
    this.getHomeGoods("new");
    this.getHomeGoods("sell");
  },
  computed: {
    showGoods() {
      return this.goods[this.currentType].list;
    },
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
    },

    // 返回顶部
    backClick() {
      this.$refs.scroll.scrollTo(0, 0, 500)
    },

    // 显示隐藏backTop
    contentScroll(position) {
        this.isShowBackTop = (-position.y) > 650 ? true : false 
    },

    // 上拉加载更多
    loadMore() {
      console.log('加载更多');
      this.getHomeGoods(this.currentType)

      // 重新计算可滚动的高度
      // this.$refs.scroll.scroll.refresh()
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
.home-nav {
  background-color: var(--color-tint);
  color: #fff;
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9;
}

.tab-control {
  position: sticky;
  top: 44px;
  z-index: 9;
}

.content {
  height: calc(100vh - 93px);
  overflow: hidden;
  margin-top: 44px;
}
</style>