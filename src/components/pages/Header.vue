<template>
  <div class="header-wraper">
    <header class="blog-header">
      <h1 class="header-title">
        <router-link to="/">StarBlog</router-link>
      </h1>
      <nav class="header-nav">
          <el-input
            placeholder="请输入搜索内容"
            prefix-icon="el-icon-search"
            v-model="search"
            class="nav-search"
            @keyup.enter.native="getArticleList"
          ></el-input>

        <!-- <div class="nav-search">
          <i class="wmui icon-search"></i>
          <input type="text" maxlength="30" value>
        </div>-->
        <ul style="margin-left: 3rem">
          <li v-if="this.username!=null">
            <router-link to="/admin/settings">设置</router-link>
          </li>
          <li v-if="this.username!=null">
            <router-link to="/admin/newEssay">新随笔</router-link>
          </li>
          <li v-if="this.username==null">
            <router-link to="/login">登录</router-link>
          </li>
        </ul>
      </nav>
    </header>
  </div>
</template>
<script>
export default {
  name: "Header",
  data() {
    return {
      search: "",
      username:"",
      params: {
        currentPage: 1,//页码
        pageSize: 5,//每页显示个数
        totalCount: 10,//总记录数
        totalPage: 1,//总页数
      },
      status: 1,
    };
  },
  methods: {
    getName: function () {
      this.username = sessionStorage.getItem("username");
    },
    getArticleList(currentPage, pageSize,search) {
      let uId = sessionStorage.getItem("uId");
      status = this.status;
      currentPage=this.params.currentPage;
      search=this.search;
      pageSize=this.pageSize
      this.axios
      .get("/api/article/search", {
        params: {currentPage, pageSize, status,uId,search}
      })
      .then(res => {
        if(res.data.success){
          this.$router.push({
            path:"/searchShow",
            query:{
              search:search,
            }
          });
        }else {
          this.$message({
            message: "服务器错误,请稍后重试",
            type: "error"
          });
        }
      })
      .catch(error => {
        console.log(error);
      });
    }
  },
  created(){
    this.getName();
  }
};
</script>
<style scoped>
.header-wraper {
  position: fixed;
  top: 0;
  display: flex;
  width: 100%;
  height: 50px;
  line-height: 50px;
  border-bottom: 1px solid #eee;
  z-index: 999;
  background-color: #fff;
}
.blog-header {
  display: flex;
  width: 960px;
  margin: 0 auto;
  justify-content: space-between;
  padding: 0 15px;
}
.header-title {
  font-size: 20px;
  font-weight: 700;
}
.header-nav {
  display: -webkit-box;
  margin-right: 13rem;
}
.blog-header .header-nav ul {
  list-style: none;
}
.blog-header .header-nav li {
  display: inline-block;
}
.blog-header .header-nav li a {
  padding: 0 15px;
}
</style>
