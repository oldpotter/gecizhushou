<template>
	<view>
		<view class="uni-padding-wrap uni-common-mt">
			<view class="text-box" scroll-y="true">
				<text class="uni-media-list-text-top" selectable>{{ lyric }}</text>
			</view>
			<view class="uni-btn-v">
				<button type="primary" @click="add">收藏</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				songId: null,
				lyric: ''
			};
		},

		onLoad(e) {
			// console.log(e.songId)
			this.songId = e.songId
			this.getLyric(this.songId)
		},

		methods: {
			add() {
				uni.getStorage({
					key: 'songlist',
					success(res) {
						let songList = res.data
						//查找一下是否已经保存过
						const results = songList.filter(song => song.id === this.songId)
						// console.log(results)
						if (results.length > 0) {
							uni.showToast({
								title: '歌曲已存在',
								mask: false,
								duration: 1500
							});
						} else {
							const song = {
								'id': this.songId,
								'lyric': this.lyric
							}
							songList.push(song)
							uni.setStorage({
								key: 'songlist',
								data: songList,
								success() {
									uni.showToast({
										title: '保存成功',
										mask: false,
										duration: 1500
									});
								}
							})
						}

					},
					fail(res) {
						let songList = []
						const song = {
							'id': this.songId,
							'lyric': this.lyric
						}
						songList.push(song)
						uni.setStorage({
							key: 'songlist',
							data: songList,
							success() {
								uni.showToast({
									title: '保存成功',
									mask: false,
									duration: 1500
								});
							}
						})
					}
				})
			},
			getLyric(songId) {
				uni.request({
					url: 'http://shenkeling.top:3000/lyric?id=' + songId,
					method: 'GET',
					success: res => {
						if (!res.data.lrc) {
							uni.showToast({
								title: '这首歌曲没有歌词',
								mask: false,
								duration: 1500
							});
							uni.navigateBack({
								delta: 1
							});
						} else {
							const lyric = res.data.lrc.lyric
								.replace(/\[[\d.:]+\]/g, '')
							this.lyric = lyric
						}
					},
				});
			}

		}
	}
</script>

<style>
</style>
