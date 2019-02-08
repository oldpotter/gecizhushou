<template>
	<view>
		<view class="uni-padding-wrap uni-common-mt">
			<view class="text-box" scroll-y="true">
				<text class="uni-media-list-text-top" selectable>{{ lyric }}</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				lyric: '',
				song: null
			};
		},

		onLoad(e) {
			this.song = {
				id: e.id,
				name: e.name,
				url: e.url,
				ablum: e.ablum
			}
			this.getLyric(this.song.id)
			// console.log(this.song.url)
		},

		onNavigationBarButtonTap(e) {
			this.add()
		},

		methods: {
			add() {
				const _this = this
				uni.getStorage({
					key: 'songlist',
					success(res) {
						let songList = res.data
						//查找一下是否已经保存过
						const results = songList.filter(song => song.id === _this.song.id)
						// console.log(results)
						if (results.length > 0) {
							// 							uni.showToast({
							// 								title: '歌曲已存在',
							// 								mask: false,
							// 								duration: 1500
							// 							});
							uni.showModal({
								title: '',
								content: '是否删除这首歌曲',
								showCancel: true,
								cancelText: '取消',
								confirmText: '删除',
								success: res => {
									if(res.confirm){
										const newList = songList.filter(song => song.id !== _this.song.id)
										console.log(newList)
										uni.setStorage({
											key: 'songlist',
											data: newList,
											success() {
												uni.showToast({
													title: '删除成功',
													mask: false,
													duration: 1500
												});
												setTimeout(() => uni.navigateBack({
													delta: 1
												}), 1500)
											}
										})
									}
								},
								fail: () => {},
								complete: () => {}
							});
						} else {
							songList.push(_this.song)
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
						songList.push(_this.song)
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
							setTimeout(() => uni.navigateBack({
								delta: 1
							}), 1500)
						} else {
							// console.log(res)
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
