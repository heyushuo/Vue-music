<template>
	<div class="singer">
		<list-view @select="selectSinger" :data='singers' ></list-view>
		<router-view></router-view>
	</div>
</template>

<script>
	import {getSingerList} from 'api/singer'
	import {ERROR_OK} from 'api/config'
	import Singer from 'common/js/singer'
	import ListView from 'base/listview/listview'
	
	const HOT_SINGER_LEN = 10
  	const HOT_NAME = '热门'
	export default {
		components:{
			ListView
		},
		created(){
			this._getSingerList()
		},
		data(){
			return {
				singers:[]
			}
		},
		methods:{
			selectSinger(singer){
				this.$router.push({
					path: `/singer/${singer.id}`
				})
			},
			_getSingerList(){
				getSingerList().then((res)=>{
					
					console.log(this._normaliseSinger(res.data.list))
					this.singers=this._normaliseSinger(res.data.list);
				})
			},
			_normaliseSinger(list){
				let map={
					hot:{
						title:HOT_NAME,
						items:[]
					}
				}
				list.forEach((item ,index)=>{
					
					if(index<HOT_SINGER_LEN){
						map.hot.items.push(new Singer({
							name:item.Fsinger_name,
							id:item.Fsinger_mid
						}))
					}
					
					//定义key为字母a-z
					const key=item.Findex;
					if(!map[key]){
						map[key] = {
							title:key,
							items:[]
						}
					}
					
					map[key].items.push(new Singer({
						name:item.Fsinger_name,
						id:item.Fsinger_mid
					}))
					
					
				})
				//上班循环完成现在需要对map进行排序
				let ret=[]
				let hot=[]
				for(let key in map){
					let val=map[key];
					if(val.title.match(/[a-zA-Z]/)){
						ret.push(val);
					}else if(val.title === HOT_NAME){
						hot.push(val)
					}
				}
				//现在对字母进行排序
				ret.sort((a,b)=>{
					return a.title.charCodeAt(0)-b.title.charCodeAt(0)
				})
				
				return [...hot,...ret]
				
			}
		}
	}
</script>
<style scoped lang="stylus" rel="stylesheet/stylus">
  .singer
    position: fixed
    top: 88px
    bottom: 0
    width: 100%
</style>