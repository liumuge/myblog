<template>
  <div class="home">
    <Header></Header>

    <el-row id="artList" type="flex" justify="space-around" style="margin-top: 5%">
      <el-col :span="2"></el-col>
      <el-col :span="14" style="width: 55vw">
        <el-row class="art-item" v-for="article in articleList">
          <el-card shadow="hover">
            <h5 style="height: 50px">
              <a>
                <span class="art-title" @click="goto(article.id)">{{article.title}}</span>
              </a>
            </h5>
            <el-row class="art-info d-flex align-items-center justify-content-start">
              <div class="art-time"><i class="el-icon-time"></i>
                <span v-if="article.updateTime!=null">
                  {{article.updateTime | formatDate('yyyy-MM-dd hh:mm:ss')}}
                </span>
                <span v-if="article.updateTime==null">
                  {{article.creatTime | formatDate('yyyy-MM-dd hh:mm:ss')}}
                </span>
              </div>
              <div class="d-flex align-items-center">
                <img class="tag" src="@/assets/images/tag.png"/>：
                <el-tag size="mini" v-for="tag in article.tags" style="margin-left:10px">
                  {{tag.tagName}}
                </el-tag>
              </div>
            </el-row>
            <el-row class="art-body">
              <!-- 显示缩略图 未完成-->
              <div class="side-img hidden-sm-and-down">
                <img class="art-banner"
                     src="@/assets/images/vue.jpg"></div>
              <div class="side-abstract">
                <div class="art-abstract">
                  {{article.content | ellipsis}}
                </div>
                <div class="art-more">
                  <el-button plain @click="goto(article.id)">阅读更多</el-button>
                  <div class="view"><i class="el-icon-view"></i>{{article.views}}</div>
                </div>
              </div>
            </el-row>
          </el-card>
          <img class="star" src="@/assets/images/star.png"/>
        </el-row>
        <el-pagination
          background
          layout="prev, pager, next"
          :total="params.totalCount"
          :page-size="params.pageSize"
          :current-page="params.currentPage"
          @current-change="changePage"
        >
        </el-pagination>
      </el-col>
      <el-col :span="5" class="hidden-sm-and-down" id="side">
        <div class="item">
          <introduction></introduction>
        </div>
        <div class="item">
          <tag></tag>
        </div>
        <div class="item">
          <friend></friend>
        </div>
      </el-col>
      <el-col :span="1"></el-col>
    </el-row>

    <Footer></Footer>
  </div>
</template>
<script>
  import Header from "./Header.vue";
  import Footer from "./Footer.vue";
  import friend from './friend';
  import tag from './tag';
  import Introduction from './Introduction';
  import marked from "marked";

  export default {
    data() {
      return {
        articleList: [],
        params: {
          currentPage: 1,//页码
          pageSize: 5,//每页显示个数
          totalCount: 10,//总记录数
          totalPage: 1,//总页数
        },
        status: 1,
      };
    },
    filters: {
      //限制显示长度
      ellipsis(value) {
        if (!value) {
          return ''
        }
        if (value.length > 310) {
          return value.slice(0, 200) + ' . . . '
        }
        return value
      }
    },
    components: {Header, Footer, friend, tag, Introduction},
    methods: {
      goto(articleId) {
        this.$router.push({
          name: "article",
          params: {
            id: articleId,
          }
        });
      },
      changePage(currentPage) {
        this.params.currentPage = currentPage;
        this.getArticleList(this.params.currentPage, this.params.pageSize);
      },
      getArticleList(currentPage, pageSize) {
        let uId = sessionStorage.getItem("uId");
        status = this.status;
        this.axios
        .get("/api/article/getArticleList", {
          params: {uId, currentPage, pageSize, status}
        })
        .then(res => {
          this.articleList = res.data.queryResult.list[0].list;
          this.params = res.data.queryResult.list[0];
        })
        .catch(error => {
          console.log(error);
        });
      },
    },
    beforeRouteEnter(to, from, next) {
      next(vm => {
        vm.article = to.query.article
      })
    },
    mounted() {
      this.getArticleList(this.params.currentPage, this.params.pageSize);
    }
  };
</script>
<style scoped>
  #side .item {
    margin-bottom: 30px;
  }

  .art-item {
    margin-bottom: 30px;
    position: relative;
  }

  .art-item .star {
    width: 60px;
    height: 60px;
    position: absolute;
    top: 0;
    right: 0;
  }

  img.tag {
    width: 16px;
    height: 16px;
  }

  .art-title {
    border-left: 3px solid #F56C6C;
    padding-left: 5px;
    cursor: pointer;
  }

  .art-title:hover {
    padding-left: 10px;
    color: #409EFF;
  }

  .art-time {
    margin-right: 20px;
  }

  .art-body {
    display: flex;
    padding: 10px 0;
  }

  .side-img {
    height: 150px;
    width: 270px;
    overflow: hidden;
    margin-right: 10px;
  }

  img.art-banner {
    width: 100%;
    height: 100%;
    transition: all 0.6s;
  }

  img.art-banner:hover {
    transform: scale(1.4);
  }

  .side-abstract {
    flex: 1;
    display: flex;
    flex-direction: column;
  }

  .art-abstract {
    flex: 1;
    color: #aaa;
    width: 500px;
    overflow: hidden;
    text-overflow: ellipsis;
  / / 文本溢出显示省略号 white-space: normal;
    word-break: break-all;
  }

  .art-more {
    height: 40px;
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
  }

  .art-more .view {
    color: #aaa;
  }

  h5 {
    font-size: 18px;
  }

  .pagination {
    background-color: #F9F9F9;
  }
</style>
