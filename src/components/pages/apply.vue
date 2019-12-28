<template>
  <div class="apply">
    <Header></Header>
    <el-row class="main" type="flex" justify="center" style="margin-top: 5%">
      <el-col :span="2"></el-col>
      <el-col :span="14">
        <h5 class="title"><i class="el-icon-circle-plus-outline"></i>申请流程</h5>
        <hr/>
        <el-card shadow="hover">
          <el-steps align-center :active="currStep">
            <el-step description="首先接入本博客站点"></el-step>
            <el-step  description="提交您的站点信息"></el-step>
            <el-step description="等待审核，邮件通知"></el-step>
          </el-steps>
          <div class="stepContent" v-if="currStep === 1">
            <div class="statement" >
              <div class="item">博客链接：https://www.fengziy.cn/</div>
              <div class="item">昵称：MuGe</div>
              <div class="item">简介：向上的路并不拥挤，而大多数人选择了安逸。</div>
            </div>
          </div>
          <div class="stepContent" v-if="currStep === 2">
            <el-form label-position="right" :rules="rules" label-width="80px" ref="formLabelAlign"
                     :model="formLabelAlign">
              <el-form-item label="昵称" prop="name">
                <el-input v-model="formLabelAlign.name"></el-input>
              </el-form-item>
              <el-form-item label="联系邮箱" prop="email">
                <el-input v-model="formLabelAlign.email"></el-input>
              </el-form-item>
              <el-form-item label="博客链接" prop="blogLink">
                <el-input v-model="formLabelAlign.blogLink"></el-input>
              </el-form-item>
              <el-form-item label="备注" prop="notes">
                <el-input type="textarea" v-model="formLabelAlign.notes"></el-input>
              </el-form-item>
            </el-form>
          </div>
          <div class="stepContent" v-if="currStep === 3">
            <p class="tips">
              <i class="el-icon-success" style="color: #409EFF;font-size: 30px;"></i><br/>
              感谢您对本站的信赖，审核成功后我将以邮件的方式通知您。
            </p>
          </div>
          <el-row type="flex" class="btn-tool" justify="space-around">
            <el-button icon="el-icon-back" v-if="currStep === 2" @click="lastStep">
              申请流程
            </el-button>
            <el-button type="primary"  @click="nextStep"
                       v-if="currStep !== 3">下一步
            </el-button>
          </el-row>
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
    data() {
      return {
        currStep: 1,
        formLabelAlign: {
          name: '',
          email: '',
          blogLink: '',
          notes: ''
        },
        rules: {
          name: [{
            required: true,
            message: '请输入您的昵称',
            trigger: 'blur'
          }],
          email: [{
            required: true,
            message: '请输入邮箱地址',
            trigger: 'blur'
          },
            {
              type: 'email',
              message: '请输入正确的邮箱地址',
              trigger: ['blur', 'change']
            }
          ],
          blogLink: [{
            required: true,
            message: '请输入博客',
            trigger: 'blur'
          }],
          notes: [{
            required: false,
            message: '请输入额外备注',
            trigger: 'blur'
          }]
        }
      };
    },
    components: {Header, Footer},
    methods: {
      nextStep() {
        this.currStep++
      },
      lastStep() {
        this.currStep--
      }
    }
  }
</script>

<style scoped>
  .title {
    margin-top: 40px;
  }

  .statement {
    padding: 20px;
    background-color: #EBEEF5;
    margin-top: 20px;
  }

  .stepContent {
    margin: 30px 0;
  }

  .stepContent .tips {
    font-size: 18px;
    text-align: center;
  }
</style>
