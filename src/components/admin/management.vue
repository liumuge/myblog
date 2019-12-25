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
              :data="tableData"
              border
              style="width: 100%">
              <el-table-column
                prop="date"
                label="创建时间"
                width="150">
              </el-table-column>
              <el-table-column
                prop="date"
                label="最近更新时间"
                width="150">
              </el-table-column>
              <el-table-column
                prop="name"
                label="用户姓名"
                width="120">
              </el-table-column>
              <el-table-column
                prop="province"
                label="内容简介"
                width="280">
              </el-table-column>
              <el-table-column
                prop="city"
                label="状态"
                width="100">
              </el-table-column>
              <el-table-column
                prop="address"
                label="访问量"
                width="100">
              </el-table-column>
              <el-table-column
                fixed="right"
                label="操作"
                width="170">
                <template slot-scope="scope">
                  <el-button type="text" size="small">置顶</el-button>
                  <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
                  <el-button type="text" size="small">编辑</el-button>
                  <el-button type="text" size="small">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
            <el-pagination
              background
              layout="prev, pager, next"
              :total="1000"
              style="float:right;margin: 2% 0 2% 0">
            </el-pagination>
          </div>
        </el-card>
      </el-col>
    </el-row>

  </div>
</template>

<script>
  export default {
    methods: {
      handleClick(row) {
        console.log(row);
      }
    },

    data() {
      return {
        tableData: [{
          date: '2016-05-02',
          name: '王小虎',
          province: '上海',
          city: '普陀区',
          address: '上海市',
          zip: 200333
        }, {
          date: '2016-05-04',
          name: '王小虎',
          province: '上海',
          city: '普陀区',
          address: '上海市',
          zip: 200333
        }, {
          date: '2016-05-01',
          name: '王小虎',
          province: '上海',
          city: '普陀区',
          address: '上海市',
          zip: 200333
        }, {
          date: '2016-05-03',
          name: '王小虎',
          province: '上海',
          city: '普陀区',
          address: '上海市',
          zip: 200333
        }],
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
    }
  }
</script>

<style scoped>

</style>
