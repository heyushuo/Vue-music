<template>
  <div class="slider" ref="slider">
    <div class="slider-group" ref="sliderGroup">
      <slot>
      </slot>
    </div>
    <div class="dots">
      <span class="dot"  v-for="(item, index) in dots" :class="{active: currentPageIndex === index }"></span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import {addClass} from 'common/js/dom'
  import BScroll from 'better-scroll'

  export default {
    name: 'slider',
    props: {
      loop: {
        type: Boolean,
        default: true
      },
      autoPlay: {
        type: Boolean,
        default: true
      },
      interval: {
        type: Number,
        default: 4000
      }
    },
    data() {
      return {
        dots: [],
        currentPageIndex: 0
      }
    },
    mounted() {
      setTimeout(() => {
        this._setSliderWidth()
        this._initDots()   //初始化下方的点
        this._initSlider()

        if (this.autoPlay) {
          this._play()
        }
      }, 20)
		//监听窗口变化
      window.addEventListener('resize', () => {
        if (!this.slider) {
          return
        }
        this._setSliderWidth(true)
//     宽度发生变化	 重新刷新slider
        this.slider.refresh()
      })
    },
    //设置keep-alive后，可以在这里执行激活的东西
    activated() {
      if (this.autoPlay) {
        this._play()
      }
    },
    deactivated() {
      clearTimeout(this.timer)
    },
    beforeDestroy() {
      clearTimeout(this.timer)
    },
    methods: {
      _setSliderWidth(isResize) {
//    	this.$refs.sliderGroup  获取的是dom对象
//这里在vue的实例对象上绑定了一个children的值
        this.children = this.$refs.sliderGroup.children
        let width = 0
        let sliderWidth = this.$refs.slider.clientWidth
        for (let i = 0; i < this.children.length; i++) {
          let child = this.children[i]
          //添加class
          addClass(child, 'slider-item')

          child.style.width = sliderWidth + 'px'
          width += sliderWidth
        }
        //如果是循环需要两个的宽度
        //如果是视口变化执行的函数，不需要在加2倍了
        if (this.loop && !isResize) {
          width += 2 * sliderWidth
        }
        this.$refs.sliderGroup.style.width = width + 'px'
      },
      //初始化slider
      _initSlider() {
        this.slider = new BScroll(this.$refs.slider, {
          scrollX: true, //横向滚动
          scrollY: false, //纵向不滚动
          momentum: false, //惯性
          snap: true,      //
          snapLoop: this.loop, //循环
          snapThreshold: 0.3, //
          snapSpeed: 400
        })

        this.slider.on('scrollEnd', () => {
        	//获取当前滚动的第几个元素
          let pageIndex = this.slider.getCurrentPage().pageX
          //如果此时是自动循环，自动减
          if (this.loop) {
            pageIndex -= 1
          }
          this.currentPageIndex = pageIndex
					//如果设置了自动循环
					//直接调用this._play()
          if (this.autoPlay) {
          	//自动轮播快要轮播时，如果现在手动轮播清除掉那个自动轮播
          	clearTimeout(this.timer)
            this._play()
          }
        })

        this.slider.on('beforeScrollStart', () => {
          if (this.autoPlay) {
            clearTimeout(this.timer)
          }
        })
      },
      _initDots() {
        this.dots = new Array(this.children.length)
      },
      _play() {
        let pageIndex = this.currentPageIndex + 1
        if (this.loop) {
          pageIndex += 1
        }
        //轮播的间隔
        this.timer = setTimeout(() => {
        	//跳转到相应地点  第二个参数是y方向的
          this.slider.goToPage(pageIndex, 0, 400)
        }, this.interval)
      }
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .slider
    min-height: 1px
    .slider-group
      position: relative
      overflow: hidden
      white-space: nowrap
      .slider-item
        float: left
        box-sizing: border-box
        overflow: hidden
        text-align: center
        a
          display: block
          width: 100%
          overflow: hidden
          text-decoration: none
        img
          display: block
          width: 100%
    .dots
      position: absolute
      right: 0
      left: 0
      bottom: 12px
      text-align: center
      font-size: 0
      .dot
        display: inline-block
        margin: 0 4px
        width: 8px
        height: 8px
        border-radius: 50%
        background: $color-text-l
        &.active
          width: 20px
          border-radius: 5px
          background: $color-text-ll
</style>