
<template>
    <div class="container">
        <el-row>
            <el-alert
              title="催收外包管理"
              :closable="false"
            type="success">
            </el-alert>           
        </el-row>
        <el-row class="mt-4 d-flex flex-row flex-nowrap justify-content-end">
            <el-form :inline="true" :model="formInline" class="demo-form-inline">
             <el-form-item>
                  <el-button type="success" @click="reset">重置</el-button>
              </el-form-item> 
              <el-form-item>
                    <el-select class="l20" v-model="formInline.select" placeholder="是否外包">
                      <el-option
                        v-for="item in parentRole"
                        :key="item.mid"
                        :label="item.mname"
                        :value="item.mid">
                      </el-option>
                    </el-select>               
              
              </el-form-item> 
                           
              <el-form-item >
                <el-date-picker
                style="width:400px"
                      v-model="formInline.time"
                      type="daterange"
                      value-format="yyyy-MM-dd"
                      range-separator="至"
                      start-placeholder="开始日期"
                      end-placeholder="结束日期">
                </el-date-picker> 
              </el-form-item>              
              <el-form-item>
                <el-button @click="handleSearch" class="mr-4"  icon="el-icon-search"  type="success" circle></el-button>
              </el-form-item>
            </el-form>            
        </el-row>
        <el-table
            :data="tableData"  
            border  
            ref="multipleTable" 
            tooltip-effect="dark"
            highlight-current-row 
            style="width: 100%;font-weight:bold"
            class="mt-3"
        
            @selection-change="handleSelectionChange"
            v-loading="loading">
                
            				<el-table-column type="selection"  width="55">
                  </el-table-column>
               
				
            <el-table-column prop="overdueId" label="逾期订单" align="center" min-width="120"></el-table-column>
            <el-table-column prop="totalPenalty" label="逾期金额" align="center" min-width="120"></el-table-column>
            <el-table-column prop="mobile" label="手机号" align="center"  min-width="120"></el-table-column>
            <el-table-column prop="statementDate" label="逾期天数" align="center"  min-width="120"></el-table-column>
            <el-table-column prop="idNo" label="身份证" align="center"  min-width="180"></el-table-column>
            <el-table-column prop="loanAmount" label="提现金额" align="center"  min-width="120"></el-table-column>
            <el-table-column prop="name" label="姓名" align="center"  min-width="120"></el-table-column>
            <el-table-column prop="asid" label="是否外包" align="center"  min-width="120">
                  <template slot-scope="scope">
                      <el-tag
                          :type="scope.row.asid?'':'danger'"
                      >{{scope.row.asid?'是':'否'}}</el-tag>
                  </template>                
            </el-table-column>
            <el-table-column prop="提现时间" label="提现时间" align="center"  width="180">
                <template slot-scope="scope">
                    {{scope.row.create_time|dateServer}}
                </template> 
            </el-table-column>
            <el-table-column label="操作" align="center" min-width="150">
              <template slot-scope="scope">
                <el-button
                  size="mini"
                  :disabled="scope.row.asid"
                  type="danger"
                  @click="handleEdit(scope.$index, scope.row)">外包</el-button>
              </template>
            </el-table-column>            
        </el-table>
        <el-row class="mt-4" v-if="total>0">
				<el-button type="primary" style="float:left" @click="allotOperator()"  :disabled="multipleSelection.length==0">批量外包</el-button>
             <div style="float:right">
                 <el-pagination
                   @current-change="handleCurrentChange"
                    @size-change="handleSizeChange"
                   :current-page="npage"
                    :page-sizes="[10, 20, 50, 100, 1000,990000]"
                     :page-size="pagesize"
                   background
                   layout="total,sizes,prev, pager, next,jumper"
                   :total="total">
                 </el-pagination>   
             </div>
        </el-row>         
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
  httpDocollectentrust
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
      this.$confirm("是否外包这个订单？", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          console.log(2);
          this.gethttpDocollectentrust(row.overdueId + ",");
        })
        .catch(() => {
          console.log(1);
          this.$message({
            type: "info",
            message: "已取消外包"
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
    }
  },
  mounted() {
    this.gethhttpSysMinerpowerlist(
      this.npage,
      this.pagesize,
      this.formInline.select
    );
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

