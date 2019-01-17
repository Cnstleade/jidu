
<template>
    <div class="container">
        <el-row>
            <el-alert
              title="公司银行卡服务状态相关"
              :closable="false"
            type="success">
            </el-alert>           
        </el-row>
        <el-table
            :data="tableData"  
            border  
      
            tooltip-effect="dark"
            highlight-current-row 
            style="width: 100%;font-weight:bold;margin-top:20px"
            class="mt-3"
            v-loading="loading">
            <el-table-column prop="id" label="编号" align="center" min-width="120"></el-table-column>
            <el-table-column prop="bankFlag" label="简称" align="center" min-width="120"></el-table-column>
            <el-table-column prop="bankDetail" label="银行" align="center"  min-width="120"></el-table-column>
            <el-table-column prop="asid" label="开启状态" align="center"  min-width="120">
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
            </el-table-column>            
            <el-table-column label="操作" align="center" min-width="150">
              <template slot-scope="scope">
                <el-button
                  size="mini"
                  v-if="!scope.row.status"
                  type="danger"
                  @click="handleEdit(scope.$index, scope.row)">开启</el-button>
                <el-button
                  size="mini"
                  v-if="scope.row.status"
                  type="danger"
                  @click="handleClose(scope.$index, scope.row)">关闭</el-button>                  
              </template>
            </el-table-column>            
        </el-table>
        <el-dialog 
            title="编辑功耗变化表" 
            :visible.sync="dialogFormEditVisible" 
            center
            width="30%"
            >
          <el-form :model="editForm">
            <el-form-item label="名称" label-width="100px">
              <el-input v-model="editForm.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="备注" label-width="100px">
              <el-select v-model="editForm.region" placeholder="请选择备注">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormEditVisible = false">取 消</el-button>
            <el-button type="primary" @click="dialogFormEditVisible = false">确 定</el-button>
          </div>
        </el-dialog> 
        <el-dialog 
            title="新增功耗变化表" 
            :visible.sync="dialogFormAddVisible" 
            center
            width="30%"
            >
          <el-form :model="addForm">
            <el-form-item label="名称" label-width="100px">
              <el-input v-model="addForm.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="备注" label-width="100px">
              <el-select v-model="addForm.region" placeholder="请选择备注">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormAddVisible = false">取 消</el-button>
            <el-button type="primary" @click="dialogFormAddVisible = false">确 定</el-button>
          </div>
        </el-dialog>                
    </div> 
</template>
<script>
import {
  httpGetcollectentrustlist,
  httpDocollectentrust,
  httpGetbankstatuslist,
  httpUpdatebankstatus
} from "../../../service/http";
export default {
  data() {
    return {
      multipleSelection: [],
      search: {},
      loading: false,
      tableData: [],
      parentRole: [
        {
          mid: 0,
          mname: "未外包"
        },
        {
          mid: 1,
          mname: "已外包"
        }
      ],
      npage: 1,
      pagesize: 10,
      total: 0,
      dialogVisible: false,
      formInline: {},
      editForm: {},
      addForm: {},
      dialogFormEditVisible: false,
      dialogFormAddVisible: false,
      rowId: ""
    };
  },
  methods: {
    reset() {},
    handleSearch() {},
    submitForm() {},
    changeDialog() {},
    handleEdit(index, row) {
      console.log(row.overdueId);
      this.$confirm("是否开启服务状态？", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          console.log(2);
          this.changehttpUpdatebankstatus(row.id,row.bankFlag,row.bankDetail,true);
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消开启"
          });
        });
    },
    handleClose(){
      this.$confirm("是否关闭服务状态？", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          console.log(2);
          this.changehttpUpdatebankstatus(row.id,row.bankFlag,row.bankDetail,false);
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消关闭"
          });
        });
    },
    gethttpDocollectentrust(ids) {
      console.log(ids);
      httpDocollectentrust(ids).then(res => {
        let data = res.data;
        if (data.code == 200) {
          this.$message({
            type: "primary",
            message: "外包成功!"
          });
          this.gethhttpSysMinerpowerlist(
            this.npage,
            this.pagesize,
            this.formInline.select
          );
        } else {
          this.$message({
            type: "success",
            message: "网络错误，请联系管理员"
          });
        }
      });
    },
    handleAdd() {
      this.dialogFormAddVisible = true;
    },
    handleDelete(index, row) {},
    onSubmit() {},
    /*   后台管理模块 / 矿机算力功率变化列表展示 */
    gethhttpSysMinerpowerlist(
      npage,
      pagesize,
      collectEntructStatus,
      begainTimeString,
      endTimeString
    ) {
      this.loading = true;
      httpGetcollectentrustlist(
        npage,
        pagesize,
        collectEntructStatus,
        begainTimeString,
        endTimeString
      )
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.tableData = data.data.list;
            this.total = data.data.allpage;
            this.$message({
              message: "查询成功",
              type: "success"
            });
          } else {
            this.$message({
              message: data.msg,
              type: "error"
            });
          }
          this.loading = false;
        })
        .catch(err => {
          let data = {
            code: 200,
            msg: "提交成功",
            data: {
              npage: 1,
              allpage: 68,
              pagesize: 20,
              allsize: 0,
              list: [
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 2,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: 1,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                },
                {
                  overdueId: 9,
                  totalPenalty: 2900,
                  mobile: "13594237679",
                  statementDate: 145,
                  idNo: "50023019910308375X",
                  loanAmount: 500,
                  name: "付月磊",
                  asid: null,
                  create_time: "2018-08-12T16:00:00.000+0000"
                }
              ]
            }
          };
          this.tableData = data.data.list;
          this.total = data.data.allpage;
          this.loading = false;
        });
    },
    /*     重置选择 */
    reset() {
      this.formInline = {};
      this.handleSearch();
    },
    /*   选取第几页 */
    handleCurrentChange(val) {
      this.npage = val;
      this.handleSearch(false);
    },
    /* 选取页数 */
    handleSizeChange(val) {
      this.pagesize = val;
      this.handleSearch(false);
    },
    /* 按条件搜索 */
    handleSearch(type = true) {
      if (type) {
        this.npage = 1;
        this.pagesize = 10;
      }
      if (this.formInline.time && this.formInline.time.length) {
        this.gethhttpSysMinerpowerlist(
          this.npage,
          this.pagesize,
          this.formInline.select,
          this.formInline.time[0],
          this.formInline.time[1]
        );
      } else {
        this.gethhttpSysMinerpowerlist(
          this.npage,
          this.pagesize,
          this.formInline.select
        );
      }
    },
    //批量外包
    allotOperator() {
      if (this.multipleSelection.length == 1) {
        this.rowId = this.multipleSelection[0] + ",";
      } else {
        this.rowId = this.multipleSelection.join(",");
      }
      this.$confirm("是否外包这个订单？", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.gethttpDocollectentrust(this.rowId);
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消外包"
          });
        });
    },
    //全选分配
    handleSelectionChange(val) {
      var arr = val.filter(v => !v.asid);
      this.multipleSelection = arr.map(v => v.overdueId);
      console.log(this.multipleSelection);
    },
    gethttpGetbankstatuslist() {
      httpGetbankstatuslist()
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.tableData = data.data;
          }
        })
        .catch(err => {
          let data = {
            code: 200,
            msg: "提交成功",
            data: [
              {
                id: 1,
                bankFlag: "bbtv",
                bankDetail: "建设银行",
                createTime: "2019-01-03T01:37:52.000+0000",
                updateTime: "2019-01-03T01:37:54.000+0000",
                status: false
              }
            ]
          };
          if (data.code == 200) {
            this.tableData = data.data;
          }
        });
    },
    changehttpUpdatebankstatus(id,bankFlag,bankDetail,asid) {
      httpUpdatebankstatus(id,bankFlag,bankDetail,asid).then(res => {
        let data = res.data;
        if (res.data == 200) {
          this.gethttpGetbankstatuslist();
        }
      });
    }
  },
  mounted() {
    this.gethttpGetbankstatuslist();
  }
};
</script>
<style  scoped>
.d-flex {
  display: flex;
  flex-direction: row;
  justify-content: flex-end;
  margin-top: 20px;
}
.demo-form-inline {
  margin-right: 20px;
}
</style>

