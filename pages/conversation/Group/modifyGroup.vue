<template>
	<view class="container">
		<u-navbar title-width="400" title="修改群资料" />
		<view class="group-menber">
			<AccessItem @click="modifyGroupNameOrIntroduction('name')" title="群组名" />
			<AccessItem @click="modifyGroupPortrait" title="群头像" />
			<AccessItem @click="modifyGroupNameOrIntroduction('introduction')" title="群介绍" />
		</view>
	</view>
</template>

<script>
	import AccessItem from "./comps/AccessItem.vue"
	import uploadFile from "@/uploadFile"
	export default {
		data() {
			return {
				groupInfo:{}
			}
		},
		components: {
			AccessItem
		},
		methods: {
			modifyGroupNameOrIntroduction(type) {
				uni.navigateTo({
					url: '/pages/conversation/Group/modifyGroupName?type='+type
				})
			},
			modifyGroupPortrait() {
				let _this = this;
				uni.chooseImage({
					count: 1,
					sizeType: ["original", "compressed"],
					sourceType: ["album"],
					success: async function(res) {
						const tempFilePaths = res.tempFilePaths;
						let upLoadImgUrl = await uploadFile(tempFilePaths[0], _this);
						_this.groupInfo.faceUrl = upLoadImgUrl
						_this.$openSdk.setGroupInfo(_this.groupInfo,(data)=>{
							if(data.msg){
								_this.$u.toast('update success!')
							}
						})
					},
				});
			},
			getGroupInfo(){
				const groupIdList = [this.vuex_conversation.groupID]
				this.$openSdk.getGroupsInfo(groupIdList,(data)=>{
					this.groupInfo = JSON.parse(data.msg)[0]
					this.$u.vuex('vuex_group_info',this.groupInfo)
				})
			},
		},
		onShow() {
			this.getGroupInfo()
		}
	}
</script>

<style lang="scss">
	page {
		display: flex;

		.container {
			flex: 1;
			background-color: #ebf2ff;

			.group-menber {
				background-color: #FFFFFF;
				margin-top: 24rpx;
			}
		}
	}
</style>
