<template>
  <div class="tag">
    <el-card class="box-card">
      <div slot="header" class="d-flex align-items-center" style="height: 140px">
        <div class="demo-basic--circle">
          <div class="block" style="text-align: center;margin-top: 15%">
            <img :src="avatar" style="width: 90px; border-radius: 50%;">
          </div>
        </div>
      </div>
      <div>
        {{signature}}
      </div>
    </el-card>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        avatar:'../../assets/images/avater.png',
        signature:'',
      }
    },
    methods: {
      getUser() {
        let id = sessionStorage.getItem("uId");
        this.axios
        .get("/api/user/findById", {
          params: {id}
        })
        .then(res => {
          this.avatar=res.data.queryResult.list[0].avatar;
          this.signature = res.data.queryResult.list[0].signature;
        })
        .catch(error => {
          console.log(error);
        });
      }
    },
    created() {
      this.getUser();
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
