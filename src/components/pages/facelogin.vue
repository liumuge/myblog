<template>
  <div class="login-form" v-show="canvasShow">
    <p class="titleInfo">请把正脸对准摄像头</p>
    <div class="regInfo">
      <!-- <div class="tip">没有检测到脸</div> -->
      <div class="canvas">
        <!--video用于显示媒体设备的视频流，自动播放-->
        <video ref="vd" id="video" autoplay playsinline style="width: 200px;height: 200px"></video>
        <!--描绘video截图-->
        <canvas ref="cav" id="canvas" width="200" height="200"></canvas>
      </div>
    </div>
    <form id="myForm" action="/api/frame/facelogin/checkFace" method="post" ref="myForm" enctype="multipart/form-data">
    </form>
    <!-- <p style="text-align:center;padding:50px 0 0"><el-button id="capture" ref="capture" @click='handleClick'>拍 照</el-button> </p> -->
    <!-- <img :src='imgUrl' width="100" height="100"> -->
  </div>
</template>
<script>

</script>
<style>

</style>
