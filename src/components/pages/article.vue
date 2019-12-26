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
              <div class="d-flex align-items-center">
                <img class="tag" src="@/assets/images/tag.png"/>：
                <el-tag size="mini" v-for="tag in article.tags" style="margin-left:10px">
                  {{tag.tagName}}
                </el-tag>
              </div>
            </el-row>
          </div>
          <div v-html="article.contentHtml">
          </div>
          <div>
            <el-form label-position="top" label-width="80px" :model="formLabelAlign">
              <el-divider></el-divider>
              <el-form-item label="评论">
                <el-input type="textarea" v-model="contentsText" rows="3" maxlength="1000"  @input="descInput"></el-input>
                <div style="float:right;margin: 2% 2% 0 0">
                  <span style="font-size: 12px;color: #999999">评论将由博主筛选后显示，对所有人可见 | 还能输入<em>{{txtVal}}</em>个字符 &nbsp;&nbsp;<el-button
                    type="primary" size="small" style="margin-left:15px" @click="addComment">发表评论</el-button> </span>
                </div>
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
        contentsText: '',
        article: '',
        articleId: 0,
        txtVal: 1000,
      }
    },
    methods: {
      descInput() {
        this.txtVal = 1000-this.contentsText.length;
      },
      getArticle(articleId) {
        this.axios
        .get("/api/article/getArticle", {
          params: {articleId}
        })
        .then(res => {
          this.article = res.data.queryResult.list[0];
        })
      },
      addComment(){
        let uId = sessionStorage.getItem("uId");
        this.axios
        .post("/api/comment/addComment", {
          articleId:this.articleId,
          comment:this.contentsText,
          uid:uId,
        })
        .then(res => {
          if ((res.data.success)) {
            this.$message({
              message: "发表成功,评论将由博主筛选后显示，对所有人可见",
              type: "success"
            });
            this.contentsText="";
          } else {
            this.$message.error("发表失败,请稍后重试");
          }
        })
      },
      updateViews(articleId){
        this.axios
        .get("/api/article/updateViews", {
          params: {articleId}
        })
      }
    },
    components: {Header, Footer},
    created() {
      if (this.$route.params.id!=null){
        sessionStorage.setItem("articleId", this.$route.params.id);
      }
      this.articleId = sessionStorage.getItem("articleId")
      this.updateViews(this.articleId);
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

  img.tag {
    width: 16px;
    height: 16px;
  }

  #statement {
    border-left: 3px solid #F56C6C;
    padding: 20px;
    background-color: #EBEEF5;
  }
</style>
