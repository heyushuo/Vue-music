<template>
  <div id="app">
  <!--引用组件的时候可以使小写例如：tab  驼峰的可以改成m-header-->
    <MHeader></MHeader>
    <Tab></Tab>
    <keep-alive>
    	<router-view></router-view>
    </keep-alive>
  </div>
</template>

<script>
import MHeader from 'components/m-header/m-header'
import Tab from 'components/tab/tab'
export default {
  components:{
  	MHeader,
  	Tab
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
	
</style>
