<template>
	<view class="container">
		<u-navbar title="转让群"/>
		<u-search shape="square" :show-action="false" bg-color="#e9f2ff" class="searchInput" @change="search" @clear="clearSearch"
			placeholder="搜索" v-model="searchVal"/>
		<uni-indexed-list :showIndex="false" @click="clickItem" :showSelect="false" :isShowSection="false" :options="friendList"/>
		
		<u-modal v-model="showModal" @confirm="comfirmAction" :show-title="false" confirm-text="确定" show-cancel-button cancel-text="取消" content="确定要转让群吗?"></u-modal>
	</view>
</template>

<script>
	export default{
		data(){
			return{
				showIndex:true,
				showModal:false,
				searchVal:"",
				friendList:[
					{
						letter: "#",
						data: []
					}
				],
				selectUser:""
			}
		},
		methods:{
			clickItem(data){
				this.selectUser = data.item.name.userId
				this.showModal = true
			},
			search(val){
				if(val===""){
					this.showIndex = true
					this.friendList[0].letter = "#"
					this.friendList[0].data = this.vuex_original_mail_list
				}else{
					this.showIndex = false
					const newArr = []
					this.friendList[0].letter = "contacts"
					this.friendList[0].data.forEach(v=>{
						if(v.name.indexOf(val)>-1) newArr.push(v)
					})
					this.friendList[0].data = newArr
				}
			},
			comfirmAction(){
				if(this.selectUser==='') return false
				this.$openSdk.transferGroupOwner(this.vuex_conversation.groupID,this.selectUser,(data)=>{
					console.log(data);
					if(data.msg){
						this.$u.toast('转让成功')
						uni.navigateBack()
					}
				})
			},
			clearSearch(){
				
			},
			getList(){
				console.log(this.vuex_group_info);
				this.$openSdk.getGroupMemberList(this.vuex_conversation.groupID,0,0,(data)=>{
					let tmpArr = JSON.parse(data.msg).data
					this.friendList[0].data = tmpArr.filter(v=>{
						if(v.userId!==this.vuex_group_info.ownerId) return v
					})
				})
			},
		},
		beforeMount() {
			this.getList()
		},
	}
</script>

<style lang="scss">
	page{
		display: flex;
		.container{
			flex: 1;
			background-color: #f0f2f5;
			.right-title{
				margin-right: 24rpx;
			}
			.searchInput{
				padding: 24rpx 0;
				background-color: #f0f2f5;
				/deep/.u-content{
					border-radius: 0 !important;
					background-color: #e8f2ff !important;
					height: 78rpx !important;
				}
			}
			.uni-indexed-list{
				background-color: #f0f2f5;
				/deep/.uni-indexed-list__item{
					background-color: #f0f2f5;
				}
			}
		}
	}
</style>
