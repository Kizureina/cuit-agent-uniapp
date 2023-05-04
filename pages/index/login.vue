<template>
	<div class="login">
		<form class="login-form" @submit.prevent>
			<h2>用户登录</h2>
			<view v-if="showError" class="error">
				<uni-icons type="uni-close-circled"></uni-icons>
				<text>{{errorMsg}}</text>
			</view>
			<div class="form-group">
				<input type="text" placeholder="请输入用户名" v-model="username">
			</div>
			<div class="form-group">
				<input type="password" placeholder="请输入密码" v-model="password">
			</div>
			<h4>注意：服务器响应较慢，登录前请务必确认输入正确，否则可能有较高延迟</h4>
			<br/>
			<div class="form-group">
				<u-button class="login-btn" :disabled="!isFormValid" @click="handleSubmit()">登录</u-button>
				<!-- <button class="login-btn" :disabled="!isFormValid" @click="handleSubmit()">登录</button> -->
			</div>
			<br>
			<br>
			<br>
			<br>
			<br>
			<h5 style="text-align: center;">开发者不会在服务器存储您的任何个人信息</h5>
		</form>
	</div>
</template>

<script>
	import JSEncrypt from 'jsencrypt'
	import uniSection from "@/uni_modules/uni-section/components/uni-section/uni-section.vue"
	import uniPopup from "@/components/uni-popup/components/uni-popup/uni-popup.vue"
	export default {
		components: {
			uniSection,
			uniPopup
		},
		data() {
			return {
				showError: false,
				errorMsg: "",
				username: '',
				password: '',
				showLoading: false
			}
		},
		created() {
		},
		computed: {
			isFormValid() {
				return this.username !== '' && this.password !== ''
			}
		},
		methods: {
			encryptest() {
				console.log("发起请求")
				uni.request({
					url: 'http://localhost:2333/data/rsa',
					method: 'POST',
					header: {
					    'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: {
						enCryptText: this.username
					},
					success: (res) => {
						console.log(res.data);
					},
					fail: (err) => {
						console.log(err);
					}
				});
			},
			enCrypted(msg) {
				let publicKey =
					"MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAK9sKAKgc9LWh/fYqZGOo3gK0gfulDXTUYEZAIroPg3HNmkMq3h7PVDgVClRfwaQiMhSFET3c2BGrKV4AgXb4CsCAwEAAQ=="
				let encrypt = new JSEncrypt()
				encrypt.setPublicKey(publicKey)
				return encrypt.encrypt(msg)
			},
			handleSubmit() {
				uni.showLoading({
					title: '加载中'
				}); // 显示加载页面
				this.password = this.enCrypted(this.password)
				uni.request({
					url: 'http://localhost:2333/data/course',
					// url: 'http://localhost:2333/data/course',
					method: 'POST',
					header: {
					    'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: {
						username: this.username,
						token: this.password,
						// token: "5D30C10C-E5E3-8956-618F-A86EDAF74411"
					},
					success: (res) => {
						console.log(res.data.message);
						if (res.data.code === 1) {
							let jsondata = res.data.activities;
							uni.setStorage({
								key: 'course',
								data: JSON.stringify(jsondata),
								success: function() {
									console.log('successfully stored data');
								}
							});
							uni.setStorageSync("token", res.data.code)
							uni.setStorageSync("username", this.username)
							uni.setStorageSync("password", this.password)
							// uni.switchTab({
							uni.redirectTo({
								url: '/pages/index/index',
								success: function() {
									var page = getCurrentPages().pop();
									if (page == undefined || page == null) return;
									page.onShow(); // 这里触发了目标页面的 onShow 函数
								}
							})
							uni.hideLoading();
							
							uni.showToast({
								title: '登录成功', //显示的文字
								duration: 1000, //显示多少时间，默认1500毫秒
								icon: "success" //自定义显示的图标，默认成功success，错误error,加载loading，不显示图标是none
							})
						} else {
							uni.hideLoading();
							this.showError = true;
							this.errorMsg = "用户名或密码错误";
						}
					},
					fail: (err) => {
						console.log(err);
					}
				});
			}
		}
	}
</script>

<style scoped>
	.btn {
	    width: 200rpx;
	    height: 72rpx;
	    border: 2rpx solid #005EF4;
	    border-radius: 36rpx;
	    margin: 0;
	    background: transparent;
	    font-size: 28rpx;
	    line-height: 72rpx;
	    color: #0268FA;
	}
	
	.login {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100vh;
		background-color: #f5f5f5;
	}

	.login-form {
		width: 80%;
		max-width: 400px;
		background-color: #fff;
		border-radius: 10px;
		padding: 20px;
		box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
	}

	.login-form h2 {
		text-align: center;
		margin-bottom: 20px;
	}

	.form-group {
		margin-bottom: 20px;
	}

	.form-group input {
		width: 100%;
		padding: 10px;
		border: none;
		border-radius: 4px;
	}

	.login-btn {
		font-size: medium;
		background-color: #007aff;
		color: #fff;
		padding: 10px;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		width: 100%;
	}

	.loading-page {
		position: fixed;
		z-index: 999;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.loading-icon {
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: #0055ff;
		animation: rotate 1s infinite linear;
		/* animation: 3s linear 1s infinite alternate slidein; */
	}

	.loading-text {
		color: #fff;
		margin-top: 10px;
		font-size: 18px;
	}

	.mask {
		position: absolute;
		z-index: -1;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		background-color: rgba(0, 0, 0, 0.5);
		/* 半透明黑色遮挡层 */
	}
	.error {
	  display: flex;
	  align-items: center;
	  background-color: rgba(255, 0, 0, 0.1);
	  border-radius: 4px;
	  padding: 8rpx;
	  margin-top: 20rpx;
	}
	.error uni-icons {
	  font-size: 30rpx;
	  color: red;
	  margin-right: 10rpx;
	}
	.error text {
	  font-size: 28rpx;
	  color: red;
	  line-height: 36rpx;
	}

	@keyframes rotate {
		0% {
			transform: rotate(0deg);
		}

		100% {
			transform: rotate(360deg);
		}
	}
</style>