<template>
  <div class="about">
    <el-button type="primary" @click="checkIn">打卡</el-button>
    <el-button type="success" @click="calculate">计算</el-button>
    <div>
      <el-table :data="arr" style="width: 100%">
    <el-table-column prop="date" label="Date" ></el-table-column>
    <el-table-column prop="start" label="start" ></el-table-column>
    <el-table-column prop="end" label="end" ></el-table-column>
    <el-table-column prop="time" label="Time (hours)" ></el-table-column>
  </el-table>
    </div>
  </div>
</template>
<script setup>
import { ElMessage } from 'element-plus'
import { ref, onMounted } from 'vue'

onMounted(() => {

  console.log(calculate());


})
const data = ref(0);

function checkIn() {
  const currentTime = new Date().getTime();
  console.log(currentTime);
  const time = getNowFormatDate();
  const timeArr = localStorage.getItem(getNowFormatDate());
  if (timeArr) {
    const newTime = JSON.parse(timeArr);
    newTime.push(currentTime);
    localStorage.setItem(time, JSON.stringify(newTime));
  } else {
    const newTime = [currentTime];
    localStorage.setItem(time, JSON.stringify(newTime));
  }
  ElMessage({
    message: '打卡成功',
    type: 'success',
  })
}
let str = ref("");
const arr = ref([]);
function calculate() {
  str.value = ""
  arr.value = [];
  console.log('-------');

  debugger;
  for (var i = 0; i < localStorage.length; i++) {
    const key = localStorage.key(i);
    const timeArr = JSON.parse(localStorage.getItem(key));
    const start = timeArr.shift();
    let end = timeArr.pop();
    if (isTimeInRange(end)) {
      end = new Date(`${key} 17:30`).getTime();
    }
    let time = Math.abs(Number(end) - Number(start)) - (1000 * 60 * 60 * 1.5);
    if (isTimeStampAfter6PM(end)) {
      time -= (1000 * 60 * 60 * 0.5);
    }

    // 将毫秒转换为小时
    let differenceInHours = (time / (1000 * 60 * 60)).toFixed(1);
    str.value += ("日期：" + key + "  时间:" + differenceInHours + '<br>');
    arr.value.push({
      date: key,
      time: differenceInHours,
      start: convertTimestampToTime(start),
      end: convertTimestampToTime(end)
    })
  }
}

function minutesPast530(timestamp) {
  const date = new Date(timestamp);
  const hours = date.getHours();
  const minutes = date.getMinutes();
  if (hours === 17 && minutes >= 30 && hours < 18) {
    return minutes - 30;
  } else {
    return -1;
  }
}
function convertTimestampToTime(timestamp) {
  const date = new Date(timestamp);
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, '0');
  const day = String(date.getDate()).padStart(2, '0');
  const hours = String(date.getHours()).padStart(2, '0');
  const minutes = String(date.getMinutes()).padStart(2, '0');
  const seconds = String(date.getSeconds()).padStart(2, '0');
  return `${hours}:${minutes}`;
}



function isTimeInRange(timestamp) {
  debugger
  const date = new Date(timestamp);
  const hours = date.getHours();
  const minutes = date.getMinutes();
  return hours === 17 && minutes >= 30 && hours < 18;
}
function isTimeStampAfter6PM(timestamp) {
  const date = new Date(timestamp);
  const hours = date.getHours();
  return hours >= 18;
}
function getNowFormatDate() {
  let date = new Date(),
    year = date.getFullYear(), //获取完整的年份(4位)
    month = date.getMonth() + 1, //获取当前月份(0-11,0代表1月)
    strDate = date.getDate() // 获取当前日(1-31)
  if (month < 10) month = `0${month}` // 如果月份是个位数，在前面补0
  if (strDate < 10) strDate = `0${strDate}` // 如果日是个位数，在前面补0

  return `${year}-${month}-${strDate}`
}
</script>
<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
