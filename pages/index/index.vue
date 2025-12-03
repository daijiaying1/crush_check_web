<template>
	<view class="content">
		<!-- Logo和标题区域 -->
		<view class="header">
			<image class="logo" src="/static/logo.png" mode="widthFix"></image>
			<text class="app-name">Crush Check</text>
		</view>
		
		<!-- QR码占位符 -->
		<view class="qr-container">
			<!-- 隐藏的 canvas，用于生成二维码 -->
			<canvas 
				class="qr-canvas-hidden" 
				canvas-id="qrCanvas" 
				id="qrCanvas"
				:style="{width: canvasWidth + 'px', height: canvasHeight + 'px'}"
			></canvas>
			<image 
				v-if="qrCodeUrl" 
				class="qr-image" 
				:src="qrCodeUrl" 
				mode="aspectFit"
			></image>
			<view class="qr-placeholder" v-else></view>
			<text class="qr-tip">—— 快速测试TA渣不渣 ——</text>
			<text class="qr-tip">手机立即体验</text>
		</view>
		
		<!-- 下载按钮区域 -->
		<view class="download-buttons">
			<view class="download-btn ios-btn" @click="generateQRCode('ios')">
				<image class="btn-icon" src="/static/ios.png" mode="widthFix"></image>
			</view>
			<view class="download-btn apk-btn" @click="generateQRCode('apk')">
				<image class="btn-icon" src="/static/apk.png" mode="widthFix"></image>
			</view>
			<view class="download-btn google-btn" @click="generateQRCode('google')">
				<image class="btn-icon" src="/static/google.png" mode="widthFix"></image>
			</view>
		</view>
	</view>
</template>

<script>
	import QRCode from '@/utils/qrcode.js'
	
	export default {
		data() {
			return {
				qrCodeUrl: '',
				canvasWidth: 300,
				canvasHeight: 300,
				// 三个下载链接（写死的）
				downloadLinks: {
					ios: 'https://www.crashcheck.net',
					apk: 'https://www.crashcheck.net/apk/app.apk',
					google: 'https://open.weixin.qq.com/'
				}
			}
		},
		onLoad() {

		},
		methods: {
			generateQRCode(type) {
				// 根据点击的按钮类型，获取对应的链接
				let qrText = '';
				switch(type) {
					case 'ios':
						qrText = this.downloadLinks.ios;
						break;
					case 'apk':
						qrText = this.downloadLinks.apk;
						break;
					case 'google':
						qrText = this.downloadLinks.google;
						break;
					default:
						// 默认生成包含所有三个链接的二维码
						qrText = `${this.downloadLinks.ios}\n${this.downloadLinks.apk}\n${this.downloadLinks.google}`;
				}
				
				// 二维码尺寸（px）
				const qrSize = 300;
				this.canvasWidth = qrSize;
				this.canvasHeight = qrSize;
				
				// 等待 canvas 渲染完成
				this.$nextTick(() => {
					// 使用 qrcode.js 生成二维码
					QRCode.toCanvas('qrCanvas', qrText, {
						width: qrSize,
						margin: 2,
						colorDark: '#000000',
						colorLight: '#FFFFFF',
						correctLevel: QRCode.CorrectLevel.M
					}, (error) => {
						if (error) {
							console.error('生成二维码失败:', error);
							uni.showToast({
								title: '生成二维码失败',
								icon: 'none'
							});
						} else {
							// 将 canvas 转换为图片
							setTimeout(() => {
								uni.canvasToTempFilePath({
									canvasId: 'qrCanvas',
									width: qrSize,
									height: qrSize,
									destWidth: qrSize,
									destHeight: qrSize,
									success: (res) => {
										this.qrCodeUrl = res.tempFilePath;
									},
									fail: (err) => {
										console.error('转换图片失败:', err);
										uni.showToast({
											title: '生成二维码失败',
											icon: 'none'
										});
									}
								}, this);
							}, 100);
						}
					});
				});
			}
		}
	}
</script>

<style>
	.content {
		width: 100vw;
		height: 100vh;
		background: #1d182e;
		display: flex;
		flex-direction: column;
		align-items: center;
        justify-content: center;
		padding: 250rpx ;
		box-sizing: border-box;
	}
	
	.header {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-top: 60rpx;
	}
	
	.logo {
		width: 200rpx;
		height: 120rpx;
		margin-bottom: 30rpx;
	}
	
	.app-name {
		color: #ffffff;
		font-size: 48rpx;
		letter-spacing: 2rpx;
		font-weight: 200;
	}
	
	.qr-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		flex: 1;
		justify-content: center;
		margin: 60rpx 0;
	}
	
	.qr-placeholder {
		width: 300rpx;
		height: 300rpx;
		background: #e0e0e0;
		border: 30rpx solid #ffffff;
		border-radius: 24rpx;
		margin-bottom: 40rpx;
	}
	
	.qr-canvas-hidden {
		position: absolute;
		left: -9999px;
		top: -9999px;
		visibility: hidden;
	}
	
	.qr-image {
		width: 300rpx;
		height: 300rpx;
		border: 30rpx solid #ffffff;
		border-radius: 24rpx;
		margin-bottom: 40rpx;
		background: #ffffff;
		padding: 0;
	}
	
	.qr-tip {
		color: rgba(255, 255, 255, 0.7);
		font-size: 24rpx;
		text-align: center;
		margin-bottom: 10rpx;
	}
	
	.download-buttons {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
		gap: 40rpx;
		width: 100%;
		flex-wrap: wrap;
	}
	
	.download-btn {
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		width: 310rpx;
		gap: 16rpx;
	}
	
	.btn-icon {
		width: 100%;
		height: 100%;
	}
	
	.btn-text {
		color: #000000;
		font-size: 24rpx;
		white-space: nowrap;
	}
</style>
