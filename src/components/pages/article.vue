<template>
  <div>
    <Header></Header>
    <el-row class="main" type="flex" justify="center" style="margin-top: 5%">
      <el-col :span="16">
        <el-card>
          <div slot="header">
            <h2>{{article.title}}</h2>
            <el-row class="art-info d-flex align-items-center justify-content-start">
              <div class="art-time"><i class="el-icon-time"></i>
                <span v-if="article.updateTime!=null">
                  {{article.updateTime | formatDate('yyyy-MM-dd hh:mm:ss')}}
                </span>
                <span v-if="article.updateTime==null">
                  {{article.creatTime | formatDate('yyyy-MM-dd hh:mm:ss')}}
                </span>
              </div>
            </el-row>
          </div>
          <div v-html="article.contentHtml">
          </div>
          <div>
            <el-form label-position="top" label-width="80px" :model="formLabelAlign">
              <el-divider></el-divider>
              <el-form-item label="评论">
                <el-input type="textarea" v-model="contentsText"></el-input>
                <el-button type="primary" style="float:right;margin: 2% 3% 0 0">提交</el-button>
              </el-form-item>
            </el-form>
          </div>
        </el-card>
      </el-col>
    </el-row>
    <Footer></Footer>
  </div>
</template>

<script>
  import Header from "./Header.vue";
  import Footer from "./Footer.vue";

  export default {
    name: "article",
    data() {
      return {
        contentsText:'',
        article:'',
        articleId:0,
      }
    },
    methods:{
      getArticle(articleId){
        this.axios
        .get("/api/article/getArticle", {
          params: {articleId}
        })
        .then(res => {
         this.article = res.data.queryResult.list[0];
        })
      }
    },
    components: {Header, Footer},
    created() {
      this.articleId = this.$route.params.id;
      this.getArticle(this.articleId);
    },
  }
</script>

<style scoped>
  #artcle-info {
    padding: 20px;
    background-image: url();
    margin-bottom: 40px;
  }

  #artcle-info .abstract {
    color: #ffffff;
    border-left: 3px solid #F56C6C;
    padding: 10px;
    background-color: rgba(126, 129, 135, 0.3);
  }

  #artcle-info .timeAndView {
    padding: 20px;
    line-height: 30px;
    font-size: 16px;
    color: #ffffff;
  }

  pre.has {
    color: #ffffff;
    background-color: rgba(0, 0, 0, 0.8);
  }

  img.has {
    width: 100%;
  }

  #statement {
    border-left: 3px solid #F56C6C;
    padding: 20px;
    background-color: #EBEEF5;
  }
</style>
