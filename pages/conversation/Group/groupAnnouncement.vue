<template>
	<view class="">
		<u-navbar title-width="400" title="群公告">
			<view class="title-right" slot="right">
				<u-button :loadding="loadding" type="primary" size="mini" @click="changeGroupInfo">确定</u-button>
			</view>
		</u-navbar>
		<u-input v-model="groupInfo.notification" class="input-group-introduction" type="textarea" placeholder="请输入群公告"/>
	</view>
</template>

<script>
	export default{
		data(){
			return{
				groupInfo:{},
				loadding:false,
			}
		},
		methods:{
			changeGroupInfo(){
				this.loadding = true
				this.$openSdk.setGroupInfo(this.groupInfo,(data)=>{
					console.log(data);
					if(data.msg){
						this.$u.toast('update success!')
					}
					this.loadding = false
					uni.navigateBack()
				})
			}
		},
		onLoad:function(options){
			if(options.content) this.groupInfo = JSON.parse(options.content)
		}
	}
</script>

<style lang="scss">
	.title-right{
		margin-right: 24rpx;
	}
	.input-group-introduction{
		margin: 24rpx;
		/deep/.u-input__textarea{
			font-size: 32rpx;
			.uni-textarea-placeholder{
				font-size: 32rpx;
			}
		}
	}
</style>
