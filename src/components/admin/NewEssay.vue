<template>
  <section class="blog-body">
    <div class="admin-publish">
      <el-input v-model="title" placeholder="文章标题" autofocus></el-input>
      <div id="editor" style="text-align:left"></div>
      <mavon-editor
        v-model="context"
        :toolbars="toolbars"
        @imgAdd="$imgAdd"
        class="blogcontent"
        ref=md
      />
      <el-row style="margin-bottom:20px">
        <el-col :span="16">
          <el-select
            v-model="tags"
            multiple
            :popper-append-to-body="false"
            placeholder="请选择标签"
            style="width:100%"
          >
            <el-option v-for="tag in options" :key="tag.id" :label="tag.tagName"
                       :value="tag.id"></el-option>
          </el-select>
        </el-col>
        <el-col :span="6" :offset="2">
          <el-button type="primary" size="medium" @click="saveArticle">存为草稿</el-button>
          <el-button type="primary" size="medium" @click="publish">发布</el-button>
        </el-col>
      </el-row>
    </div>
  </section>
</template>
<script>
  export default {
    data() {
      return {
        title: "",
        context: "", //输入的数据
        toolbars: {
          bold: true, // 粗体
          italic: true, // 斜体
          header: true, // 标题
          underline: true, // 下划线
          mark: true, // 标记
          superscript: true, // 上角标
          quote: true, // 引用
          ol: true, // 有序列表
          link: true, // 链接
          imagelink: true, // 图片链接
          help: true, // 帮助
          code: true, // code
          subfield: true, // 是否需要分栏
          fullscreen: true, // 全屏编辑
          readmodel: true, // 沉浸式阅读
          undo: true, // 上一步
          trash: true, // 清空
          save: true, // 保存（触发events中的save事件）
          htmlcode: true, // 展示html源码
          navigation: true // 导航目录
        },
        options: [],
        tags: [],
        username: "",
        articleId: 0,
      };
    },
    methods: {
      publish() {
        if (this.title.length < 1) {
          this.$message({
            message: "标题不能为空",
            type: "warning"
          });
        } else if (this.context.length < 1) {
          this.$message({
            message: "内容不能为空",
            type: "warning"
          });
        } else if (this.tags.length < 1) {
          this.$message({
            message: "请选择标签",
            type: "warning"
          });
        } else {
          let html = this.$refs.md.d_render;
          let uId = sessionStorage.getItem("uId");
          this.axios
          .post("/api/article/addArticle", {
            uid: uId,
            title: this.title,
            content: this.context,
            contentHtml: html,
            tags: this.tags,
            status: 1,
            id: this.articleId
          })
          .then(res => {
            if ((res.data.success)) {
              this.$message({
                message: "发布成功",
                type: "success"
              });
              this.title = "";
              this.context = "";
              this.tags = [];
            } else {
              this.$message.error(res.data.message);
            }
          })
          .catch(error => {
            console.log(error);
          });
        }
      },
      saveArticle() {
        if (this.title.length < 1) {
          this.$message({
            message: "标题不能为空",
            type: "warning"
          });
        } else if (this.context.length < 1) {
          this.$message({
            message: "内容不能为空",
            type: "warning"
          });
        } else if (this.tags.length < 1) {
          this.$message({
            message: "请选择标签",
            type: "warning"
          });
        } else {
          let html = this.$refs.md.d_render;
          let uId = sessionStorage.getItem("uId");
          this.axios
          .post("/api/article/addArticle", {
            uid: uId,
            title: this.title,
            content: this.context,
            contentHtml: html,
            tags: this.tags,
            status: 0,
            id: this.articleId
          })
          .then(res => {
            if ((res.data.success)) {
              this.$message({
                message: "保存成功",
                type: "success"
              });
              this.title = "";
              this.context = "";
              this.tags = [];
            } else {
              this.$message.error(res.data.message);
            }
          })
          .catch(error => {
            console.log(error);
          });
        }
      },
      // 绑定@imgAdd event
      $imgAdd(pos, $file) {
        // 第一步.将图片上传到服务器.
        var formdata = new FormData();
        formdata.append('image', $file);
        this.axios({
          url: '/api/filesUpload/imagesUpload',
          method: 'post',
          data: formdata,
          headers: {'Content-Type': 'multipart/form-data'},
        }).then(res => {
          console.log(res)
          // 第二步.将返回的url替换到文本原位置![...](0) -> ![...](url)
          /**
           * $vm 指为mavonEditor实例，可以通过如下两种方式获取
           * 1. 通过引入对象获取: `import {mavonEditor} from ...` 等方式引入后，`$vm`为`mavonEditor`
           * 2. 通过$refs获取: html声明ref : `<mavon-editor ref=md ></mavon-editor>，`$vm`为 `this.$refs.md`
           */
            // 第二步.将返回的url替换到文本原位置![...](0) -> ![...](url)
          let url = res.data.url.replace("D:\\WorkingArea\\WebstormWorkSpace\\myblog\\src",
            "../..");
          this.$refs.md.$img2Url(pos, url.replace(/\\/g, "/"));

        })
      },
      imgDel(pos) {
        delete this.img_file[pos];
      },
      getTagAll() {
        let uId = sessionStorage.getItem("uId");
        this.axios
        .get("/api/tag/getTagAll/" + uId)
        .then(res => {
          this.options = res.data.queryResult.list;
        })
        .catch(error => {
          console.log(error);
        });
      },
      getArticle(articleId) {
        if (articleId != 0) {
          this.axios
          .get("/api/article/getArticle", {
            params: {articleId}
          })
          .then(res => {
            this.title = res.data.queryResult.list[0].title;
            this.context = res.data.queryResult.list[0].content;
            for (var i in res.data.queryResult.list[0].tags) {
              this.tags.push(res.data.queryResult.list[0].tags[i].id);
            }
          })
        }
      }
    }
    ,
    mounted() {
      this.username = sessionStorage.getItem("username");
      this.getTagAll();
      this.articleId = this.$route.params.id;
      this.getArticle(this.articleId);
    }
  }
  ;
</script>
<style scoped>
  .blogcontent {
    height: 400px;
    margin-top: 20px;
    margin-bottom: 20px;
  }

  span {
    margin-right: 5px;
    cursor: pointer;
  }
</style>
