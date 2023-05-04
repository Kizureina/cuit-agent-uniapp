<template>
	<view>
		<ly-curriculumtable :weekTableState="true" :weekTableNumber="weekNumbers" :controlWeek="controlWeek"
			:timetableType="timetableType" :timetables="timetables" :startdDate="startdDate" :weekButton="weekButton"
			@nextWeekClick="nextWeekClick" @lastWeekClick="lastWeekClick" @courseClick="courseClick"
			@weekSelectClick="weekSelectClick" @weekOpenClick="weekOpenClick">
		</ly-curriculumtable>
		<u-button class="update-btn" @click="updateData()">更新数据</u-button>
		<u-button class="login-btn" @click="removeData()">清除数据</u-button>
		<!-- 课程详情 -->
		<u-modal class="modal" :show="showMyModal" title="课程详情" :closeOnClickOverlay="true" :showConfirmButton="false"
			@close="closeModal">
			<view class="slot-content">
				<u-cell-group>
					<u-cell v-for="(item,index) in modalItem" :key="index" :title="item" :icon="modalIcon[index]"
						:iconStyle="{color: colorList()[index+1]}" size="large"></u-cell>
				</u-cell-group>
			</view>
		</u-modal>
		<view v-show="showLoading" class="loading-page">
			<view class="loading-icon"></view>
			<view class="mask"></view>
			<view class="loading-text">加载中...</view>
		</view>
	</view>
</template>

<script>
	import uniSection from "@/uni_modules/uni-section/components/uni-section/uni-section.vue"
	import uCellGroup from "@/uni_modules/uview-ui/components/u-cell-group/u-cell-group.vue"
	import uniPopupDialog from "@/components/uni-popup/components/uni-popup-dialog/uni-popup-dialog.vue"
	export default {
		onShow() {
			this.getCourse(this.controlWeek)
		},
		components: {
			uCellGroup,
			uniSection,
			uniPopupDialog
		},
		created() {
			console.log("登录校验")
			const token = uni.getStorageSync('token')
			if (!token) {
				uni.redirectTo({
					url: '/pages/index/login'
				})
			}
			// 获取当前周
			let days = this.getDateDays(this.startdDate)
			let week = parseInt(days / 7 + 1)
			// 赋值当前周
			this.controlWeek = week
			// 显示当前周的课表
			this.$nextTick(() => {
				this.weekCurrent = week
			})
			// 赋值课表
			this.getCourse(this.controlWeek)
		},
		data() {
			return {
				showLoading: false,
				title: "hello cuit agent!",
				weekNumbers: 18, //一共显示几周
				controlWeek: 1, //显示的第几周
				weekButton: true, //开启上一周下一周按钮
				startdDate: '2023-2-20', //开始时间  默认为当前时间
				timetables: [
					['', '', '', '', '', '', '', '', '', '', ''],
					['', '', '', '', '', '', '', '', '', '', ''],
					['', '', '', '', '', '', '', '', '', '', ''],
					['', '', '', '', '', '', '', '', '', '', ''],
					['', '', '', '', '', '', '', '', '', '', '']
				],
				timetableType: [{
						index: '1',
						name: '08:20\n09:05',
						value: '08:20-09:05'
					},
					{
						index: '2',
						name: '09:15\n10:00',
						value: '09:15-10:00'
					},
					{
						index: '3',
						name: '10:20\n11:05',
						value: '10:20-11:05'
					},
					{
						index: '4',
						name: '11:15\n12:00',
						value: '11:15-12:00'
					},
					{
						index: '5',
						name: '14:00\n14:45',
						value: '14:00-14:45'
					},
					{
						index: '6',
						name: '14:55\n15:40',
						value: '14:55-15:40'
					},
					{
						index: '7',
						name: '15:55\n16:40',
						value: '15:55-16:40'
					},
					{
						index: '8',
						name: '17:00\n17:45',
						value: '17:00-17:45'
					},
					{
						index: '9',
						name: '19:30\n20:15',
						value: '19:30-20:15'
					},
					{
						index: '10',
						name: '20:25\n21:10',
						value: '20:25-21:10'
					},
					{
						index: '11',
						name: '21:20\n22:05',
						value: '21:20-22:05'
					},
				],
				//弹窗属性
				showMyModal: false,
				modalIcon: ['calendar', 'home', 'server-man', 'clock'], //图标
				modalItem: [], //依次是课程名、教室、教师、上课时间--为了适配渲染
			}
		},
		methods: {
			updateData() {
				console.log("开始更新数据")
				uni.showLoading({
					title: '加载中'
				});
				let password = uni.getStorageSync("password")
				let username = uni.getStorageSync("username")
				uni.request({
					url: 'http://124.221.114.237:2333/data/course',
					// url: 'http://localhost:2333/data/course',
					method: 'POST',
					header: {
						'Content-Type': 'application/x-www-form-urlencoded'
					},
					data: {
						username: username,
						token: password,
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
								title: '数据更新成功', //显示的文字
								duration: 2000, //显示多少时间，默认1500毫秒
								icon: "success" //自定义显示的图标，默认成功success，错误error,加载loading，不显示图标是none
							})
						} else {
							uni.showToast({
								title: '数据更新失败，请稍后重试', //显示的文字
								duration: 2000, //显示多少时间，默认1500毫秒
								icon: "error" //自定义显示的图标，默认成功success，错误error,加载loading，不显示图标是none
							})
						}
					},
					fail: (err) => {
						console.log(err);
					}
				});
			},
			removeData() {
				uni.removeStorageSync("username")
				uni.removeStorageSync("password")
				uni.removeStorageSync("token")
				uni.removeStorageSync("course")
				console.log("数据已清除")
				uni.redirectTo({
					url: '/pages/index/login'
				})
			},
			getStartedDay() {
				let nowYear = new Date().getFullYear()
				let nowMonth = new Date().getMonth()
				if (nowMonth > 1 && nowMonth < 8) {
					this.startdDate = concat(nowYear, '2-20')
				} else {
					this.startdDate = nowYear + '8-30'
				}
			},
			getCourse(e) {
				this.controlWeek = e
				let timetables = [
					['', '', '', '', '', '', '', '', '', '', ''],
					['', '', '', '', '', '', '', '', '', '', ''],
					['', '', '', '', '', '', '', '', '', '', ''],
					['', '', '', '', '', '', '', '', '', '', ''],
					['', '', '', '', '', '', '', '', '', '', '']
				]
				if (!uni.getStorageSync("course")) {
					return
				}
				let course = JSON.parse(uni.getStorageSync("course"))
				for (let i = 0; i < 5; i++) {
					for (let j = 0; j < 11; j++) {
						if (course[11 * i + j][0] !== undefined) {
							if (course[11 * i + j][0]['vaildWeeks'][e] == 1) {
								timetables[i][j] = course[11 * i + j][0]['courseName']
							} else {
								timetables[i][j] = ''
							}
						}
					}
				}
				this.timetables = timetables
			},
			getDateDays(date1) {
				let a1 = Date.parse(new Date(date1))
				let a2 = Date.parse(new Date())
				let day = parseInt((a2 - a1) / (1000 * 60 * 60 * 24))
				return day;
			},
			courseClick(re) {
				if (re.name === '') {
					return
				}
				let courseIndex = re.index + 11 * re.weekIndex - 1
				let course = JSON.parse(uni.getStorageSync("course"))
				// console.log(course[courseIndex][0])
				this.modalItem[0] = "课程: " + re.name;
				this.modalItem[1] = "教室: " + course[courseIndex][0].roomName
				this.modalItem[2] = "教师: " + course[courseIndex][0].teacherName
				// this.modalItem[3] = "时间: " + this.timetableType[re.index-1].value
				this.showMyModal = true;
			},
			nextWeekClick(e) {
				console.log("下一周", e)
				this.getCourse(e)
				this.$forceUpdate();
			},
			lastWeekClick(e) {
				console.log("上一周", e)
				this.getCourse(e)
				this.$forceUpdate();
			},
			weekOpenClick() {
				// console.log("点击了第几周")
			},
			weekSelectClick(e) {
				console.log("您选择了", e)
				this.getCourse(e)
			},
			//关闭弹窗
			closeModal() {
				this.showMyModal = this.showMyModal == true ? false : true;
			},
			//返回颜色
			colorList() {
				return [
					"#ffffff", //0
					"#ffaa00", //1
					"#33CC99",
					"#ff5500", //3
					"#789262", //4
					"#66CCCC", //5
					"#9999FF", //6
				]
			}

		},
	}
</script>
<style lang="scss">
	@mixin flex {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
	}

	@mixin height {
		/* #ifndef APP-NVUE */
		height: 100%;
		/* #endif */
		/* #ifdef APP-NVUE */
		flex: 1;
		/* #endif */
	}

	.loading-icon {
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: #000;
		animation: rotate 1s infinite linear;
		/* animation: 3s linear 1s infinite alternate slidein; */
	}

	.loading-text {
		color: #fff;
		margin-top: 10px;
		font-size: 14px;
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

	.box {
		@include flex;
	}

	.button {
		@include flex;
		align-items: center;
		justify-content: center;
		flex: 1;
		height: 35px;
		margin: 0 5px;
		border-radius: 5px;
	}

	.example-body {
		background-color: #fff;
		padding: 10px 0;
	}

	.button-text {
		color: #fff;
		font-size: 12px;
	}

	.popup-content {
		@include flex;
		align-items: center;
		justify-content: center;
		padding: 15px;
		height: 50px;
		background-color: #fff;
	}

	.popup-height {
		@include height;
		width: 200px;
	}

	.text {
		font-size: 12px;
		color: #333;
	}

	.popup-success {
		color: #fff;
		background-color: #e1f3d8;
	}

	.popup-warn {
		color: #fff;
		background-color: #faecd8;
	}

	.popup-error {
		color: #fff;
		background-color: #fde2e2;
	}

	.popup-info {
		color: #fff;
		background-color: #f2f6fc;
	}

	.success-text {
		color: #09bb07;
	}

	.warn-text {
		color: #e6a23c;
	}

	.error-text {
		color: #f56c6c;
	}

	.info-text {
		color: #909399;
	}

	.dialog,
	.share {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
	}

	.dialog-box {
		padding: 10px;
	}

	.dialog .button,
	.share .button {
		/* #ifndef APP-NVUE */
		width: 100%;
		/* #endif */
		margin: 0;
		margin-top: 10px;
		padding: 3px 0;
		flex: 1;
	}

	.dialog-text {
		font-size: 14px;
		color: #333;
	}

	.login-btn {
		background-color: #d40b0f;
		color: #fff;
		padding: 10px;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		width: 100%;
	}

	.update-btn {
		background-color: #0095df;
		color: #fff;
		padding: 10px;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		width: 100%;
	}

	.login {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100vh;

		form {
			display: flex;
			flex-direction: column;

			input {
				margin: 10px 0;
				padding: 10px;
				font-size: 16px;
				border: solid 1px #C9C9C9;
			}

			button {
				margin: 10px 0;
				padding: 10px;
				font-size: 16px;
				color: #fff;
				background-color: #007aff;
				border: none;
			}
		}
	}

	.result {
		display: flex;
		flex-direction: column;
		align-items: center;

		p {
			font-size: 20px;
			margin: 20px 0;
		}

		button {
			padding: 10px;
			font-size: 16px;
			color: #fff;
			background-color: #007aff;
			border: none;
		}
	}
</style>