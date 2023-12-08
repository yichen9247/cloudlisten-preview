
<script setup>
  import "./assets/app.css"
  import { message } from 'ant-design-vue'
  import { reactive,onMounted,watch } from "vue"
  onMounted(() => setInterval(() => getStatistics(),1000));

  const counte = reactive({ counter_1: 0, counter_2: 0, counter_3: 0 });
  const target = reactive({ target_1: 1000,target_2: 1000,target_3: 1000 });
  const status = reactive({ status_1: false, status_2: false, status_3: false });

  const frequency = 20;
  let firstStatus = false;
  let intervalId1 = () => {};
  let intervalId2 = () => {};
  let intervalId3 = () => {};
  const xmlhttp = new XMLHttpRequest();

  const getStatistics = () => {
    xmlhttp.open("GET","https://cloudlisten-api.yunair.cn/com/cloudlisten/music/listener/api/statistics.php",true);
    xmlhttp.onreadystatechange = () => {
      if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
        var data = JSON.parse(xmlhttp.responseText);
        target.target_1 = data.app_run;
        target.target_2 = data.app_reg;
        if (!firstStatus) {
          firstStatus = true;
          intervalId1 = setInterval(incrementCounter1,frequency);
          intervalId2 = setInterval(incrementCounter2,frequency);
          intervalId3 = setInterval(incrementCounter3,frequency);
        }
      }
    }
    xmlhttp.onerror = () => {
      message.error("系统提示：数据获取失败！", 2);
    }
    xmlhttp.send();
  }

  const incrementCounter1 = () => {
    counte.counter_1++;
    if (counte.counter_1 == target.target_1) {
        status.status_1 = true;
        clearInterval(intervalId1);
        counte.counter_1 = target.target_1;
    }
  }

  const incrementCounter2 = () => {
    counte.counter_2++;
    if (counte.counter_2 == target.target_2) {
        status.status_2 = true;
        clearInterval(intervalId2);
        counte.counter_2 = target.target_2;
    }
  }

  const incrementCounter3 = () => {
    counte.counter_3++;
    if (counte.counter_3 == target.target_3) {
        status.status_3 = true;
        clearInterval(intervalId3);
        counte.counter_3 = target.target_3;
    }
  }

  // eslint-disable-next-line no-unused-vars
  watch(target,(_newValue,_oldValue) => {
    if (status.status_1 && status.status_2) {
      counte.counter_1 = target.target_1;
      counte.counter_2 = target.target_2;
      counte.counter_3 = target.target_3;
      message.success('系统提示：数据已更新成功！', 2);
    }
  },{ deep: true });
</script>

<template>
  <div id="main-content">
    
    <div class="header">
      <div class="container-fluid">
          <div class="header-body"></div>
      </div>
    </div>

    <div class="container">
        <div class="container-fluid">
            <span class="container-text">云听Cloudlisten数据可视化</span>
        </div>
    </div>

    <div class="content">
        <div class="container-fluid">
            <div class="container-box">
                <span class="box-name">启动次数：</span>
                <span class="box-number box_1" id="box_1">{{ counte.counter_1 }}</span>
            </div>

            <div class="container-box">
                <span class="box-name">使用人数：</span>
                <span class="box-number box_2" id="box_1">{{ counte.counter_2 }}</span>
            </div>

            <div class="container-box">
                <span class="box-name">好评人数：</span>
                <span class="box-number box_3" id="box_1">{{ counte.counter_3 }}</span>
            </div>
        </div>
    </div>

    <div class="footer">
        <div class="container">
            <span class="copyright-text">Copyright 2022 -2023 云听Cloudlisten</span>
        </div>
    </div>
  </div>
</template>