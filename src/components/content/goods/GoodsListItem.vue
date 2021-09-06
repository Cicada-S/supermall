<template>
  <div class="goods-item">
    <img :src="goodsItem.show.img">
    <div class="goods-info">
      <p>{{goodsItem.title}}</p>
      <div class="msg">
        <span class="price"><span>￥</span>{{goodsItem.price}}</span>
        <span class="sales">{{goodsItem.sale}}人付款</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GoodsListItem',
  props: {
    goodsItem: {
      type: Object,
      default() {
        return {}
      }
    }
  }
}
</script>

<style scoped>
  .goods-item {
    display: flex;
    flex-direction: column;
    background: white;
    border-radius: 5px;
    margin-bottom: 10px;
    /* 瀑布流布局使用 */
    /* height: 100%;  */
    width: 48%;
  }

  .goods-item img {
    width: 100%;
    border-radius: 5px 5px 0 0;
    margin-bottom: 5px;
  } 

  .goods-item .goods-info {
    font-size: 13px;
    overflow: hidden;
    padding: 8px;
  }

  .goods-info p {
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
    overflow: hidden;
    letter-spacing: 1.3px;
    margin-bottom: 10px;
  }

  .goods-info .msg {
    display: flex;
    align-items: center;
    font-size: 11px;
  }

  .msg .price {
    font-size: 14px;
    color: var(--color-high-text);
    margin-right: 10px;
  }

  .msg .price span {
    font-size: 11px;
  }

  .msg .sales {
    color: #999;
  }
</style>