<style lang="scss">
	/* 注意要写在第一行，同时给style标签加入lang="scss"属性 */
	@import "@/uni_modules/uview-ui/index.scss";
</style>
<template>
  <div>
    <router-view></router-view>
  </div>
</template>
<script>
	export default {
		// beforeCreate() {
		//     // 在应用启动时检查本地存储中是否存在token，如果不存在则跳转到登录页
		//     const token = uni.getStorageSync('token')
		//     if (!token && this.$route.path !== '/pages/index/login') {
		//      uni.navigateTo({
		//        url: '/pages/index/login'
		//      })
		//     }
		// },
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>
