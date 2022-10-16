<template>
  <!-- ref/cholden -->
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'

  export default {
    name: "Scroll",
    props: {
      probeType: {
        type: Number,
        default: 0
      },
      pullUpLoad: {
        type: Boolean,
        default: false
      }
    },
    data() {
      return {
        scroll: null
      }
    },
    mounted() {
      // 1. 创建BScroll对象
      this.scroll = new BScroll(this.$refs.wrapper, {
        click: true,
        probeType: this.probeType,
        pullUpLoad: this.pullUpLoad
      })
      
      // 2. 监听滚动的位置
      this.scroll.on('scroll', (position) => {
        this.$emit('scroll', position)
      })

      // 3. 监听上拉事件
      this.scroll.on('pullingUp', () => {
        // 
        // setTimeout(() => {
        //   this.scroll.finishPullUp()
        // }, 2000)
        this.$emit('pullingUp')
      })
    },
    methods: {
      // backTop返回顶部
      scrollTo(x, y, time = 300) {
        this.scroll.scrollTo(x, y, time)
      },

      // 继续调用 上拉加载更多
      finishPullUp() {
        setTimeout(() => {
          this.scroll.finishPullUp()
        }, 2000)
      }
    }
  }
</script>

<style scoped>

</style>