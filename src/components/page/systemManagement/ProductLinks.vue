<template>
    <div class="container">
                <el-row>
            <el-alert
              title="产品列表"
              :closable="false"
              type="info">
            </el-alert>           
        </el-row> 
        <el-row class="m20 col-flex-end" >
            <div style="flex-grow:1">
    <el-button  type="primary" @click="handleAdd" >新增</el-button>
            </div>
            
               <el-button  type="primary" @click="cz">重置</el-button>
                                   <div class="l20">
                 <el-input            
                 style="padding:0px 10px 0px 0px"
                      placeholder="请输入产品"
                      v-model="search.phonenumber"
                      clearable>
                     </el-input> 
                    </div> 

                    <el-button @click="handleSearch" class="l20" style="margin-left:20px" icon="el-icon-search"  type="success" circle></el-button>                                                                  
        </el-row>   
        <el-table
            :data="tableData"  
            border  
      
            tooltip-effect="dark"
            highlight-current-row 
            style="width: 100%;font-weight:bold;margin-top:20px"
            class="mt-3"
           >
            <el-table-column prop="id" label="编号" align="center" min-width="120"></el-table-column>
            <el-table-column prop="productName" label="产品名称" align="center" min-width="120"></el-table-column>
            <el-table-column prop="bankDetail" label="图片" align="center"  min-width="120">
                <template slot-scope="scope">
                    <div>
                        <img :src="scope.row.imageUrl" alt="" width="200" >
                    </div>
                </template>
            </el-table-column>
            <el-table-column prop="registeredUrl" label="产品注册地址" align="center"  min-width="120"></el-table-column>
            <el-table-column prop="extendField" label="苹果下载地址" align="center"  min-width="120"></el-table-column>
            <el-table-column prop="downloadUrl" label="安卓下载地址" align="center"  min-width="120"></el-table-column>
            <el-table-column prop="sort" label="排序（权重）" align="center"  min-width="120"></el-table-column>
            <el-table-column prop="productName" label="是否展示" align="center"  min-width="120">
                  <template slot-scope="scope">
                      <el-tag
                          :type="scope.row.isUsed?'':'danger'"
                      >{{scope.row.isUsed?'是':'否'}}</el-tag>
                  </template>   
            </el-table-column>
            <el-table-column prop="create_time" label="添加时间" align="center"  min-width="120">
                <template slot-scope="scope">
                    {{scope.row.createTime|dateServer}}
                </template> 
            </el-table-column>
            <!-- <el-table-column prop="asid" label="开启状态" align="center"  min-width="120">
                  <template slot-scope="scope">
                      <el-tag
                          :type="scope.row.status?'':'danger'"
                      >{{scope.row.status?'是':'否'}}</el-tag>
                  </template>                
            </el-table-column>
            <el-table-column prop="createTime" label="创建时间" align="center"  width="180">
                <template slot-scope="scope">
                    {{scope.row.createTime|dateServer}}
                </template> 
            </el-table-column>
           <el-table-column prop="updateTime" label="更新时间" align="center"  width="180">
                <template slot-scope="scope">
                    {{scope.row.updateTime|dateServer}}
                </template> 
            </el-table-column>             -->
            <el-table-column label="操作" align="center" min-width="150">
              <template slot-scope="scope">
                <el-button
                  size="mini"
                  :disabled="scope.row.status"
                  type="danger"
                  @click="handleEdit(scope.$index, scope.row)">修改</el-button>
              </template>
            </el-table-column>            
        </el-table>    
        <el-dialog
            title="添加产品" 
            :visible.sync="dialogFormAddVisible" 
            center
            width="30%"
        >
            <el-form :model="addForm">
            <el-form-item label="产品名称" label-width="100px">
              <el-input v-model="addForm.productName" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="注册地址" label-width="100px">
              <el-input v-model="addForm.registeredUrl" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="苹果下载" label-width="100px">
              <el-input v-model="addForm.extendField" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="安卓下载" label-width="100px">
              <el-input v-model="addForm.downloadUrl" autocomplete="off"></el-input>
            </el-form-item> 
            <el-form-item label="产品封面" label-width="100px">
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
            <el-form-item label="排序权重" label-width="100px">
              <el-input v-model="addForm.sort" autocomplete="off"></el-input>
            </el-form-item>   
            <el-form-item label="是否展示" label-width="100px">
             <el-switch
                v-model="addForm.isUsed"
                active-text="是"
                inactive-text="否">
            </el-switch>
            </el-form-item>                                                                      
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormAddVisible = false">取 消</el-button>
            <el-button type="primary" @click="handleClick">添加</el-button>
          </div>
        </el-dialog> 
                <el-dialog
            title="修改产品" 
            :visible.sync="dialogFormEditVisible" 
            center
            width="30%"
        >
               <el-form :model="editForm">
            <el-form-item label="产品名称" label-width="100px">
              <el-input v-model="editForm.productName" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="注册地址" label-width="100px">
              <el-input v-model="editForm.registeredUrl" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="苹果下载" label-width="100px">
              <el-input v-model="editForm.extendField" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="安卓下载" label-width="100px">
              <el-input v-model="editForm.downloadUrl" autocomplete="off"></el-input>
            </el-form-item> 
            <el-form-item label="是否修改图片" label-width="100px">
             <el-switch
                v-model="editForm.img"
                active-text="是"
                inactive-text="否">
            </el-switch>
            </el-form-item>             
            <el-form-item label="产品封面" label-width="100px" v-if="editForm.img">
                    <el-upload
                        action="123"
                      class="upload-demo"
                      ref="uploads"
                      :limit="1"
                   
                      :on-change="handleChange"
                        :on-preview="handlePreview"
                        :on-remove="handleRemove"
                      :before-upload="beforeAvatarUploads"
                      list-type="picture"
                      :auto-upload="false" >
                      <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
                      <!-- <el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">上传到服务器</el-button> -->
                      <!-- <div slot="tip" class="el-upload__tip">（必须上传图片，且大小为4M以内），且不超过4M</div> -->
                    </el-upload>  
            </el-form-item> 
            <el-form-item label="排序权重" label-width="100px">
              <el-input v-model="editForm.sort" autocomplete="off"></el-input>
            </el-form-item>   
            <el-form-item label="是否展示" label-width="100px">
             <el-switch
                v-model="editForm.isUsed"
                active-text="是"
                inactive-text="否">
            </el-switch>
            </el-form-item>                                                                      
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormEditVisible = false">取 消</el-button>
            <el-button type="primary" @click="handleClicks">修改</el-button>
          </div>
        </el-dialog>          
    </div>
</template>

<script>
import {
  httpselectProductLinks,
  httpSaveProductLinks
} from "../../../service/http";
export default {
  data() {
    return {
      tableData: [],
      loading: true,
      search: {},
      addForm: {},
      editForm: {},
      dialogFormAddVisible: false,
      dialogFormEditVisible: false
    };
  },
  methods: {
    getData(productName) {
      httpselectProductLinks(productName).then(res => {
        let data = res.data;
        if (data.code == 200) {
          this.tableData = data.data;
          this.loading = false;
        }
      });
    },
    handleSearch() {
      this.getData(this.search.productName);
    },
    handleEdit(index, row) {
      this.editForm = JSON.parse(JSON.stringify(row));
      this.dialogFormEditVisible = true;
    },
    cz() {
      this.search = {};
      this.handleSearch();
    },
    addForm() {
      httpSaveProductLinks(
        null,
        this.addForm.imageUrl,
        this.addForm.downloadUrl,
        this.addForm.registeredUrl,
        this.addForm.extendField,
        this.addForm.productName,
        this.addForm.sort
      );
    },
    editForms() {
      httpSaveProductLinks(
        this.editForm.id,
        this.editForm.imageUrl,
        this.editForm.downloadUrl,
        this.editForm.registeredUrl,
        this.editForm.extendField,
        this.editForm.productName,
        this.editForm.sort
      );
    },
    handleRemove(file, fileList) {
      this.imgAdd = false;
      this.$message.error("必须上传图片凭证");
    },
    handlePreview(file) {},
    handleChange(file, fileList) {},
    beforeAvatarUpload(file) {
      httpSaveProductLinks(
        null,
        file,
        this.addForm.downloadUrl,
        this.addForm.registeredUrl,
        this.addForm.extendField,
        this.addForm.productName,
        this.addForm.sort
      ).then(res => {
        let data = res.data;
        if (data.code == 200) {
          this.dialogFormAddVisible = false;
       this.getData();
        }
      });
    },
    beforeAvatarUploads(file) {
      httpSaveProductLinks(
        this.editForm.id,
        file,
        this.editForm.downloadUrl,
        this.editForm.registeredUrl,
        this.editForm.extendField,
        this.editForm.productName,
        this.editForm.sort
      ).then(res => {
        let data = res.data;
        if (data.code == 200) {
          this.dialogFormEditVisible = false;
         this.getData();
        }
      });
    },
    handleClick() {
      this.$refs.upload.submit();
    },
    handleAdd() {
      this.addForm = {};
      this.dialogFormAddVisible = true;
    },
    handleClicks() {
      if (this.editForm.img) {
        this.$refs.uploads.submit();
      } else {
        httpSaveProductLinks(
          this.editForm.id,
          null,
          this.editForm.downloadUrl,
          this.editForm.registeredUrl,
          this.editForm.extendField,
          this.editForm.productName,
          this.editForm.sort
        ).then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.dialogFormEditVisible = false;
            this.getData();
          }
        });
      }
    }
  },
  mounted() {
    this.getData();
  }
};
</script>

<style scoped>
</style>

