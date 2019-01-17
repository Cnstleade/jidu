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
        <el-table
            :data="tableData"  
            border  
            class="m20"
            v-loading="loading"
                 highlight-current-row style="width: 100%;font-weight:bold"
                  :row-class-name="tableRowClassName"
                  id="sss"
          >
            <el-table-column prop="dateTimeString" label="日期"  align="center" width="100"></el-table-column>
            <el-table-column  prop="allSalesMoney" label="当日应还款额" align="center" width="166" class="red">
                <el-table-column label="当日应还款订单数" align="center"  width="166" >
                        <template slot-scope="scope" >
                           <div style="background:red;color:#fff;">
                            {{scope.row.allSalesMoney}}<br>
                            {{scope.row.allSalesCount}}
                            </div> 
                        </template> 
                </el-table-column>
            </el-table-column> 
            <el-table-column  prop="allSalesMoney" label="当日实际还款额" align="center" width="166">
                <el-table-column label="当日实际还款订单数" align="center"  width="166">
                        <template slot-scope="scope">
                            <div style="background:red;color:#fff;">
                            {{scope.row.repayMoney}}<br>
                            {{scope.row.repayNumber}}
                            </div> 
                        </template> 
                </el-table-column>
            </el-table-column>             
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="当日还款率"  align="center" width="100">
                        <template slot-scope="scope">
                            <div :style="{
                                background:(((scope.row.repayMoney)/scope.row.allSalesMoney).toFixed(4)*100).toFixed(2) &lt; 88 ?'rgb(254,3,3)':
                                (((scope.row.repayMoney)/scope.row.allSalesMoney).toFixed(4)*100).toFixed(2) &lt; 91 &&  (((scope.row.repayMoney)/scope.row.allSalesMoney).toFixed(4)*100).toFixed(2) >= 88?'rgb(254,164,17)':
                                (((scope.row.repayMoney)/scope.row.allSalesMoney).toFixed(4)*100).toFixed(2) &lt; 94 &&  (((scope.row.repayMoney)/scope.row.allSalesMoney).toFixed(4)*100).toFixed(2) >= 91?'rgb(19,15,253)':
                               (((scope.row.repayMoney)/scope.row.allSalesMoney).toFixed(4)*100).toFixed(2) >= 94?'rgb(0,127,9)':
                                ''
                                ,color:'#fff'}">
                                {{(((scope.row.repayMoney)/scope.row.allSalesMoney).toFixed(4)*100).toFixed(2)}}%<br>
                                {{(((scope.row.repayNumber)/scope.row.allSalesCount).toFixed(4)*100).toFixed(2)}}%
                            </div>
                        </template> 
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D01"  align="center" width="100">
                        <template slot-scope="scope">
                            {{scope.row.repayMoney?scope.row.repayMoney:''}}<br>
                            {{scope.row.repayNumber?scope.row.repayNumber:''}}
                        </template> 
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D02"  align="center" width="100">
                        <template slot-scope="scope">
                            {{scope.row.repayDay1Money?scope.row.repayDay1Money:''}}<br>
                            {{scope.row.repayDay1Number?scope.row.repayDay1Number:''}}
                        </template>                
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D03"  align="center" width="100">
                        <template slot-scope="scope">
                            {{scope.row.repayDay2Money?scope.row.repayDay2Money:''}}<br>
                            {{scope.row.repayDay2Number?scope.row.repayDay2Number:''}}
                        </template>                
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D04"  align="center" width="100">
                        <template slot-scope="scope">
         {{scope.row.repayDay3Money?scope.row.repayDay3Money:''}}<br>
                            {{scope.row.repayDay3Number?scope.row.repayDay3Number:''}}
                        </template>                
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D05"  align="center" width="100">
                        <template slot-scope="scope">
                                                      {{scope.row.repayDay4Money?scope.row.repayDay4Money:''}}<br>
                            {{scope.row.repayDay4Number?scope.row.repayDay4Number:''}}
                    
                        </template>                
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D06"  align="center" width="100">
                        <template slot-scope="scope">
                                    {{scope.row.repayDay5Money?scope.row.repayDay5Money:''}}<br>
                            {{scope.row.repayDay5Number?scope.row.repayDay5Number:''}}
                     
                        </template>                
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D07"  align="center" width="100">
                        <template slot-scope="scope">
                                   {{scope.row.repayDay6Money?scope.row.repayDay6Money:''}}<br>
                            {{scope.row.repayDay6Number?scope.row.repayDay6Number:''}}
                        </template>                
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D08"  align="center" width="100">
                        <template slot-scope="scope">
                             {{scope.row.repayDay7Money?scope.row.repayDay7Money:''}}<br>
                            {{scope.row.repayDay7Number?scope.row.repayDay7Number:''}}
                        </template>                
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D09"  align="center" width="100">
                        <template slot-scope="scope">
                              {{scope.row.repayDay8Money?scope.row.repayDay8Money:''}}<br>
                            {{scope.row.repayDay8Number?scope.row.repayDay8Number:''}}
                        </template>                
            </el-table-column>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="D010"  align="center" width="100">
                        <template slot-scope="scope">
                                 {{scope.row.repayDay9Money?scope.row.repayDay9Money:''}}<br>
                            {{scope.row.repayDay9Number?scope.row.repayDay9Number:''}}
                        </template>                
            </el-table-column>
            <!-- <el-table-column prop="needRepayTotalSalesMoney" label="待回款"  align="center" width="100">
                        <template slot-scope="scope">
                            {{scope.row.totalSalesMoney + scope.row.alreadyRepayotalLatefree-scope.row.alreadyRepayTotalSalesMoney}}
                        </template> 
            </el-table-column>
            <el-table-column prop="withdrawdateString" label="总滞纳金"  align="center" width="100">
                        <template slot-scope="scope">
                            {{scope.row.alreadyRepayotalLatefree &lt; 0 ? 0 : scope.row.alreadyRepayotalLatefree}}
                        </template>
            </el-table-column>
            <el-table-column prop="withdrawdateString" label="未还占比"  align="center" width="100">
                        <template slot-scope="scope">
                            {{(((scope.row.totalSalesMoney + scope.row.alreadyRepayotalLatefree-scope.row.alreadyRepayTotalSalesMoney)/scope.row.totalSalesMoney).toFixed(4)*100).toFixed(2)}}%
                        </template>
            </el-table-column>                   
            <el-table-column prop="withdrawdateString" label="回款率"  align="center" width="100">
                       <template slot-scope="scope">
                           {{((((scope.row.alreadyRepayTotalSalesMoney+(scope.row.alreadyRepayotalLatefree &lt; 0 ? 0 : scope.row.alreadyRepayotalLatefree)))/scope.row.totalSalesMoney).toFixed(4)*100).toFixed(2)}}%
                        </template>
            </el-table-column>                    -->
        </el-table>
        <el-row class="m20" v-if="total>0">
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
    </div>
</template>
<script>
import { httpRepayprobabilityanalysis } from "../../../service/http";
import { timeFormat } from "../../../config/time";
import miment from "miment";
export default {
  data() {
    return {
      title: "回款率统计",
      search: {},
      date: false,
      preM: null,
      afterM: null,
      npage: 1,
      pagesize: 10,
      total: 0,
      tableData: [],
      loading: false,
      dateArr: []
    };
  },
  methods: {
    tableRowClassName({ row, rowIndex }) {
      let i =
        ((row.repayMoney / row.allSalesMoney).toFixed(4) * 100).toFixed(2) *
        100;
      //   console.log(i)
      //   if (i >= 0 && i < 5) {
      //     return "";
      //   } else if (i >= 5 && i < 10) {
      //     return "";
      //   } else if (i >= 10 && i < 15) {
      //     return "";
      //   } else if (i >= 15) {
      //     return "red";
      //   }
      if (i < 85) {
        return "red";
      }
      return "";
    },
    getData(npage, pagesize, begainTimeString, endTimeString) {
      httpRepayprobabilityanalysis(
        npage,
        pagesize,

        begainTimeString,
        endTimeString
      )
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.npage = data.data.npage;
            this.total = data.data.allpage;
            this.tableData = data.data.list;
            var currentDate = this.tableData[0].list[0].repayDateString;
            var dateArr = [];
            if (this.date) {
              for (var num = 1; num <= 7; num++) {
                for (var num = 0; num < 7; num++) {
                  dateArr.push(
                    miment(currentDate)
                      .add(num, "MM")
                      .format("YYYY-MM")
                  );
                }
              }
            } else {
              for (var num = 0; num < 7; num++) {
                dateArr.push(
                  miment(currentDate)
                    .add(num, "DD")
                    .format("YYYY-MM-DD")
                );
              }
            }
            this.tableData.forEach(v => {
              dateArr.forEach(d => {
                var arr = v.list.find(a => a.repayDateString == d);
                v[d] = arr;
              });
            });
            this.dateArr = dateArr;
          }
        })
        .catch(err => {
          let data = {
            code: 200,
            msg: "提交成功",
            data: {
              npage: 1,
              allpage: 56,
              pagesize: 50,
              allsize: 0,
              list: [
                {
                  dateTimeString: "2018-12-18",
                  allSalesMoney: 1500,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 1500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-17",
                  allSalesMoney: 2700,
                  exceedMonry: 0,
                  allSalesCount: 3,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 3,
                  repayMoney: 2700,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-16",
                  allSalesMoney: 1400,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1400,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-14",
                  allSalesMoney: 1000,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 1,
                  repayDay7Money: 1000,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-13",
                  allSalesMoney: 1000,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-12",
                  allSalesMoney: 1500,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-11",
                  allSalesMoney: 2500,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-10",
                  allSalesMoney: 2300,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 2300,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-09",
                  allSalesMoney: 1000,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 0,
                  repayMoney: 0,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-06",
                  allSalesMoney: 1500,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-05",
                  allSalesMoney: 5200,
                  exceedMonry: 0,
                  allSalesCount: 5,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 4,
                  repayMoney: 4200,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-03",
                  allSalesMoney: 1400,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 0,
                  repayMoney: 0,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-02",
                  allSalesMoney: 1000,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 1000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-12-01",
                  allSalesMoney: 1500,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 1,
                  repayDay5Money: 1500,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-30",
                  allSalesMoney: 1400,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1400,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-29",
                  allSalesMoney: 3000,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 3000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 1,
                  repayDay4Money: 1500,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-28",
                  allSalesMoney: 1500,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 1500,
                  repayDay1Number: 2,
                  repayDay1Money: 1500,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-25",
                  allSalesMoney: 1500,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 0,
                  repayMoney: 0,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-24",
                  allSalesMoney: 2800,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 2800,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-23",
                  allSalesMoney: 2900,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 2900,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-22",
                  allSalesMoney: 2900,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1400,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 1,
                  repayDay3Money: 1400,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-21",
                  allSalesMoney: 1200,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 0,
                  repayMoney: 0,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-20",
                  allSalesMoney: 1400,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 0,
                  repayMoney: 0,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-19",
                  allSalesMoney: 1200,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1200,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-18",
                  allSalesMoney: 4400,
                  exceedMonry: 0,
                  allSalesCount: 4,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 4,
                  repayMoney: 4400,
                  repayDay1Number: 2,
                  repayDay1Money: 1900,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-17",
                  allSalesMoney: 1400,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1400,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-16",
                  allSalesMoney: 2800,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 2800,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-15",
                  allSalesMoney: 1100,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1100,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-14",
                  allSalesMoney: 2600,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 2600,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 1,
                  repayDay2Money: 1300,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-13",
                  allSalesMoney: 7800,
                  exceedMonry: 0,
                  allSalesCount: 7,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 4,
                  repayMoney: 5000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 1,
                  repayDay5Money: 1300,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-12",
                  allSalesMoney: 2600,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1300,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-11",
                  allSalesMoney: 19400,
                  exceedMonry: 0,
                  allSalesCount: 17,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 6,
                  repayMoney: 7000,
                  repayDay1Number: 5,
                  repayDay1Money: 5700,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-10",
                  allSalesMoney: 6400,
                  exceedMonry: 0,
                  allSalesCount: 5,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 5,
                  repayMoney: 6400,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 2,
                  repayDay2Money: 2300,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-09",
                  allSalesMoney: 5500,
                  exceedMonry: 0,
                  allSalesCount: 7,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 1500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-08",
                  allSalesMoney: 63300,
                  exceedMonry: 0,
                  allSalesCount: 75,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-07",
                  allSalesMoney: 2500,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-05",
                  allSalesMoney: 3600,
                  exceedMonry: 0,
                  allSalesCount: 4,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 0,
                  repayMoney: 0,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-11-04",
                  allSalesMoney: 13500,
                  exceedMonry: 0,
                  allSalesCount: 14,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 2000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 1,
                  repayDay3Money: 1000,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-10-30",
                  allSalesMoney: 500,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-09-04",
                  allSalesMoney: 1500,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-09-03",
                  allSalesMoney: 3000,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 3000,
                  repayDay1Number: 1,
                  repayDay1Money: 1500,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-08-31",
                  allSalesMoney: 4500,
                  exceedMonry: 0,
                  allSalesCount: 3,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 3000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-08-30",
                  allSalesMoney: 1500,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-08-29",
                  allSalesMoney: 4500,
                  exceedMonry: 0,
                  allSalesCount: 4,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 3,
                  repayMoney: 3000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 2,
                  repayDay2Money: 1500,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-08-28",
                  allSalesMoney: 30700,
                  exceedMonry: 0,
                  allSalesCount: 21,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 4,
                  repayMoney: 6000,
                  repayDay1Number: 3,
                  repayDay1Money: 4500,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-08-27",
                  allSalesMoney: 4100,
                  exceedMonry: 0,
                  allSalesCount: 3,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 0,
                  repayMoney: 0,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-08-25",
                  allSalesMoney: 1300,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 0,
                  repayMoney: 0,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-08-24",
                  allSalesMoney: 3000,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 2,
                  repayMoney: 3000,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-08-23",
                  allSalesMoney: 3000,
                  exceedMonry: 0,
                  allSalesCount: 2,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                },
                {
                  dateTimeString: "2018-08-22",
                  allSalesMoney: 1500,
                  exceedMonry: 0,
                  allSalesCount: 1,
                  exceedCount: null,
                  channelName: null,
                  repayNumber: 1,
                  repayMoney: 1500,
                  repayDay1Number: 0,
                  repayDay1Money: 0,
                  repayDay2Number: 0,
                  repayDay2Money: 0,
                  repayDay3Number: 0,
                  repayDay3Money: 0,
                  repayDay4Number: 0,
                  repayDay4Money: 0,
                  repayDay5Number: 0,
                  repayDay5Money: 0,
                  repayDay6Number: 0,
                  repayDay6Money: 0,
                  repayDay7Number: 0,
                  repayDay7Money: 0,
                  repayDay8Number: 0,
                  repayDay8Money: 0,
                  repayDay9Number: 0,
                  repayDay9Money: 0,
                  repayDay10Number: 0,
                  repayDay10Money: 0
                }
              ],
              type: 0,
              countVo: null
            }
          };
          if (data.code == 200) {
            this.npage = data.data.npage;
            this.total = data.data.allpage;
            this.tableData = data.data.list;
          }
        });
    },
    handleSearch() {
      if (this.search.time && this.search.time.length) {
        this.getData(
          this.npage,
          this.pagesize,
          this.search.time[0] + " 00:00:00",
          timeFormat(this.search.time[1], 1) + " 00:00:00"
        );
      } else {
        this.getData(this.npage, this.pagesize);
      }
    },
    handleCurrentChange(val) {
      this.npage = val;
      this.handleSearch(false);
    },
    handleSizeChange(val) {
      this.pagesize = val;
      this.handleSearch(false);
    },
    reset() {
      this.preM = null;
      this.afterM = null;
      this.search = {};
      this.npage = 1;
      this.pagesize = 10;
      this.getData(this.npage, this.pagesize);
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
<style >
.el-table .red {
  background: #fe0000;
  color: #fff;
}
.el-table .yellow {
  background: #fda500;
  color: #fff;
}
.el-table .green {
  background: #0000fe;
  color: #fff;
}
.el-table .blue {
  background: #008104;
  color: #fff;
}
#sss .el-table__body tr:hover > td {
  color: #000;
}
</style>