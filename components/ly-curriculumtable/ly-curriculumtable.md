## Timetable

#### props

| 参数    | 说明                             | 类型            | 默认值  | 可选值                       |
| ----------- | ------------------------------------ | ------------------- | ------- | :------------------------------- |
| timetableType | 定制表格左侧的序号和时间 | Array | 看源码... | -                                |
| week | 定制表格上方的周          | Array         | ['一', '二', '三', '四', '五', '六', '日'] | -                          |
| timetables | 课程内容       | Array              | []     | -                                |
| palette | 调色板, 会优先使用调色板的颜色     | Array              | []     | -                                |
| startdDate | 开始周的时间,    | string              |  默认获取当前时间    | -                                |
| weekButton | 是否开启上一周下一周按钮     | Boolean              | false     |             false/true                   |
| weekTableState | 开启周课表 （点击第几周时触发）    | Boolean              | false     |             false/true                   |
| weekNumbers | 周课表一共显示的周数    |  Number, String             | 1     |            -                  |
| controlWeek | 根据开始时间显示第几周 如：开始时间是2022/09/05 显示第二周 就是2022/09/12     | Number, String              | 1     |            -                 |       |


#### event

| 事件名       | 说明               |返回值               |
| ----------- | ------------------ |------------------ |
| courseClick | 点击课程时触发, 参数course, 内容如下 |
| lastWeekClick | 点击上一周触发 | 返回周数|
| nextWeekClick | 点击下一周触发 |返回周数| 
| weekOpenClick | 点击第几周触发 |无| 
| weekSelectClick | 点击周课表是触发，注意：需开启周课表 |返回周数| 

#### course

```javascript
{
  index: 3,               // 这是第几节课
  length: 2,              // 这节课的课节数
  weekIndex: 4,           // 周数组的下标
  week: '五',             // 周五
  name: '数据结构与算法分析' // 课程名称
}
```



#### 使用

```vue
<template>
	<view>
		<ly-curriculumtable  :weekTableState="true" :weekTableNumber="weekNumbers" 
		 :controlWeek="controlWeek" :timetableType="timetableType" 
		 :timetables="timetables" :startdDate="startdDate" :weekButton="weekButton" 
		 @nextWeekClick="nextWeekClick" @lastWeekClick="lastWeekClick" 
		 @courseClick="courseClick" @weekSelectClick="weekSelectClick" 
		 @weekOpenClick="weekOpenClick">
		 
		 </ly-curriculumtable> 
	</view>
</template>
<script>
	    export default {
	        data() {
	            return {
					weekNumbers:20,//一共显示几周
					controlWeek:1,//显示的第几周
					weekButton:true,//开启上一周下一周按钮
					startdDate:'', //开始时间  默认为当前时间
	                timetables: [
	                  ['大学英语', '大学英语', '大学英语', '', '', '', '毛概', '毛概',],
	                  ['', '', '信号与系统', '信号与系统', '模拟电子技术基础', '模拟电子技术基础', '模拟电子技术基础', '模拟电子技术基', '', '', '', '信号与系'],
	                  ['大学体育', '大学体育', '形势与政策', '形势与政策', '', '', '', '', ''],
	                  ['', '', '', '', '电装实习', '电装实习', '', '', ''],
	                  ['', '', '数据结构与算法分析数据结构与算法分析', '数据结构与算法分析数据结构与算法分析', '', '', '', '', '信号与系'],
	                ],
					timetableType:[ 
					   { index: '1', name: '08:00\n08:40' },
					   { index: '2', name: '08:50\n09:30' },
					   { index: '3', name: '09:40\n10:20' },
					   { index: '4', name: '10:30\n11:10' },
					   { index: '5', name: '11:20\n12:00' },
					   { index: '6', name: '14:00\n14:40' },
					   { index: '7', name: '15:50\n16:30' },
					   { index: '8', name: '16:40\n17:20' },
					   { index: '9', name: '17:30\n18:10' },
					   { index: '10', name: '19:00\n19:40'},
					   { index: '11', name: '20:50\n21:30'},
					   { index: '12', name: '21:40\n22:20'}]
	            }
	        },
			methods:{
				courseClick(re){
					console.log(re)
				},
				nextWeekClick(e){
					console.log("下一周",e)
				},
				lastWeekClick(e){
					console.log("上一周",e)
				},
				weekOpenClick(){
					console.log("点击了第几周")
				},
				weekSelectClick(e){
					console.log("您选择了",e)
				},
				
	    },
	}
</script>



```
