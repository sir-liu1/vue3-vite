<template>
  <div class="about">
    <button @click="checkIn">打卡</button>
    <button @click="calculate">计算</button>
    <p v-html="str"></p>
  </div>
</template>
<script setup>
import { ref} from 'vue'
const data = ref(0);

function checkIn() {
  const currentTime = new Date().getTime();
  console.log(currentTime);
  const time = getNowFormatDate();
  const timeArr = localStorage.getItem(getNowFormatDate());
  if(timeArr){
     const newTime = JSON.parse(timeArr);
     newTime.push(currentTime);
     localStorage.setItem(time,JSON.stringify(newTime));
  }else{
    const newTime = [currentTime];
    localStorage.setItem(time,JSON.stringify(newTime));
  }
}
let str = ref("");

function calculate() {
  str.value = ""
  console.log('-------');
  
  debugger;
  for (var i=0;i<localStorage.length;i++) {
      const key = localStorage.key(i);
      const timeArr =  JSON.parse(localStorage.getItem(key));
      const start  = timeArr.shift();
      const end = timeArr.pop();
      let time = Math.abs( Number(end) - Number(start))  -(1000 * 60 * 60 *1.5);
      if(isTimeStampAfter6PM(end)){
        time  -=(1000 * 60 * 60 *0.5);
      }
      // if(isTimeInRange(time)){
      //  time-= (minutesPast530(timestamp)/60)*(1000 * 60 * 60)
      // }
       // 将毫秒转换为小时
      let differenceInHours = (time / (1000 * 60 * 60)).toFixed(1);

  
      str.value+=("日期："+key+"  时间:" +differenceInHours+'<br>');
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



function isTimeInRange(timestamp) {
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
[1729555200000,1729601390174,1729602112959,1729602289555,1729602290015,1729602400453]
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
