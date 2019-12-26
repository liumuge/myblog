<template>
  <div class="header-wraper">
    <el-row style="width:100%">
      <el-col :span="3" :offset="4">
        <h1 class="header-title">
          <router-link to="/">StarBlog</router-link>
        </h1>
      </el-col>
      <el-col :span="4" :offset="2">
        <el-input
          placeholder="请输入搜索内容"
          prefix-icon="el-icon-search"
          v-model="search"
          class="nav-search"
          @keyup.enter.native="getArticleList"
        ></el-input>
      </el-col>
      <el-col :span="9" :offset="2">
        <nav class="header-nav">
          <ul>
            <li>
              <router-link to="/admin/settings">设置</router-link>
            </li>
            <li>
              <router-link to="/admin/management">管理</router-link>
            </li>
            <li>
              <router-link to="/admin/comments">评论</router-link>
            </li>
            <li>
              <router-link to="/admin/draft">草稿</router-link>
            </li>
            <li>
              <router-link to="/admin/tags">标签</router-link>
            </li>
            <li>
              <router-link to="/admin/newEssay">随笔</router-link>
            </li>
            <li>
              {{this.username}} ,
              <router-link @click.native="exit" to="/login">退出</router-link>
            </li>
          </ul>
        </nav>
      </el-col>
    </el-row>
  </div>
</template>
<script>
  export default {
    name: "Header",
    data() {
      return {
        search: "",
        username: "",
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
              name:"searchShow",
              params:{
                search:search,
                articleList:res.data.queryResult.list[0].list,
                params:res.data.queryResult.list[0]
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
      },
      exit() {
        sessionStorage.clear();
      }
    },
    created() {
      this.username=sessionStorage.getItem("username")
    }
  }
  ;
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
    margin-right: 6rem;
  }

  li {
    display: inline-block;
    margin-right: 1.4rem;
    cursor: pointer;
  }
</style>
