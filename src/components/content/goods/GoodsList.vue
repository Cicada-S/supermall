<template>
  <div class="goods">
    <Goods-List-Item v-for="item in goods" :goods-item="item"/>
  </div>
</template>

<script>
import GoodsListItem from './GoodsListItem'

export default {
  name: 'GoodsList',
  components: {
    GoodsListItem
  },
  props: {
    goods: {
      type: Array,
      default() {
        return []
      }
    }
  }
}
</script>

<style scoped>
  .goods {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-evenly;
    padding-top: 6px;
    margin-bottom: 49px;
    background: whitesmoke;
  }
</style>