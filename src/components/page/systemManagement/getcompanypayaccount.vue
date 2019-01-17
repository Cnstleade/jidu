<template>
    <div class="container getcompanypayaccount">
        <el-card class="box-card" v-if="show">
            <div slot="header" class="clearfix">
              <span>公司账户管理</span>
              <el-button style="float: right; padding: 3px 0" type="text" @click="change"> 修改</el-button>
            </div>
            <div class="text item">
                <div>
                    <el-tag type="success">ID</el-tag>
                    <span class="span">{{tableData.id}}</span>
                </div>
                <div>
                     <el-tag type="success">支付宝账号</el-tag>
                     <span class="span">{{tableData.alipayAccount}}</span>
                </div>
                 <img :src="tableData.zhimaQrcode" class="image">
                <div>
                    <el-tag type="success">公司电话</el-tag>
                    <span class="span">{{tableData.companyPhone}}</span>
                </div>
                <div>
                    <el-tag type="success">微信号</el-tag>
                    <span class="span">{{tableData.weixinNumber}}</span>
                </div>  
                <div>
                    <el-tag type="success">微信账户名</el-tag>
                    <span class="span">{{tableData.weixinName}}</span>
                </div>
                <div>
                    <el-tag type="success">更新时间</el-tag>
                    <span class="span">{{tableData.updateTime|dateServer}}</span>
                </div>                                                 
            </div>
        </el-card>
                <el-dialog 
            title="编辑公司账户" 
            :visible.sync="dialogFormEditVisible" 
            center
            width="30%"
            >
          <el-form :model="editForm" >
            <el-form-item label="支付宝账号" label-width="100px">
              <el-input v-model="editForm.alipayAccount" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="公司电话" label-width="100px">
              <el-input v-model="editForm.companyPhone" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="微信号" label-width="100px">
              <el-input v-model="editForm.weixinNumber" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="微信账户名" label-width="100px">
              <el-input v-model="editForm.weixinName" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="支付宝账号" label-width="100px">
              <el-input v-model="editForm.alipayAccount" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="二维码" label-width="100px">
                    <el-upload
                        action="123"
                      class="upload-demo"
                      ref="upload"
                      :limit="1"
                   
                      :on-change="handleChange"
                        :on-preview="handlePreview"
                        :on-remove="handleRemove"
                      :before-upload="beforeAvatarUpload"
                      list-type="picture"
                      :auto-upload="false" >
                      <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
                      <!-- <el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">上传到服务器</el-button> -->
                      <!-- <div slot="tip" class="el-upload__tip">（必须上传图片，且大小为4M以内），且不超过4M</div> -->
                    </el-upload>  
            </el-form-item>                                                            

          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormEditVisible = false">取 消</el-button>
            <el-button type="primary" @click="handleClick">确 定</el-button>
          </div>
        </el-dialog> 

    </div>
</template>

<script>
import {
  httpGetcompanypayaccount,
  httpUpdatecompanypayaccount
} from "../../../service/http";
export default {
  data() {
    return {
      tableData: {},
      show: false,
      editForm: {},
      dialogFormEditVisible: false
    };
  },
  methods: {
    _inits() {
      httpGetcompanypayaccount()
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.tableData = data.data;
            this.show = true;
          }
        })
        .catch(err => {
          let data = {
            code: 200,
            msg: "提交成功",
            data: {
              id: 1,
              alipayAccount: "1845656",
              zhimaQrcode: "12313",
              updateTime: "2019-01-03T01:09:49.000+0000",
              companyPhone: "13232323",
              weixinNumber: "2132323",
              weixinName: "12323"
            }
          };
          if (data.code == 200) {
            this.tableData = data.data;
            this.show = true;
          }
        });
    },
    change() {
      this.editForm = JSON.parse(JSON.stringify(this.tableData));
      this.dialogFormEditVisible = true;
    },
    beforeRemove(file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`);
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
    handleRemove(file, fileList) {
      this.imgAdd = false;
      this.$message.error("必须上传图片凭证");
    },
    handlePreview(file) {},
    handleChange(file, fileList) {},
    beforeAvatarUpload(file) {
      httpUpdatecompanypayaccount(
        this.editForm.id,
        this.editForm.alipayAccount,
        this.editForm.companyPhone,
        this.editForm.weixinNumber,
        this.editForm.weixinName,
        file
      ).then(res => {
        let data = res.data;
        if (data.code == 200) {
          this.dialogFormEditVisible = false;
          this._inits();
        }
      });
    },
    handleClick() {
      this.$refs.upload.submit();
    }
  },
  mounted() {
    this._inits();
  }
};
</script>

<style scoped>
.box-card {
  width: 600px;
  margin: 0 auto;
}
.image {
  width: 100%;
  display: block;
}
.text {
  padding: 10px;
}
.text > div {
  margin: 10px;
}
.span {
  float: right;
  margin-right: 50px;
  font-weight: bold;
}
</style>

