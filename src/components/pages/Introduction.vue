<template>
  <div class="tag">
    <el-card class="box-card">
      <div slot="header" class="d-flex align-items-center" style="height: 140px">
        <div class="demo-basic--circle">
          <div class="block" style="text-align: center;margin-top: 15%">
            <img src="@/assets/images/avater.png" style="width: 90px; border-radius: 50%;">
          </div>
        </div>
      </div>
      <div>
        既然选择了远方，便只顾风雨兼程🤔
      </div>
    </el-card>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        fits: ['fill', 'contain', 'cover', 'none', 'scale-down'],
        url: ''
      }
    },
    name: 'tag',
    methods: {
      tag(name) {
        this.$router.push({
          name: 'tag',
          params: {
            'name': name
          }
        });
      }
    }
  }
</script>

<style scoped>
  .box-card .item:hover {
    color: #409EFF;
    cursor: pointer;
  }

  .box-card span {
    font-weight: bold;
  }

</style>
