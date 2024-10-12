<template>
  <div class="main">
    <el-row>
      <el-col :span="12" class="play">
        <div class="song-name"></div>
        <div class="song-imag"></div>
        <div class="song-"></div>
      </el-col>
      <el-col :span="12" class="song"></el-col>
    </el-row>
  </div>
</template>

<script setup>

</script>

<style lang="less">
body {
  margin: 0;
  padding: 0;
  .song-name {
    height: 100px;
  }
}
.main {
  width: 100vw;
  height: 100vh;
  background-color: aqua;
}
</style>
