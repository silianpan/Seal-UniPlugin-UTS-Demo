<template>
	<view>
		<h4 class="title">跨平台Office文档预览UTS插件【非X5离线、组件嵌入、水印、WPS预览编辑】</h4>
		<uni-section title="Office文档预览" type="line" padding>
			<uni-grid :column="4" border-color="#03a9f4">
				<uni-grid-item v-for="(item, index) in docList" :index="index" :key="index">
					<view class="grid-item-box" style="background-color: #fff;">
						<image @tap="showMenuDoc(item)" mode="aspectFill" style="width: 100rpx; height: 100rpx;"
							:src="'/static/' + item.substring(item.lastIndexOf('.') + 1) + '.svg'" />
						<text class="text">{{ item.substring(item.lastIndexOf('.') + 1) }}</text>
					</view>
				</uni-grid-item>
			</uni-grid>
		</uni-section>
		<uni-section title="图片预览" type="line" padding>
			<uni-grid :column="4" border-color="#03a9f4">
				<uni-grid-item v-for="(item, index) in imageList" :index="index" :key="index">
					<view class="grid-item-box" style="background-color: #fff;">
						<image @tap="openFileImage(item, index)" mode="aspectFill" style="width: 100rpx; height: 100rpx;"
							:src="'/static/' + item.substring(item.lastIndexOf('.') + 1) + '.svg'" />
						<text class="text">{{ item.substring(item.lastIndexOf('.') + 1) }}</text>
					</view>
				</uni-grid-item>
			</uni-grid>
		</uni-section>
		<uni-section title="音视频播放" type="line" padding>
			<uni-grid :column="4" border-color="#03a9f4">
				<uni-grid-item v-for="(item, index) in videoList" :index="index" :key="index">
					<view class="grid-item-box" style="background-color: #fff;">
						<image @tap="openFileVideo(item)" mode="aspectFill" style="width: 100rpx; height: 100rpx;"
							:src="'/static/' + item.substring(item.lastIndexOf('.') + 1) + '.svg'" />
						<text class="text">{{ item.substring(item.lastIndexOf('.') + 1) }}</text>
					</view>
				</uni-grid-item>
			</uni-grid>
		</uni-section>
	</view>
</template>
<script>
	import * as UTSSealOfficeOnline from '../../uni_modules/seal-office-online-uts'

	export default {
		data() {
			return {
				initPluginFirstSuccess: false,
				docList: [
					'http://silianpan.cn/upload/2022/01/01/2.pdf',
					'http://silianpan.cn/upload/2022/01/01/1.txt',
					'http://silianpan.cn/upload/2022/01/01/1.docx',
					'http://silianpan.cn/upload/2022/01/01/1.xlsx',
					'http://silianpan.cn/upload/2022/01/01/1.pptx',
					'http://silianpan.cn/upload/2022/01/01/1.epub',
					'http://silianpan.cn/upload/2022/01/01/1.csv',
					'https://static.gongkaoleida.com/2021/file/download/2021湖南省公务员考试《报考指导手册》.pdf',
					'https://static.gongkaoleida.com/2021/file/download/' + encodeURIComponent(
						'2021湖南省公务员考试《报考指导手册》') + '.pdf',
				],
				imageList: [
					'http://silianpan.cn/upload/2022/01/01/1.jpg',
					'http://silianpan.cn/upload/2022/01/01/1.jpeg',
					'http://silianpan.cn/upload/2022/01/01/1.png',
					'http://silianpan.cn/upload/2022/01/01/1.bmp',
					'http://silianpan.cn/upload/2022/01/01/1.gif'
				],
				videoList: [
					'http://silianpan.cn/upload/2022/01/01/1.mp4',
					'http://silianpan.cn/upload/2022/01/01/1.mkv',
					'http://silianpan.cn/upload/2022/01/01/1.avi',
					'http://silianpan.cn/upload/2022/01/01/1.mp3',
					'http://silianpan.cn/upload/2022/01/01/1.wav',
					'http://silianpan.cn/upload/2022/01/01/1.flac'
				]
			}
		},
		onLoad() {
			uni.showLoading({
				title: '插件首次初始化中'
			});
			this.initPluginFirst((code, msg) => {
				if (code === 1) {
					this.initPluginFirstSuccess = true;
					uni.showToast({
						title: '插件首次初始化成功',
						duration: 2000
					});
				} else {
					this.initPluginFirstSuccess = false;
				}
				uni.hideLoading();
			})
			
			// 检查WPS应用是否安装
			console.log('checkWps', this.checkWps());
		},

		methods: {
			initPluginFirst(callback) {
				UTSSealOfficeOnline.initEngine(callback);
			},
			/**
			 * 打开文档，非腾讯TBS，无内核加载，真正离线
			 * @param {Object} fileUrl 文档url
			 */
			openFile(fileUrl, otherOptions) {
				UTSSealOfficeOnline.openFile({
					waterMarkText: '水印水印换行',
					url: fileUrl,
					...otherOptions
				}, (code, msg) => {
					console.log('openFile', code, msg);
				});
			},
			/**
			 * WPS预览或编辑文档
			 * @param {String} fileUrl 文档url
			 * @param {String} openMode 打开模式
			 * openMode取值：
			 * Normal：正常模式，正常打开，WPS默认打开方式
			 * ReadOnly：只读模式，以只读的方式打开，WPS会隐藏编辑按钮
			 * EditMode：编辑模式，可对文档进行编辑
			 * ReadMode：阅读器模式，支持左右翻页，仅Word、TXT文档支持
			 * SaveOnly：另存模式(打开文件,另存,关闭)，仅Word、TXT文档支持
			 */
			openFileWPS(fileUrl, openMode) {
				UTSSealOfficeOnline.openFileWPS({
						url: fileUrl,
						openMode
					},
					(code, msg) => {
						console.log('openFileWPS', code, msg);
					}
				);
			},
			/**
			 * 图片预览
			 * @param {Object} fileUrl 图片url
			 * @param {Object} imageCurrentIndex 当前图片位置下标，从0开始
			 */
			openFileImage(fileUrl, imageCurrentIndex) {
				UTSSealOfficeOnline.openFileImage({
						imageUrls: JSON.parse(JSON.stringify(this.imageList)),
						imageCurrentIndex, // 当前点击图片在imageUrls中的下标，从0开始，默认为0
						imageIndexType: 'number', // 图片底部指示器类型，默认为'dot'，可选：'number':数字；'dot':点
						isSaveImg: true,
					},
					(code, msg) => {
						console.log('openFileImage', code, msg);
					});
			},
			/**
			 * 音视频播放
			 * @param {String} fileUrl 音视频url
			 */
			openFileVideo(fileUrl) {
				UTSSealOfficeOnline.openFileVideo({
						videoUrl: fileUrl,
						isLive: true,
						title: '音视频播放标题',
						isTopBar: true,
						isBackArrow: false,
						topBarBgColor: '#F77234',
						topBarTextColor: '#FCF26B',
						topBarTextLength: 12
					},
					(code, msg) => {
						console.log('openFileVideo', code, msg);
					}
				);
			},
			// 检查WPS应用是否安装，返回值：true或false
			checkWps() {
				return UTSSealOfficeOnline.checkWps();
			},
			/**
			 * 打开文档预览选项框
			 * @param {String} fileUrl 文档url
			 */
			showMenuDoc(fileUrl) {
				uni.showActionSheet({
					itemList: [
						'离线文档预览（非腾讯TBS，无内核加载，真正离线，自定义水印、顶栏）',
						'禁止截屏预览（离线文档、组件嵌入均支持）',
						'WPS打开文档（正常模式，需安装WPS客户端）',
						'WPS打开文档（只读模式，需安装WPS客户端）',
						'WPS打开文档（编辑模式，需安装WPS客户端）',
						'WPS打开文档（阅读器模式，需安装WPS客户端）',
						'WPS打开文档（另存模式，需安装WPS客户端）'
					],
					success: ({
						tapIndex
					}) => {
						switch (tapIndex) {
							case 0:
								this.openFile(fileUrl);
								break;
							case 1:
								this.openFile(fileUrl, {
									// 禁止截屏
									canScreenshot: false,
								});
								break;
							case 2:
								this.openFileWPS(fileUrl, 'Normal');
								break;
							case 3:
								this.openFileWPS(fileUrl, 'ReadOnly');
								break;
							case 4:
								this.openFileWPS(fileUrl, 'EditMode');
								break;
							case 5:
								this.openFileWPS(fileUrl, 'ReadMode');
								break;
							case 6:
								this.openFileWPS(fileUrl, 'SaveOnly');
								break;
						}
					}
				});
			},
		}
	}
</script>

<style lang="scss">
	.grid-item-box {
		flex: 1;
		// position: relative;
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 15px 0;
	}

	.text {
		font-size: 14px;
		margin-top: 5px;
	}

	.title {
		padding: 20rpx 30rpx 10rpx 30rpx;
		text-align: center;
	}
</style>