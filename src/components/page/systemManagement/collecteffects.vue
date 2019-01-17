<template>
    <div class="container">
        <el-row>
            <el-alert
              :title="title"
              :closable="false"
              type="info">
            </el-alert>           
        </el-row>  
        <el-row class="m20" >
            <el-col   class="col-flex-end">
                    <el-button  type="primary" @click="reset">重置</el-button>
                    <el-date-picker
                    style="width:340px"
                    class="l20"
                      v-model="search.time"
                      type="daterange"
                      value-format="yyyy-MM-dd"
                      range-separator="至"
                      start-placeholder="开始日期"
                      end-placeholder="结束日期">
                    </el-date-picker>                
                    <el-button @click="handleSearch" class="l20" style="margin-left:20px" icon="el-icon-search"  type="success" circle></el-button>                                                                  
            </el-col>             
        </el-row>        
        <el-row style="margin-top:40px">
	        <div id="myChart" :style="{width: '1200px', height: '600px'}"></div>
        </el-row>   
        <!-- <el-dialog :visible.sync="showVisible" width="60%"  v-if="JSON.stringify(baseInfo) !== '{}'">
            <el-row class="flx">
                <div class="fl">
                    贷前审核报告报告编号: <span>{{baseInfo.baseInfo.reportNum}}</span>
                </div>
                <div class="fr">
                </div>
            </el-row>
            <el-row >
                <el-card>
                    <el-col :span="6" :offset="2">
                        <div >
                            <div class="progress-text">
                                <strong>{{baseInfo.baseInfo.score}}</strong>
                                <p >{{baseInfo.baseInfo.suggestion == 1?'建议拒绝':(baseInfo.baseInfo.suggestion == 2?'建议人工审核':'建议通过')}}</p>
                            </div>
                                <el-progress type="circle" :percentage="baseInfo.baseInfo.score" color="red" :show-text="showText">aaaaa</el-progress>
                        </div>
                    </el-col>
                    <el-col :span="12">
                        <el-row style="text-align:center;padding-top:30px">
                           <strong style="font-size:20px">
                               {{baseInfo.baseInfo.suggestion == 1?'申请用户测出高危风险建议拒绝':(baseInfo.baseInfo.suggestion == 2?'申请用户测出风险建议人工审核':'申请用户建议通过')}}
                              </strong>
                        <br/>
                  
                        </el-row>
                    </el-col>
                </el-card>
            </el-row> 
            <el-row style="margin-top:20px" v-if="baseInfo.contactInfo.length>0">
                <el-card>
                    <el-row>
                        <h3>个人基本信息</h3>
                    </el-row>
                    <el-row style="margin-top:20px">
                        <el-col :span="12">
                            <el-row>
                                <el-col :span="5">
                                    姓名：
                                </el-col>
                                <el-col :span="6">
                                    {{baseInfo.baseInfo.realName}}
                                </el-col>
                            </el-row>
                        </el-col>
                        <el-col :span="12">
                            <el-row>
                                <el-col :span="5">
                                    身份证号：
                                </el-col>
                                <el-col :span="6">
                                     {{baseInfo.baseInfo.idNum}}
                                </el-col>
                            </el-row>
                        </el-col>
                    </el-row>
                    <el-row>
                        <el-col :span="12">
                            <el-row>
                                <el-col :span="5">
                                    手机号码：
                                </el-col>
                                <el-col :span="6">
                                   {{baseInfo.baseInfo.phoneNum}}
                                </el-col>
                            </el-row>
                        </el-col>
                    </el-row>
                    <el-row style="margin-top:20px" v-if="baseInfo.contactInfo.length==2">
                        <el-col :span="8">
                            <el-row>
                                <h3>第一个联系人</h3>
                            </el-row>
                            <el-row style="margin-top:20px">
                                <el-col :span="8">
                                    姓名：
                                </el-col>
                               <el-col :span="12">
                                    {{baseInfo.contactInfo[0].contactName}}
                                </el-col>                                
                            </el-row>
                            <el-row>
                                <el-col :span="8">
                                    手机号码：
                                </el-col>
                               <el-col :span="12">
                                    {{baseInfo.contactInfo[0].contactPhone}}
                                </el-col>                                
                            </el-row>
                            <el-row>
                                <el-col :span="8">
                                   社会关系：
                                </el-col>
                               <el-col :span="12">
                                   {{baseInfo.contactInfo[0].relationShip==1?'直系':(baseInfo.contactInfo[0].relationShip==2?'非直系':'社会')}}
                                </el-col>                                
                            </el-row>                                                        
                        </el-col>
                        <el-col :span="8">
                            <el-row>
                                <h3>第二个联系人</h3>
                            </el-row>
                            <el-row style="margin-top:20px">
                                <el-col :span="8">
                                    姓名：
                                </el-col>
                               <el-col :span="12">
                                    {{baseInfo.contactInfo[1].contactName}}
                                </el-col>                                
                            </el-row>
                            <el-row>
                                <el-col :span="8">
                                    手机号码：
                                </el-col>
                               <el-col :span="12">
                                    {{baseInfo.contactInfo[1].contactPhone}}
                                </el-col>                                
                            </el-row>
                            <el-row>
                                <el-col :span="8">
                                   社会关系：
                                </el-col>
                               <el-col :span="12">
                                   {{baseInfo.contactInfo[1].relationShip==1?'直系':(baseInfo.contactInfo[1].relationShip==2?'非直系':'社会')}}
                                </el-col>                                
                            </el-row>                                                        
                        </el-col>                       
                    </el-row>
                </el-card>
            </el-row>  
            <el-row style="margin-top:20px">
                <el-card>
                    <el-row>
                        <h3>归属地解析</h3>
                    </el-row>
                    <el-row style="margin-top:20px">
                        <el-col :span="5">
                            身份证所属地地址:
                        </el-col>
                        <el-col :span="12">
                            {{baseInfo.baseInfo.idCardAddress}}
                        </el-col>
                    </el-row>
                    <el-row>
                        <el-col :span="5">
                            手机所属地地址:
                        </el-col>
                        <el-col :span="12">
                           {{baseInfo.baseInfo.phoneAddress}}
                        </el-col>
                    </el-row>                    
                </el-card>
            </el-row>
            <el-row style="margin-top:20px">
                <el-card>
                        <table class="check-table" style="border-collapse: collapse" >
                            <thead style="text-align:center">
                            <tr>
                                <th style="width: 15%;">&nbsp;</th>
                                <th class="row1" style="width: 37%;">检查项目</th>
                                <th class="row2 " style="width: 9%;">风险等级</th>
                                <th class="row3" style="width: 37%;">备注</th>
                            </tr>
                            </thead>
                            <tbody style="text-align:center">
                            <template v-for="(temp,index) in baseInfo.actionInfo" >
                                <tr :key="temp.riskName" v-bind:class="{ borderBottom1: index != baseInfo.actionInfo.length-1,borderBottom2: index == baseInfo.actionInfo.length-1 }">
                                
                                    <td :rowspan="baseInfo.actionInfo.length" v-if="index==0">客户行为检测</td>
                                    <td class="risk-item-name">{{temp.riskName}}</td>
                                    <td class="risk-item-level">
                                        {{temp.riskLevel==1?'低':(temp.riskLevel==2?'中':'高')}}  
                                    </td>
                                    <td v-if="temp.reportDetail.length>0">
                                        <span><a href="javascript:void(0)" style="color: #2ea5ff;text-decoration: blink;">频度规则详情</a></span>
                                        <ul class="risk-detail-list" >
                                            <span v-if="temp.reportDetail[0].detail != undefined">{{temp.reportDetail[0].detail}}</span>
                                            <li>
                                                <span>{{temp.reportDetail[0].data}}</span>
                                            </li>
                                        </ul>
                                    </td>
                                </tr>
                            </template>
                            <template v-for="(temp,index1) in baseInfo.platFormInfo" >
                                <tr :key="temp.riskName" v-bind:class="{ borderBottom1: index1 != baseInfo.platFormInfo.length-1,borderBottom2: index1 == baseInfo.platFormInfo.length-1 }">
                                    <td :rowspan="baseInfo.platFormInfo.length" v-if="index1==0">多平台借贷申请检测</td>
                                    <td class="risk-item-name">{{temp.riskName}}</td>
                                    <td class="risk-item-level">
                                        {{temp.riskLevel==1?'低':(temp.riskLevel==2?'中':'高')}}  
                                    </td>
                                    <td>
                                        <span>总数{{temp.dimenonReports.length}}</span><br/>
                                        <span><a href="javascript:void(0)" style="color: #2ea5ff;text-decoration: blink;">频度规则详情</a></span>
                                        <ul class="risk-detail-list">
                                            <li v-for="(tem ) in temp.dimenonReports" :key="tem.id">
                                                <span>{{tem.dimension}}</span>
                                                <ul>
                                                    <li v-for="(te) in tem.displayReportDOS" :key="te.displayName" style="margin-left:20px">
                                                        {{te.dimension}}
                                                        {{te.displayName}}&nbsp;&nbsp;{{te.count}}
                                                    </li>
                                                </ul>                                                
                                            </li>
                                        </ul>
                                    </td>
                                </tr>
                            </template> 
                            <template v-for="(temp,index1) in baseInfo.overdueInfo" >
                                <tr :key="temp.riskName" v-bind:class="{ borderBottom1: index1 != baseInfo.platFormInfo.length-1,borderBottom2: index1 == baseInfo.platFormInfo.length-1 }">
                                    <td :rowspan="baseInfo.overdueInfo.length" v-if="index1==0">命中信贷逾期名单</td>
                                    <td class="risk-item-name">{{temp.riskName}}</td>
                                    <td class="risk-item-level">
                                    </td>
                                    <td>
                                        <span>总数{{temp.tdOverDetailDTOList.length}}</span><br/>
                                        <span><a href="javascript:void(0)" style="color: #2ea5ff;text-decoration: blink;">频度规则详情</a></span>
                                        <ul class="risk-detail-list"  style="text-align: left;margin-left:150px;">
                                            <li v-for="(tem ,i) in temp.tdOverDetailDTOList" :key="i">
                                               <ul style="border-bottom:2px solid black">
                                                   <li>
                                                       <span>逾期区间</span>  {{tem.amountRange}}
                                                   </li>
                                                   <li>
                                                       <span>逾期天数</span>  {{tem.dayRange}}
                                                   </li>                                                   
                                                   <li>
                                                       <span>逾期次数</span>  {{tem.eachCount}}
                                                   </li>
                                                   <li>
                                                       <span>逾期时间</span>  {{tem.overdueTime}}
                                                   </li>
                                               </ul>
                                            </li>
                                        </ul>
                                    </td>
                                </tr>
                            </template>                               
                            <template v-for="(temp,index1) in baseInfo.badInfo" >
                                <tr :key="temp.riskName" v-bind:class="{ borderBottom1: index1 != baseInfo.platFormInfo.length-1,borderBottom2: index1 == baseInfo.platFormInfo.length-1 }">
                                    <td :rowspan="baseInfo.platFormInfo.length" v-if="index1==0">不良信息扫描</td>
                                    <td class="risk-item-name">{{temp.riskName}}</td>
                                    <td class="risk-item-level">
                                        {{temp.riskLevel==1?'低':(temp.riskLevel==2?'中':'高')}}  
                                    </td>
                                    <td>
                                        <span style="color:red">风险类型：{{temp.riskContent}}</span>
                                    </td>
                                </tr>
                            </template>                                                        

                             </tbody>
                        </table>                
                </el-card>
            </el-row>         
        </el-dialog>          -->
    </div>
</template>
<script>
import { httpGetCollecteffects } from "../../../service/http";
import { timeFormat } from "../../../config/time";
export default {
  data() {
    return {
      title: "催收效果统计",
      search: {},
      showVisible: true,
      showText: false
      //   baseInfo: {
      //     baseInfo: {
      //       reportNum: "181c2d1a-31e3-4bb6-a756-661e1bb3b954",
      //       score: 29,
      //       suggestion: 2,
      //       riskCount: null,
      //       createTime: "2019-01-14T08:57:38.000+0000",
      //       realName: "方彪",
      //       phoneNum: "17730215645",
      //       idNum: "34012319910630001X",
      //       bankCardNum: null,
      //       idCardAddress: "安徽省合肥市肥西县",
      //       phoneAddress: "安徽省合肥市"
      //     },
      //     contactInfo: [],
      //     platFormInfo: [
      //       {
      //         riskName: "1个月内申请人在多个平台申请借款",
      //         riskLevel: 1,
      //         dimenonReports: [
      //           {
      //             id: 19258,
      //             dimension: "借款人身份证",
      //             displayReportDOS: [
      //               {
      //                 displayName: "财产保险",
      //                 count: 1
      //               },
      //               {
      //                 displayName: "小额贷款公司",
      //                 count: 2
      //               }
      //             ]
      //           },
      //           {
      //             id: 19259,
      //             dimension: "借款人手机",
      //             displayReportDOS: [
      //               {
      //                 displayName: "财产保险",
      //                 count: 1
      //               },
      //               {
      //                 displayName: "小额贷款公司",
      //                 count: 1
      //               }
      //             ]
      //           }
      //         ]
      //       },
      //       {
      //         riskName: "3个月内申请人在多个平台申请借款",
      //         riskLevel: 1,
      //         dimenonReports: [
      //           {
      //             id: 19261,
      //             dimension: "借款人手机",
      //             displayReportDOS: [
      //               {
      //                 displayName: "财产保险",
      //                 count: 1
      //               },
      //               {
      //                 displayName: "小额贷款公司",
      //                 count: 1
      //               },
      //               {
      //                 displayName: "P2P网贷",
      //                 count: 1
      //               }
      //             ]
      //           },
      //           {
      //             id: 19262,
      //             dimension: "借款人身份证",
      //             displayReportDOS: [
      //               {
      //                 displayName: "一般消费分期平台",
      //                 count: 1
      //               },
      //               {
      //                 displayName: "财产保险",
      //                 count: 1
      //               },
      //               {
      //                 displayName: "小额贷款公司",
      //                 count: 2
      //               },
      //               {
      //                 displayName: "P2P网贷",
      //                 count: 1
      //               },
      //               {
      //                 displayName: "银行小微贷款",
      //                 count: 1
      //               }
      //             ]
      //           }
      //         ]
      //       }
      //     ],
      //     badInfo: [
      //       {
      //         riskName: "申请人信息命中中风险关注名单",
      //         riskLevel: 1,
      //         riskContent: "异常借款"
      //       }
      //     ],
      //     hitAreaInfo: [],
      //     actionInfo: [
      //       {
      //         id: 724,
      //         reportId: null,
      //         riskName: "3个月内身份证关联多个申请信息",
      //         reportDetail: [
      //           {
      //             id: null,
      //             detail: "3个月身份证关联手机号数：4",
      //             data:
      //               '["17730215645","182※※※※4558","181※※※※7127","150※※※※4103"]'
      //           }
      //         ]
      //       }
      //     ],
      //     overdueInfo: [],
      //     zhimaReportDO: null
      //   }
    };
  },
  methods: {
    getData(begainTimeString, endTimeString) {
      httpGetCollecteffects(begainTimeString, endTimeString)
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            let xData = data.data.map(v => v.name);
            let dataO = data.data.map(v => v.allActureRepayMoney);
            let dataT = data.data.map(v => v.allActureRepayLateFee);
            this.drawLine(xData, dataO, dataT);
          }
        })
        // .catch(err => {
        //   let data = {
        //     code: 200,
        //     msg: "提交成功",
        //     data: [
        //       {
        //         id: null,
        //         name: "csjl",
        //         allOverdueMoney: 0,
        //         all_late_fee: 0,
        //         allActureRepayMoney: 8460,
        //         allActureRepayLateFee: 1460,
        //         overdueMoney_m1: 0,
        //         late_free_m1: 0,
        //         collect_money_m1: 0,
        //         overdueMoney_m2: 0,
        //         late_free_m2: 0,
        //         collect_money_m2: 0,
        //         overdueMoney_m3: 0,
        //         late_free_m3: 0,
        //         collect_money_m3: 0,
        //         overdueMoney_m4: 0,
        //         late_free_m4: 0,
        //         collect_money_m4: 0
        //       },
        //       {
        //         id: null,
        //         name: "潘月",
        //         allOverdueMoney: 0,
        //         all_late_fee: 0,
        //         allActureRepayMoney: 1700,
        //         allActureRepayLateFee: 200,
        //         overdueMoney_m1: 0,
        //         late_free_m1: 0,
        //         collect_money_m1: 0,
        //         overdueMoney_m2: 0,
        //         late_free_m2: 0,
        //         collect_money_m2: 0,
        //         overdueMoney_m3: 0,
        //         late_free_m3: 0,
        //         collect_money_m3: 0,
        //         overdueMoney_m4: 0,
        //         late_free_m4: 0,
        //         collect_money_m4: 0
        //       },
        //       {
        //         id: null,
        //         name: "王亦婷",
        //         allOverdueMoney: 0,
        //         all_late_fee: 0,
        //         allActureRepayMoney: 1560,
        //         allActureRepayLateFee: 60,
        //         overdueMoney_m1: 0,
        //         late_free_m1: 0,
        //         collect_money_m1: 0,
        //         overdueMoney_m2: 0,
        //         late_free_m2: 0,
        //         collect_money_m2: 0,
        //         overdueMoney_m3: 0,
        //         late_free_m3: 0,
        //         collect_money_m3: 0,
        //         overdueMoney_m4: 0,
        //         late_free_m4: 0,
        //         collect_money_m4: 0
        //       },
        //       {
        //         id: null,
        //         name: "赵丹丹",
        //         allOverdueMoney: 0,
        //         all_late_fee: 0,
        //         allActureRepayMoney: 4290,
        //         allActureRepayLateFee: 190,
        //         overdueMoney_m1: 0,
        //         late_free_m1: 0,
        //         collect_money_m1: 0,
        //         overdueMoney_m2: 0,
        //         late_free_m2: 0,
        //         collect_money_m2: 0,
        //         overdueMoney_m3: 0,
        //         late_free_m3: 0,
        //         collect_money_m3: 0,
        //         overdueMoney_m4: 0,
        //         late_free_m4: 0,
        //         collect_money_m4: 0
        //       },
        //       {
        //         id: null,
        //         name: "陈绍群",
        //         allOverdueMoney: 0,
        //         all_late_fee: 0,
        //         allActureRepayMoney: 12845,
        //         allActureRepayLateFee: 745,
        //         overdueMoney_m1: 0,
        //         late_free_m1: 0,
        //         collect_money_m1: 0,
        //         overdueMoney_m2: 0,
        //         late_free_m2: 0,
        //         collect_money_m2: 0,
        //         overdueMoney_m3: 0,
        //         late_free_m3: 0,
        //         collect_money_m3: 0,
        //         overdueMoney_m4: 0,
        //         late_free_m4: 0,
        //         collect_money_m4: 0
        //       },
        //       {
        //         id: null,
        //         name: "梁华金",
        //         allOverdueMoney: 0,
        //         all_late_fee: 0,
        //         allActureRepayMoney: 3185,
        //         allActureRepayLateFee: 385,
        //         overdueMoney_m1: 0,
        //         late_free_m1: 0,
        //         collect_money_m1: 0,
        //         overdueMoney_m2: 0,
        //         late_free_m2: 0,
        //         collect_money_m2: 0,
        //         overdueMoney_m3: 0,
        //         late_free_m3: 0,
        //         collect_money_m3: 0,
        //         overdueMoney_m4: 0,
        //         late_free_m4: 0,
        //         collect_money_m4: 0
        //       },
        //       {
        //         id: null,
        //         name: "刘婧",
        //         allOverdueMoney: 0,
        //         all_late_fee: 0,
        //         allActureRepayMoney: 8470,
        //         allActureRepayLateFee: 670,
        //         overdueMoney_m1: 0,
        //         late_free_m1: 0,
        //         collect_money_m1: 0,
        //         overdueMoney_m2: 0,
        //         late_free_m2: 0,
        //         collect_money_m2: 0,
        //         overdueMoney_m3: 0,
        //         late_free_m3: 0,
        //         collect_money_m3: 0,
        //         overdueMoney_m4: 0,
        //         late_free_m4: 0,
        //         collect_money_m4: 0
        //       }
        //     ]
        //   };
        //   if (data.code == 200) {
        //     let xData = data.data.map(v => v.name);
        //     let dataO = data.data.map(v => v.allActureRepayMoney);
        //     let dataT = data.data.map(v => v.allActureRepayLateFee);
        //     this.drawLine(xData, dataO, dataT);
        //   }
        // });
    },
    handleSearch() {
      if (this.search.time && this.search.time.length) {
        this.getData(
          this.search.time[0] + " 00:00:00",
          timeFormat(this.search.time[1], 1) + " 00:00:00"
        );
      } else {
        this.getData();
      }
    },
    reset() {
      this.search = {};
      this.getData();
    },
    drawLine(xData, dataO, dataT) {
      // 基于准备好的dom，初始化echarts实例
      let myChart = this.$echarts.init(document.getElementById("myChart"));
      var labelOption = {
        normal: {
          formatter: "{c}  {name|{a}}",
          fontSize: 16,
          rich: {
            name: {
              textBorderColor: "#fff"
            }
          }
        }
      };
      // 绘制图表
      myChart.setOption({
        title: { text: "客户还款情况" },
        color: ["#95CDFE", "#424247"],
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow"
          }
        },
        legend: {
          data: ["还款金额", "滞纳金"]
        },
        xAxis: [
          {
            type: "category",
            axisTick: { show: false },
            data: xData
          }
        ],
        yAxis: [
          {
            type: "value"
          }
        ],
        series: [
          {
            name: "还款金额",
            type: "bar",
            barGap: 0,
            label: labelOption,
            data: dataO
          },
          {
            name: "滞纳金",
            type: "bar",
            label: labelOption,
            data: dataT
          }
        ]
      });
    }
  },
  mounted() {
    this.getData();
  }
};
</script>
<style scoped>
</style>

<style scoped>
.title {
  padding: 10px;
  font-weight: bold;
  font-size: 18px;
  width: 100%;
}
.input-width {
  width: 215px;
}
.flx {
  position: absolute;
  width: 85%;
  top: 0px;
  padding-top: 10px;
  overflow: hidden;
  line-height: 30px;
}
.fl {
  float: left;

  font-size: 24px;
  font-weight: bold;
}
.fl span {
  font-size: 12px;
  font-weight: normal;
}
.fr {
  float: right;
  font-size: 12px;
}
.bar {
  position: relative;
  width: 50%;
  display: inline-block;
}
.progress-text {
  position: absolute;
  text-align: center;
  font-size: 32px;
  line-height: 126px;
  width: 126px;
  height: 126px;
}
.progress-text p {
  width: 100%;
  position: absolute;
  top: 80px;
  font-size: 12px;
  line-height: 20px;
  text-align: center;
}
.table {
  border-spacing: 2px;
  border-color: grey;
  font-size: 13px;
  padding-left: 20px;
  width: 100%;
  display: table;
  border-collapse: collapse;
}
thead tr {
  border-bottom: 2px solid #eaeaea;
}
th {
  padding-left: 10px;
  height: 36px;
}
.borderBottom1 {
  border-bottom: 1px solid #eaeaea;
}
.borderBottom2 {
  border-bottom: 1px solid #eaeaea;
}
.risk-detail-list {
  margin-left: 40px;
}
ul,
li {
  list-style: none;
}
.check-table {
  width: 100%;
}
</style>
