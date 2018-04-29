<template>
    <transition>
        <div class="singer-detail"> 
            我是详情页面
        </div>
    </transition>  
</template>
<script type="text/ecmascript-6" >
export default {
  data () {
      return {
          
      }
  },
  created () {
      
  },
  components: {
      
  },
  methods: {
      
  }
}
</script>
<style scoped lang="stylus" rel="stylesheet/stylus">

    @import '~common/stylus/variable';

  .singer-detail
    position :fixed;
    left:0;
    top:0;
    right:0;
    bottom :0;
    width:100%;
    height:100%;
    z-index :100;
    background :$color-background;
  .slide-enter-active, .slide-leave-active
    transition: all 0.3s

  .slide-enter, .slide-leave-to
    transform: translate3d(100%, 0, 0)
</style>

