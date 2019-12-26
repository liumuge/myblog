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
              :data="articleList"
              border
              style="width: 100%">
              <el-table-column
                prop="creatTime"
                label="创建时间"
                width="150">
              </el-table-column>
              <el-table-column
                prop="updateTime"
                label="最近更新时间"
                width="150">
              </el-table-column>
              <el-table-column
                prop="title"
                label="标题"
                width="120">
              </el-table-column>
              <el-table-column
                label="内容简介"
                width="280">
                <template slot-scope="articleContent">
                  {{articleContent.row.content | ellipsis}}
                </template>
              </el-table-column>
              <el-table-column
                prop="city"
                label="状态"
                width="100">
              </el-table-column>
              <el-table-column
                prop="views"
                label="访问量"
                width="100">
              </el-table-column>
              <el-table-column
                fixed="right"
                label="操作"
                width="170">
                <template slot-scope="article">
                  <el-button type="text" size="small">置顶</el-button>
                  <el-button @click="goto(article.row.id)" type="text" size="small">查看</el-button>
                  <el-button @click="check(article.row.id)" type="text" size="small">编辑</el-button>
                  <el-button @click="removeArticle(article.row.id)"type="text" size="small">删除</el-button>
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
        articleList: [],
        params: {
          currentPage: 1,//页码
          pageSize: 6,//每页显示个数
          totalCount: 10,//总记录数
          totalPage: 1,//总页数
        },
        status: 1,
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
          return value.slice(0, 25) + ' . . . '
        }
        return value
      }
    },
    methods: {
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
      changePage(currentPage) {
        this.params.currentPage = currentPage;
        this.getArticleList(this.params.currentPage, this.params.pageSize);
      },
      goto(articleId) {
        this.$router.push({
          name: "article",
          params: {
            id: articleId,
          }
        });
      },
      removeArticle(articleId) {
        this.axios
        .get("/api/article/removeArticle", {
          params: {articleId,}
        })
        .then(res => {
          if ((res.data.success)) {
            console.log(this.params.currentPage)
            this.$message({
              message: "删除成功!",
              type: "success",
              onClose:this.getArticleList(this.params.currentPage, this.params.pageSize)
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
      check(articleId){
        this.$router.push({
          name: "newEssay",
          params: {
            id: articleId,
          }
        });
      }
    },
    mounted() {
      this.getArticleList(this.params.currentPage, this.params.pageSize);
    }
  }
</script>

<style scoped>

</style>
