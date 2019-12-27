<template>
  <div style="margin-top: 5%">
    <el-row class="main" type="flex" justify="center" style="margin-top: 5%">
      <el-col :span="18">
        <el-card>
          <div>
            <div class="block" style="margin-bottom: 1%">
              <span class="demonstration" style="font-size: 20px">选择日期</span>
              <br>
              <el-date-picker
                v-model="value2"
                type="datetimerange"
                :picker-options="pickerOptions"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
                align="right">
              </el-date-picker>
              <el-input
                placeholder="请输入搜索内容"
                prefix-icon="el-icon-search"
                v-model="search"
                autocomplete="off"
                name="aa"
                style="width: 28%;margin:0 1% 0 1%"></el-input>
              <el-button type="primary" size="medium" v-on:click="" :loading="false">查 询</el-button>
            </div>

            <el-table
              :data="commentList"
              border
              style="width: 100%">
              <el-table-column
                prop="creatTime"
                label="时间"
                width="200">
              </el-table-column>
              <el-table-column
                prop="articleTitle"
                label="文章标题"
                width="200">
              </el-table-column>
              <el-table-column
                label="评论内容"
                width="350">
                <template slot-scope="Comment">
                  {{Comment.row.comment | ellipsis}}
                </template>
              </el-table-column>
              <el-table-column
                label="状态"
                width="120">
                <template slot-scope="commentStatus">
                  <span v-if="commentStatus.row.status==1">通过</span>
                  <span v-if="commentStatus.row.status==0">未审核</span>
                </template>
              </el-table-column>
              <el-table-column
                fixed="right"
                label="操作"
                width="170">
                <template slot-scope="comment">
                  <el-button @click="reviewComment(comment.row.id)" type="text" size="small">通过</el-button>
                  <el-button @click="goto(comment.row.articleId,comment.row.creatTime,comment.row.articleTitle,comment.row.comment)" type="text" size="small">查看</el-button>
                  <el-button @click="removeComment(comment.row.id)"type="text" size="small">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
            <el-pagination
              background
              layout="prev, pager, next"
              :total="params.totalCount"
              :page-size="params.pageSize"
              :current-page="params.currentPage"
              @current-change="changePage"
              style="float:right;margin: 2% 3% 2% 0"
            >
            </el-pagination>
          </div>
        </el-card>
      </el-col>
    </el-row>

  </div>
</template>

<script>
  export default {
    data() {
      return {
        list:null,
        commentList: [],
        params: {
          currentPage: 1,//页码
          pageSize: 6,//每页显示个数
          totalCount: 10,//总记录数
          totalPage: 1,//总页数
        },
        pickerOptions: {
          shortcuts: [{
            text: '最近一周',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近一个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近三个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit('pick', [start, end]);
            }
          }]
        },
        value1: [new Date(2000, 10, 10, 10, 10), new Date(2000, 10, 11, 10, 10)],
        value2: ''
      }
    },
    filters: {
      //限制显示长度
      ellipsis(value) {
        if (!value) {
          return ''
        }
        if (value.length > 50) {
          return value.slice(0, 95) + ' . . . '
        }
        return value
      }
    },
    methods: {
      getCommentList(currentPage, pageSize) {
        let uId = sessionStorage.getItem("uId");
        status = this.status;
        this.axios
        .get("/api/comment/findAll", {
          params: {currentPage, pageSize,uId}
        })
        .then(res => {
          this.commentList = res.data.queryResult.list[0].list;
          this.params = res.data.queryResult.list[0];
        })
        .catch(error => {
          console.log(error);
        });
      },
      changePage(currentPage) {
        this.params.currentPage = currentPage;
        this.getCommentList(this.params.currentPage, this.params.pageSize);
      },
      reviewComment(commentId) {
        this.axios
        .get("/api/comment/reviewComment", {
          params: {commentId}
        })
        .then(res => {
          if ((res.data.success)) {
            this.$message({
              message: "审核成功!",
              type: "success",
              onClose:this.getCommentList(this.params.currentPage, this.params.pageSize)
            });
          }
        })
        .catch(error => {
          this.$message({
            message: "审核失败!",
            type: "error"
          });
          console.log(error);
        });
      },
      removeComment(commentId) {
        this.axios
        .get("/api/comment/removeComment", {
          params: {commentId,}
        })
        .then(res => {
          if ((res.data.success)) {
            this.$message({
              message: "删除成功!",
              type: "success",
              onClose:this.getCommentList(this.params.currentPage, this.params.pageSize)
            });
          }
        })
        .catch(error => {
          this.$message({
            message: "删除失败!",
            type: "error"
          });
          console.log(error);
        });
      },
      goto(articleId,creatTime,articleTitle,comment){
        this.$router.push({
          name: "tags",
          params: {
            articleId:articleId,
            creatTime: creatTime,
            articleTitle:articleTitle,
            comment:comment,
          }
        });
      }
    },
    mounted() {
      this.getCommentList(this.params.currentPage, this.params.pageSize);
    }
  }
</script>

<style scoped>

</style>
