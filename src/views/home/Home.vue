<template>
  <div class="home">
    <Nav-Bar class="home-nav"><div slot="center">购物街</div></Nav-Bar>
    <Home-Swiper :banners="banners"/>
    <Recomend-View :recommends="recommends"/>
    <Feature-View/>
    <Tab-Control 
    class="tab-control" 
    :titles="['流行','新款','精选']"
    @tabClick="tabClick"/>
    <Goods-List :goods="showGoods"><slot></slot></Goods-List>
  </div>
</template>  

<script>
// 全局组件
import NavBar from 'components/common/navbar/NavBar'
import TabControl from 'components/content/tabControl/TabControl'
import GoodsList from 'components/content/goods/GoodsList'

// 子组件
import HomeSwiper from './childComps/HomeSwiper'
import RecomendView from './childComps/RecomendView'
import FeatureView from './childComps/FeatureView'

// 方法
import { getHomeMultidata, getHomeGoods } from 'network/home'

export default {
  name: "Home",
  components: {
    NavBar,
    TabControl,
    GoodsList,
    HomeSwiper,
    RecomendView,
    FeatureView
  },
  data() {
    return {
      banners: [],
      recommends: [],
      goods: {
        'pop': {page: 0, list: []},
        'new': {page: 0, list: []},
        'sell': {page: 0, list: []}
      },
      currentType: 'pop'
    }
  },
  created() {
    // 1. 请求多个数据
    this.getHomeMultidata()

    // 2. 请求商品数据
    this.getHomeGoods('pop')
    this.getHomeGoods('new')
    this.getHomeGoods('sell')
  },
  computed: {
    showGoods() {
      return this.goods[this.currentType].list
    }
  },
  methods: {
   /**
    *  事件监听相关的方法
    */
    tabClick(index) {
      console.log(index)
      switch (index) {
        case 0:
          this.currentType = 'pop'
          break;
        case 1:
          this.currentType = 'new'
          break;
        case 2:
          this.currentType = 'sell'
          break;
      }
    },

   /**
    *  网络请求相关的方法
    */
    getHomeMultidata(){
      getHomeMultidata().then(res => {
        this.banners = res.data.banner.list
        this.recommends = res.data.recommend.list
      })
    },
    getHomeGoods(type) {
      const page = this.goods[type].page + 1
      getHomeGoods(type, page).then(res => {
        this.goods[type].list.push(...res.data.list)
        this.goods[type].page += 1
      })
    }
  },
}

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
  }
</style>