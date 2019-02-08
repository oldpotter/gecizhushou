<template>
	<view>
		<view class="uni-list">
			<view class="uni-list-cell" hover-class="uni-list-cell-hover" v-for="(song,index) in list" :key="index" @tap="tapSong"
			 :data-index="index">
				<view class="uni-media-list">
					<image class="uni-media-list-logo" :src="song.url"></image>
					<view class="uni-media-list-body">
						<view class="uni-media-list-text-top">{{song.name}}</view>
						<view class="uni-media-list-text-bottom uni-ellipsis">{{song.ablum}}</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: []
			};
		},
		onShow() {
			this.load()
		},

		methods: {
			load() {
				const _this = this
				uni.getStorage({
					key: "songlist",
					success(res) {
						// console.log(res.data)
						_this.list = res.data
					},
					fail() {
						console.log('fail')
					}
				})
			},
			
			tapSong(e){
				const index = e.currentTarget.dataset.index
				const song = this.list[index]
				uni.navigateTo({
					url: `../lyricinfo/lyricinfo?id=${song.id}&name=${song.name}&url=${song.url}&ablum=${song.ablum}`
				});
			}
		}
	}
</script>

<style>

</style>
