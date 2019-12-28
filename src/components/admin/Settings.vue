<template>
  <section class="blog-body">
    <el-card class="box-card" style="margin-bottom: 1%">
      <div slot="header" class="clearfix">
        <span style="font-weight: bold">修改个人信息</span>
      </div>
      <div>

        <el-form label-position="top" label-width="80px">
          <el-form-item label="个性签名">
            <el-input type="text" v-model="signature"
                      maxlength="50" show-word-limit="true"
                      @input="descInput"></el-input>
          </el-form-item>
          <el-form-item label="个人简介">
            <el-input type="textarea" v-model="introduction" rows="3"
                      maxlength="1000" show-word-limit="true"
                      @input="descInput"></el-input>
            <div style="float:right;margin: 2% 2% 0 0">
              <el-button
                type="primary" size="small" style="margin-left:15px"
                @click="modifyUser">修改
              </el-button>
            </div>
          </el-form-item>
        </el-form>
      </div>


    </el-card>

    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span style="font-weight: bold">修改头像</span>
      </div>
      <div>
        <el-upload
          class="avatar-uploader"
          action="/api/user/avatarUpload"
          :show-file-list="false"
          :data="params"
          :on-success="handleAvatarSuccess"
          :before-upload="beforeAvatarUpload">
          <img v-if="avatar" :src="avatar" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>

        <!-- 文件上传-->
        <!-- <el-upload
           action="/api/user/avatarUpload"
           list-type="picture-card"
           :data="params"
           :on-preview="handlePictureCardPreview"
           :on-remove="handleRemove">
           <i class="el-icon-plus"></i>
         </el-upload>
         <el-dialog :visible.sync="dialogVisible">
           <img width="100%" :src="dialogImageUrl" alt="">
         </el-dialog>-->
      </div>
    </el-card>


  </section>
</template>

<script>
  export default {
    data() {
      return {
        dialogImageUrl: '',
        dialogVisible: false,
        introduction: '',
        signature: '',
        params: {
          uId: "",
        },
        avatar: '',
      };
    },
    methods: {
      //头像上传的相关方法
      handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePictureCardPreview(file) {
        this.dialogImageUrl = file.url;
        this.dialogVisible = true;
      },
      handleAvatarSuccess() {
        this.getUser();
      },
      getUser() {
        let id = sessionStorage.getItem("uId");
        this.axios
        .get("/api/user/findById", {
          params: {id}
        })
        .then(res => {
          this.signature = res.data.queryResult.list[0].signature;
          this.introduction = res.data.queryResult.list[0].introduction;
          this.avatar = res.data.queryResult.list[0].avatar;
        })
        .catch(error => {
          console.log(error);
        });
      },
      modifyUser() {
        if (this.signature == "") {
          this.$message({
            message: "签名不能为空",
            type: "warning"
          });
        } else {
          let uId = sessionStorage.getItem("uId");
          this.axios.post("/api/user/updateUser", {
            uid: uId,
            signature: this.signature,
            introduction: this.introduction,
          }).then(res => {
            if (res.data.success) {
              this.$message({
                message: "修改成功!",
                type: "success",
                onClose: this.getUser()
              });
            } else {
              this.$message({
                message: "修改失败!",
                type: "error",
              })
            }
          }).catch(error => {
            console.log(error);
          })
        }
      }
      ,
    },
    created() {
      this.params.uId = sessionStorage.getItem("uId");
      this.getUser();
    }
  }
</script>

<style>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }

  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }

  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }

  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>

