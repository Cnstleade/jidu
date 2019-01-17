<template>
    <div >
        <div>
            <el-button  type="primary" @click="changeTabList">执行顺序</el-button >  
        </div>
        {{changeList}}


<el-dialog
  title="编辑执行顺序"
  :visible.sync="centerDialogVisible"
  :before-close="close1"
  width="50%"
  center>
  <div class="color-list">
       <div 
          class="color-item" 
          v-for="(temp,i) in datass" v-dragging="{ item: temp, list: datass, group: 'color' }"
          :key="temp.id"
      >
      第{{i+1}}步
    <!-- <span>{{temp.executeName}}{{temp.executeNameCn}}</span>  -->
    <span>{{temp.executeNameCn}}</span> 
      </div>  
  </div>

  <span slot="footer" class="dialog-footer">
    <el-button @click="centerDialogVisibleAdd">取 消</el-button>
    <el-button type="primary" @click="centerDialogVisibleChageng(changeList)">确 定</el-button>
  </span>
</el-dialog>
    </div>

</template>

<script>
import {
  httpGetExecutor,
  httpSetExecutorOrder,
  httpGetxinyanreport
} from "../../../service/http";
export default {
  inject: ["reload"],
  data() {
    return {
      colors: [
        {
          text: "Aquamarine"
        },
        {
          text: "Skyblue"
        },
        {
          text: "Burlywood"
        }
      ],
      datass: [],
      changeList: [],
      centerDialogVisible: false,
      arrs: []
    };
  },
  //这里挺重要的，因为我们一般排序完要重新提交排序后的数据给后台保存，以便下一次安装我们所需要的顺序显示，这里的list就可以帮我们做到这一点，但是我们需要给数据添加一个uniqueId标志。然后在排序完后或者列表对应的顺序和uniqueId提交给后台，我也不知道我说的咋样。
  methods: {
    getData() {
      httpGetExecutor(1, 999, 0)
        .then(res => {
          let data = res.data;
          this.datass = data.list;
        })
        .catch(err => {
          let data = {
            npage: 1,
            allpage: 1,
            pagesize: 30,
            allsize: 23,
            list: [
              {
                id: 6,
                executeName: "R10012_1Executor",
                executePara: "新疆,西藏",
                executeParaHelp: "号码归属地",
                executeNameCn: "归属地查询",
                status: false,
                type: false
              },
              {
                id: 8,
                executeName: "R10012_2Executor",
                executePara: "3",
                executeParaHelp: "三月通话次数",
                executeNameCn: "常用联系人通话次数",
                status: false,
                type: false
              },
              {
                id: 9,
                executeName: "R10012_3Executor",
                executePara: "10",
                executeParaHelp: "数量为",
                executeNameCn: "互通电话数量",
                status: true,
                type: false
              },
              {
                id: 10,
                executeName: "R10012_4Executor",
                executePara: "0",
                executeParaHelp: "是否",
                executeNameCn: "是否在法院&银行黑名单",
                status: true,
                type: false
              },
              {
                id: 11,
                executeName: "R10012_5Executor",
                executePara: "30",
                executeParaHelp: "数量为",
                executeNameCn: "企业查询数量",
                status: true,
                type: false
              },
              {
                id: 12,
                executeName: "R10012_6Executor",
                executePara: "3",
                executeParaHelp: "数量为",
                executeNameCn: "身份证组合手机数量",
                status: true,
                type: false
              },
              {
                id: 13,
                executeName: "R10012_7Executor",
                executePara: "5",
                executeParaHelp: "连续三天静默次数",
                executeNameCn: "手机静默情况",
                status: true,
                type: false
              },
              {
                id: 14,
                executeName: "R10012_8Executor",
                executePara: "经常联系",
                executeParaHelp: "频次",
                executeNameCn: "贷款类号码联系情况",
                status: false,
                type: false
              },
              {
                id: 15,
                executeName: "R10012_9Executor",
                executePara: "5",
                executeParaHelp: "使用月份",
                executeNameCn: "号码使用时长",
                status: true,
                type: false
              },
              {
                id: 16,
                executeName: "R10012_10Executor",
                executePara: "40,3",
                executeParaHelp: "连续5月低于指定花费的次数(,隔开)",
                executeNameCn: "花费使用情况",
                status: true,
                type: false
              },
              {
                id: 17,
                executeName: "R10012_11Executor",
                executePara: "2,10",
                executeParaHelp: "频次,时长",
                executeNameCn: "连续5月平均通话频次及通话时长",
                status: true,
                type: false
              },
              {
                id: 18,
                executeName: "R10013_1Executor",
                executePara: null,
                executeParaHelp: null,
                executeNameCn: "同盾建议拒绝",
                status: false,
                type: false
              },
              {
                id: 19,
                executeName: "R10013_2Executor",
                executePara: "100",
                executeParaHelp: "同盾分",
                executeNameCn: "反欺诈分较高",
                status: true,
                type: false
              },
              {
                id: 20,
                executeName: "R10013_3Executor",
                executePara: null,
                executeParaHelp: null,
                executeNameCn: "多平台借款",
                status: false,
                type: false
              },
              {
                id: 21,
                executeName: "R10011_1Executor",
                executePara: null,
                executeParaHelp: null,
                executeNameCn: "芝麻账号是否存在",
                status: false,
                type: false
              },
              {
                id: 22,
                executeName: "R10011_2Executor",
                executePara: null,
                executeParaHelp: null,
                executeNameCn: "芝麻信用分",
                status: false,
                type: false
              },
              {
                id: 23,
                executeName: "R10014_1Executor",
                executePara: null,
                executeParaHelp: null,
                executeNameCn: "多平台申请检测",
                status: false,
                type: false
              },
              {
                id: 24,
                executeName: "R10014_2Executor",
                executePara: null,
                executeParaHelp: null,
                executeNameCn: "逾期订单信息",
                status: false,
                type: false
              },
              {
                id: 25,
                executeName: "R10016_0Executor",
                executePara: "580",
                executeParaHelp: "芝麻分低于",
                executeNameCn: "芝麻信用分",
                status: true,
                type: false
              },
              {
                id: 26,
                executeName: "R10015_1Executor",
                executePara: null,
                executeParaHelp: null,
                executeNameCn: "平台黑名单",
                status: true,
                type: false
              },
              {
                id: 27,
                executeName: "R10013_4Executor",
                executePara: "35",
                executeParaHelp: "7天申请平台总个数",
                executeNameCn: "7天内在多个平台申请借款",
                status: true,
                type: false
              },
              {
                id: 28,
                executeName: "R10013_5Executor",
                executePara: "70",
                executeParaHelp: "1个月申请平台总个数",
                executeNameCn: "1个月内在多个平台申请借款",
                status: true,
                type: false
              },
              {
                id: 29,
                executeName: "R10013_6Executor",
                executePara: "100",
                executeParaHelp: "3个月申请平台总个数",
                executeNameCn: "3个月内在多个平台申请借款",
                status: true,
                type: false
              }
            ],
            type: 0,
            countVo: null
          };
          this.datass = data.list;
        });
    },
    updated: function() {
      console.group("updated 更新完成状态===============》");
      console.log("%c%s", "color:red", "el     : " + this.$el);
      console.log(this.$el);
      console.log("%c%s", "color:red", "data   : " + this.$data);
      console.log("%c%s", "color:red", "message: " + this.message);
    },
    close1() {
      this.reload();
      this.centerDialogVisible = false;
      this.changeList = [];
    },
    changeTabList() {
      this.getData();
      this.centerDialogVisible = true;
      this.$dragging.$on("dragged", ({ value }) => {
        this.changeList = JSON.parse(JSON.stringify(value.list));
        console.log(value.list);
        this.arrs = value.list.map(v => v.id);
        console.log(this.arrs);
        // this.changeList = JSON.parse(JSON.stringify(value.list));
      });
      // this.$dragging.$on("dragged", ({ value }) => {
      //   this.changeList = JSON.parse(JSON.stringify(value.list));
      // });
    },
    centerDialogVisibleChageng(value) {
      console.log(value);
      if (value) {
        var arr = value.map(v => v.id);
        console.log(arr.join(","));

        httpSetExecutorOrder(arr.join(",")).then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.getTableData(this.currentPage, this.pageSize, this.type);
            this.changeList.length = 0;
            setTimeout(() => {
              this.close1();
            }, 500);
          }
        });
      }
    },
    centerDialogVisibleAdd() {
      this.close1();
    }
  },
  mounted() {
    httpGetxinyanreport()
      .then(res => {
        let data = res.data;
        if (data.code == 200) {
          var reportList =JSON.parse(data.data.data).data.result_detail;
         console.log(reportList)
        } else if (data.code == 404) {
          this.$notify.error({
            title: "错误",
            message: "此用户没有新颜报告"
          });
        }
        //   let data = res.data;

        //   _this.baseInfo = data;
        //   _this.showVisible = true;
      })
      .catch();
  }
};
</script>
<style>
.color-list {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: flex-start;
  flex-wrap: wrap;
}
.color-item {
  background-color: rgba(64, 158, 255, 0.1);
  display: inline-block;
  padding: 0 10px;
  height: 32px;
  line-height: 30px;
  font-size: 12px;
  color: #409eff;
  border-radius: 4px;
  box-sizing: border-box;
  border: 1px solid rgba(64, 158, 255, 0.2);
  white-space: nowrap;
  margin: 5px;
  cursor: pointer;
}
</style>
