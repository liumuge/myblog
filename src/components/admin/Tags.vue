<template>
  <section class="blog-body">
    <div v-if="creatTime!=null">
      <el-card class="box-card">
        <div slot="header" class="clearfix">
          <h2 style="height: 50px">
            <a>
              <span class="art-title" @click="goto(articleId)">{{articleTitle}}</span>
            </a>
          </h2>
          <i class="el-icon-time"></i>
          <span>{{creatTime}}</span>
        </div>
        <div>
          <h3>评论内容:</h3><br/>
          <p>{{comment}}</p>
        </div>
      </el-card>
    </div>
    <div v-if="creatTime==null">
      <el-tag
        :key="index"
        v-for="(tag,index) in Tags"
        closable
        :disable-transitions="false"
        @close="delTag(tag.id)">{{tag.tagName}}
      </el-tag>
      <el-input
        class="input-new-tag"
        v-if="inputVisible"
        v-model="inputValue"
        ref="saveTagInput"
        size="small"
        @keyup.enter.native="addTag"
      ></el-input>
      <el-button v-else class="button-new-tag" size="small" @click="showInput">+ New Tag</el-button>
    </div>

  </section>
</template>
<script>
  export default {
    name: "tags",
    data() {
      return {
        Tags: [],
        inputVisible: false,
        inputValue: "",
        creatTime: "",
        articleTitle: "",
        comment: "",
        articleId:"",
      };
    },
    methods: {
      delTag(id) {
        this.axios.delete("/api/tag/delTag/" + id)
        .then(res => {
          // this.Tags.splice(this.Tags);
          if (res.data.success) {
            this.$message({
              message: "删除成功",
              type: "success"
            });
            this.getTagAll();
          }
        })
        .catch(error => {
          console.log(error);
        });
      },

      showInput() {
        this.inputVisible = true;
        this.$nextTick(_ => {
          this.$refs.saveTagInput.$refs.input.focus();
        });
      },

      addTag() {
        let inputValue = this.inputValue;
        if (inputValue.length < 1) {
          this.$message({
            message: "内容不能为空",
            type: "warning"
          });
          return;
        }
        let uId = sessionStorage.getItem("uId");
        console.log(uId, 1111)
        this.axios.post("/api/tag/addTag", {
          tagName: inputValue,
          uid: uId
        })
        .then(res => {
          if (res.data.success) {
            this.$message({
              message: "添加成功",
              type: "success"
            });
            this.getTagAll();
          }
          if (inputValue) {
            this.Tags.push(inputValue);
          }
          this.inputVisible = false;
          this.inputValue = "";
        })
        .catch(error => {
          console.log(error);
        });
      },
      getTagAll() {
        let uId = sessionStorage.getItem("uId");
        this.axios
        .get("/api/tag/getTagAll/" + uId)
        .then(res => {
          this.Tags = res.data.queryResult.list;
        })
        .catch(error => {
          console.log(error);
        });
      },
      goto(articleId) {
        this.$router.push({
          name: "article",
          params: {
            id: articleId,
          }
        });
      },
    },
    mounted() {
      this.articleId=this.$route.params.articleId;
      this.creatTime = this.$route.params.creatTime;
      this.articleTitle = this.$route.params.articleTitle;
      this.comment = this.$route.params.comment;
      this.getTagAll();
    }
  };
</script>
<style scoped>
  .el-tag,
  .el-tag {
    margin-left: 10px;
  }

  .button-new-tag {
    margin-left: 10px;
    height: 32px;
    line-height: 30px;
    padding-top: 0;
    padding-bottom: 0;
  }
  .art-title {
    border-left: 3px solid #F56C6C;
    padding-left: 5px;
    cursor: pointer;
  }
  .input-new-tag {
    width: 90px;
    margin-left: 10px;
    vertical-align: bottom;
  }
</style>

