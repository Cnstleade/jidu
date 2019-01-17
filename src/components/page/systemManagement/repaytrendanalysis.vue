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
              <div style="flex-grow:1" >
                <el-switch
                    v-model="date"
                    active-color="#13ce66"
                    inactive-color="#ff4949"
                    active-text="按月"
                    inactive-text="按天">
                </el-switch>
              </div>    
                    <el-button  type="primary" @click="reset">重置</el-button>
                    <template v-if="date">
                        <el-date-picker
                          class="l20"
                          v-model="preM"
                             value-format="yyyy-MM"
                          type="month"
                          placeholder="开始月份">
                        </el-date-picker>
                        <span class="l20" style="line-height: 32px;
                            font-size: 12px;
                            font-weight: bold;">
                        至
                         </span>
                        <el-date-picker
                        class="l20"
                          v-model="afterM"
                             value-format="yyyy-MM"
                          type="month"
                          placeholder="结束月份">
                        </el-date-picker>
                    </template>
                    <template v-else>
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
                    </template>
              
                    <el-button @click="handleSearch" class="l20" style="margin-left:20px" icon="el-icon-search"  type="success" circle></el-button>                                                                  
            </el-col>             
        </el-row>        
        <el-table
            :data="tableData"  
            border  
            class="m20"
            v-loading="loading"
                 highlight-current-row style="width: 100%;font-weight:bold"
          >
            <el-table-column prop="withdrawdateString"  align="center" width="100"></el-table-column>
            <el-table-column  prop="allSalesMoney" label="销售量" align="center" width="166">
                <el-table-column label="销售额" align="center"  width="166">
                    <el-table-column label="放款额" align="center"  width="166">
                        <template slot-scope="scope">
                           {{scope.row.totalWithdrawNumber}}<br>
                           <span style="color:#527F88">{{scope.row.totalSalesMoney}}</span><br>
                           <span style="color:#1FB8C7">{{scope.row.totalCostsMoney}}</span>
                        </template> 
                    </el-table-column>
                </el-table-column>
            </el-table-column> 
            <template v-for="(temp,index) in dateArr">
              <el-table-column :key='index'  prop="allSalesMoney" :label="temp" align="center" width="120">
                        <template slot-scope="scope" >
                          <template v-if="scope.row[temp]">
                                  <span style="color:#527F88">{{scope.row[temp].alreadyRepayNumber}}</span><br>
                                  <span style="color:#527F88">{{scope.row[temp].alreadyRepaySalesMoney}}</span><br>
                                  <span style="color:#527F88">{{scope.row[temp].alreadyRepayLatefree?scope.row[temp].alreadyRepayLatefree &lt; 0? 0 :scope.row[temp].alreadyRepayLatefree:0}}</span><br>
                                   <span style="color:#527F88">{{(((scope.row[temp].alreadyRepaySalesMoney)/scope.row.totalSalesMoney).toFixed(4)*100).toFixed(2)}}%</span><br>
                                   <hr>
                                   <span style="color:#FE5540">{{scope.row[temp].alreadyRepayNumberNow}}</span><br>
                                  <span style="color:#FE5540">{{scope.row[temp].alreadyRepaySalesMoneyNow}}</span><br>
                                   <span style="color:#FE5540">{{(((scope.row[temp].alreadyRepaySalesMoneyNow)/scope.row.totalSalesMoney).toFixed(4)*100).toFixed(2)}}%</span>
                          </template>
                        </template>                   
              </el-table-column>
            </template>
            <el-table-column prop="alreadyRepayTotalSalesMoney" label="已回款"  align="center" width="100"></el-table-column>
            <el-table-column prop="needRepayTotalSalesMoney" label="待回款"  align="center" width="100">
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
            </el-table-column>                   
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
import { httpRepaytrendanalysis } from "../../../service/http";
import { timeFormat } from "../../../config/time";
import miment from "miment";
export default {
  data() {
    return {
      title: "还款趋势分析",
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
    getData(npage, pagesize, salesCountType, begainTimeString, endTimeString) {
      httpRepaytrendanalysis(
        npage,
        pagesize,
        salesCountType,
        begainTimeString,
        endTimeString
      ).then(res => {
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
      });
      // .catch(err => {
      //   let data = {
      //     code: 200,
      //     msg: "提交成功",
      //     data: {
      //       npage: 1,
      //       allpage: 56,
      //       pagesize: 50,
      //       allsize: 0,
      //       list: [
      //         {
      //           withdrawdateString: "2018-12-12",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 1500,
      //           totalCostsMoney: 1050,
      //           alreadyRepayTotalSalesMoney: 1400,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: -100,
      //           list: [
      //             {
      //               withdrawDateString: "2018-12-12",
      //               repayDateString: "2018-12-12",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 1400,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 1400,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-12-11",
      //           totalWithdrawNumber: 3,
      //           totalSalesMoney: 2700,
      //           totalCostsMoney: 1890,
      //           alreadyRepayTotalSalesMoney: 2600,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: -100,
      //           list: [
      //             {
      //               withdrawDateString: "2018-12-11",
      //               repayDateString: "2018-12-11",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-12-11",
      //               repayDateString: "2018-12-17",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1100,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 3,
      //               alreadyRepaySalesMoneyNow: 2600,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-12-10",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1400,
      //           totalCostsMoney: 980,
      //           alreadyRepayTotalSalesMoney: 1400,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-12-10",
      //               repayDateString: "2018-12-16",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1400,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1400,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-12-08",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1000,
      //           totalCostsMoney: 700,
      //           alreadyRepayTotalSalesMoney: 1000,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-12-08",
      //               repayDateString: "2018-12-21",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1000,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1000,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-12-07",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1000,
      //           totalCostsMoney: 700,
      //           alreadyRepayTotalSalesMoney: 1000,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-12-07",
      //               repayDateString: "2018-12-13",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1000,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1000,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-12-06",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1500,
      //           totalCostsMoney: 1050,
      //           alreadyRepayTotalSalesMoney: 1700,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 200,
      //           list: [
      //             {
      //               withdrawDateString: "2018-12-06",
      //               repayDateString: "2019-01-03",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1700,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1700,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-12-05",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 2500,
      //           totalCostsMoney: 1750,
      //           alreadyRepayTotalSalesMoney: 1000,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-12-05",
      //               repayDateString: "2018-12-11",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1000,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1000,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-12-04",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 2300,
      //           totalCostsMoney: 1610,
      //           alreadyRepayTotalSalesMoney: 2300,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-12-04",
      //               repayDateString: "2018-12-10",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 2300,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 2300,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-12-03",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1000,
      //           totalCostsMoney: 700,
      //           alreadyRepayTotalSalesMoney: 0,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: []
      //         },
      //         {
      //           withdrawdateString: "2018-11-30",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1500,
      //           totalCostsMoney: 1050,
      //           alreadyRepayTotalSalesMoney: 1500,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-30",
      //               repayDateString: "2018-12-06",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-29",
      //           totalWithdrawNumber: 5,
      //           totalSalesMoney: 5200,
      //           totalCostsMoney: 3640,
      //           alreadyRepayTotalSalesMoney: 4200,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-29",
      //               repayDateString: "2018-12-04",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1000,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1000,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-29",
      //               repayDateString: "2018-12-05",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 2500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-29",
      //               repayDateString: "2018-12-06",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 1700,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 4,
      //               alreadyRepaySalesMoneyNow: 4200,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-27",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1400,
      //           totalCostsMoney: 980,
      //           alreadyRepayTotalSalesMoney: 0,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: []
      //         },
      //         {
      //           withdrawdateString: "2018-11-26",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 1000,
      //           totalCostsMoney: 700,
      //           alreadyRepayTotalSalesMoney: 1000,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-26",
      //               repayDateString: "2018-11-26",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 1000,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 1000,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-25",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1500,
      //           totalCostsMoney: 1050,
      //           alreadyRepayTotalSalesMoney: 1875,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 375,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-25",
      //               repayDateString: "2018-12-06",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1875,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1875,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-24",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1400,
      //           totalCostsMoney: 980,
      //           alreadyRepayTotalSalesMoney: 1400,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-24",
      //               repayDateString: "2018-11-30",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1400,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1400,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-23",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 3000,
      //           totalCostsMoney: 2100,
      //           alreadyRepayTotalSalesMoney: 3300,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 300,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-23",
      //               repayDateString: "2018-11-29",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-23",
      //               repayDateString: "2018-12-03",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1800,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 3300,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-22",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 1500,
      //           totalCostsMoney: 1050,
      //           alreadyRepayTotalSalesMoney: 1575,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 75,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-22",
      //               repayDateString: "2018-11-29",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 1575,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 1575,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-19",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1500,
      //           totalCostsMoney: 1050,
      //           alreadyRepayTotalSalesMoney: 0,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: []
      //         },
      //         {
      //           withdrawdateString: "2018-11-18",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 2800,
      //           totalCostsMoney: 1960,
      //           alreadyRepayTotalSalesMoney: 2800,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-18",
      //               repayDateString: "2018-11-22",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-18",
      //               repayDateString: "2018-11-24",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1300,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 2800,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-17",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 2900,
      //           totalCostsMoney: 2030,
      //           alreadyRepayTotalSalesMoney: 2900,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-17",
      //               repayDateString: "2018-11-23",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 2900,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 2900,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-16",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 2900,
      //           totalCostsMoney: 2030,
      //           alreadyRepayTotalSalesMoney: 1610,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 210,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-16",
      //               repayDateString: "2018-11-25",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1610,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1610,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-15",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1200,
      //           totalCostsMoney: 840,
      //           alreadyRepayTotalSalesMoney: 0,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: []
      //         },
      //         {
      //           withdrawdateString: "2018-11-14",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1400,
      //           totalCostsMoney: 980,
      //           alreadyRepayTotalSalesMoney: 0,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: []
      //         },
      //         {
      //           withdrawdateString: "2018-11-13",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1200,
      //           totalCostsMoney: 840,
      //           alreadyRepayTotalSalesMoney: 1200,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-13",
      //               repayDateString: "2018-11-18",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1200,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1200,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-12",
      //           totalWithdrawNumber: 4,
      //           totalSalesMoney: 4400,
      //           totalCostsMoney: 3080,
      //           alreadyRepayTotalSalesMoney: 4470,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 70,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-12",
      //               repayDateString: "2018-11-17",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1400,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1400,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-12",
      //               repayDateString: "2018-11-18",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1100,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 2500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-12",
      //               repayDateString: "2018-11-19",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 1970,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 4,
      //               alreadyRepaySalesMoneyNow: 4470,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-11",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1400,
      //           totalCostsMoney: 980,
      //           alreadyRepayTotalSalesMoney: 1400,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-11",
      //               repayDateString: "2018-11-17",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1400,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1400,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-10",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 2800,
      //           totalCostsMoney: 1960,
      //           alreadyRepayTotalSalesMoney: 2800,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-10",
      //               repayDateString: "2018-11-16",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 2800,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 2800,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-09",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1100,
      //           totalCostsMoney: 770,
      //           alreadyRepayTotalSalesMoney: 1100,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-09",
      //               repayDateString: "2018-11-15",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1100,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1100,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-08",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 2600,
      //           totalCostsMoney: 1820,
      //           alreadyRepayTotalSalesMoney: 2730,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 130,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-08",
      //               repayDateString: "2018-11-14",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1300,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1300,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-08",
      //               repayDateString: "2018-11-16",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1430,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 2730,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-07",
      //           totalWithdrawNumber: 7,
      //           totalSalesMoney: 7800,
      //           totalCostsMoney: 5460,
      //           alreadyRepayTotalSalesMoney: 5325,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 325,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-07",
      //               repayDateString: "2018-11-10",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1300,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1300,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-07",
      //               repayDateString: "2018-11-13",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 2400,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 3,
      //               alreadyRepaySalesMoneyNow: 3700,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-07",
      //               repayDateString: "2018-11-18",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1625,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 4,
      //               alreadyRepaySalesMoneyNow: 5325,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-06",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 2600,
      //           totalCostsMoney: 1820,
      //           alreadyRepayTotalSalesMoney: 1300,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-06",
      //               repayDateString: "2018-12-14",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1300,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1300,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-05",
      //           totalWithdrawNumber: 17,
      //           totalSalesMoney: 19400,
      //           totalCostsMoney: 13580,
      //           alreadyRepayTotalSalesMoney: 7260,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 260,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-05",
      //               repayDateString: "2018-11-11",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1300,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1300,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-05",
      //               repayDateString: "2018-11-12",
      //               alreadyRepayNumber: 5,
      //               alreadyRepaySalesMoney: 5960,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 6,
      //               alreadyRepaySalesMoneyNow: 7260,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-04",
      //           totalWithdrawNumber: 5,
      //           totalSalesMoney: 6400,
      //           totalCostsMoney: 4480,
      //           alreadyRepayTotalSalesMoney: 6630,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 230,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-04",
      //               repayDateString: "2018-11-10",
      //               alreadyRepayNumber: 3,
      //               alreadyRepaySalesMoney: 4100,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 3,
      //               alreadyRepaySalesMoneyNow: 4100,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-04",
      //               repayDateString: "2018-11-12",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 2530,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 5,
      //               alreadyRepaySalesMoneyNow: 6630,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-03",
      //           totalWithdrawNumber: 7,
      //           totalSalesMoney: 5500,
      //           totalCostsMoney: 3850,
      //           alreadyRepayTotalSalesMoney: 1500,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-03",
      //               repayDateString: "2018-11-03",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-11-03",
      //               repayDateString: "2018-11-09",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1000,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-02",
      //           totalWithdrawNumber: 75,
      //           totalSalesMoney: 63300,
      //           totalCostsMoney: 44310,
      //           alreadyRepayTotalSalesMoney: 500,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-02",
      //               repayDateString: "2018-11-06",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-11-01",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 2500,
      //           totalCostsMoney: 1750,
      //           alreadyRepayTotalSalesMoney: 1000,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-11-01",
      //               repayDateString: "2018-11-07",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1000,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1000,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-10-30",
      //           totalWithdrawNumber: 4,
      //           totalSalesMoney: 3600,
      //           totalCostsMoney: 2520,
      //           alreadyRepayTotalSalesMoney: 0,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: []
      //         },
      //         {
      //           withdrawdateString: "2018-10-29",
      //           totalWithdrawNumber: 14,
      //           totalSalesMoney: 13500,
      //           totalCostsMoney: 9450,
      //           alreadyRepayTotalSalesMoney: 2300,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 300,
      //           list: [
      //             {
      //               withdrawDateString: "2018-10-29",
      //               repayDateString: "2018-11-03",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1000,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1000,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-10-29",
      //               repayDateString: "2018-11-07",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1300,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 2300,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-10-24",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 500,
      //           totalCostsMoney: 375,
      //           alreadyRepayTotalSalesMoney: 500,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-10-24",
      //               repayDateString: "2018-10-24",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-28",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1500,
      //           totalCostsMoney: 1200,
      //           alreadyRepayTotalSalesMoney: 1500,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-28",
      //               repayDateString: "2018-09-04",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-27",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 3000,
      //           totalCostsMoney: 2400,
      //           alreadyRepayTotalSalesMoney: 3060,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 60,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-27",
      //               repayDateString: "2018-08-27",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-08-27",
      //               repayDateString: "2018-09-04",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1560,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 3060,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-24",
      //           totalWithdrawNumber: 3,
      //           totalSalesMoney: 4500,
      //           totalCostsMoney: 3600,
      //           alreadyRepayTotalSalesMoney: 3000,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-24",
      //               repayDateString: "2018-08-24",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-08-24",
      //               repayDateString: "2018-08-31",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 3000,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-23",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1500,
      //           totalCostsMoney: 1200,
      //           alreadyRepayTotalSalesMoney: 1500,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-23",
      //               repayDateString: "2018-08-30",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-22",
      //           totalWithdrawNumber: 4,
      //           totalSalesMoney: 4500,
      //           totalCostsMoney: 3600,
      //           alreadyRepayTotalSalesMoney: 3060,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 60,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-22",
      //               repayDateString: "2018-08-29",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-08-22",
      //               repayDateString: "2018-08-31",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 1560,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 3,
      //               alreadyRepaySalesMoneyNow: 3060,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-21",
      //           totalWithdrawNumber: 21,
      //           totalSalesMoney: 30700,
      //           totalCostsMoney: 24560,
      //           alreadyRepayTotalSalesMoney: 6180,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 180,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-21",
      //               repayDateString: "2018-08-28",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-08-21",
      //               repayDateString: "2018-08-29",
      //               alreadyRepayNumber: 3,
      //               alreadyRepaySalesMoney: 4680,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 4,
      //               alreadyRepaySalesMoneyNow: 6180,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-20",
      //           totalWithdrawNumber: 3,
      //           totalSalesMoney: 4100,
      //           totalCostsMoney: 3280,
      //           alreadyRepayTotalSalesMoney: 0,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: []
      //         },
      //         {
      //           withdrawdateString: "2018-08-18",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1300,
      //           totalCostsMoney: 1040,
      //           alreadyRepayTotalSalesMoney: 0,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: []
      //         },
      //         {
      //           withdrawdateString: "2018-08-17",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 3000,
      //           totalCostsMoney: 2400,
      //           alreadyRepayTotalSalesMoney: 3800,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 800,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-17",
      //               repayDateString: "2018-08-24",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-08-17",
      //               repayDateString: "2018-09-27",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 2300,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 3800,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-16",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 3000,
      //           totalCostsMoney: 2400,
      //           alreadyRepayTotalSalesMoney: 1500,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-16",
      //               repayDateString: "2018-08-23",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-15",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1500,
      //           totalCostsMoney: 1200,
      //           alreadyRepayTotalSalesMoney: 1500,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-15",
      //               repayDateString: "2018-08-22",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-14",
      //           totalWithdrawNumber: 7,
      //           totalSalesMoney: 7700,
      //           totalCostsMoney: 6160,
      //           alreadyRepayTotalSalesMoney: 2920,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: -280,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-14",
      //               repayDateString: "2018-08-22",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1560,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1560,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-08-14",
      //               repayDateString: "2018-08-23",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 1360,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 3,
      //               alreadyRepaySalesMoneyNow: 2920,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-13",
      //           totalWithdrawNumber: 5,
      //           totalSalesMoney: 7500,
      //           totalCostsMoney: 6000,
      //           alreadyRepayTotalSalesMoney: 0,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: []
      //         },
      //         {
      //           withdrawdateString: "2018-08-12",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 3000,
      //           totalCostsMoney: 2400,
      //           alreadyRepayTotalSalesMoney: 1500,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 0,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-12",
      //               repayDateString: "2018-08-12",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-10",
      //           totalWithdrawNumber: 2,
      //           totalSalesMoney: 3000,
      //           totalCostsMoney: 2400,
      //           alreadyRepayTotalSalesMoney: 3030,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 30,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-10",
      //               repayDateString: "2018-08-17",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1500,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1500,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-08-10",
      //               repayDateString: "2018-08-18",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1530,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 3030,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-09",
      //           totalWithdrawNumber: 3,
      //           totalSalesMoney: 4500,
      //           totalCostsMoney: 3600,
      //           alreadyRepayTotalSalesMoney: 4860,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 360,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-09",
      //               repayDateString: "2018-08-17",
      //               alreadyRepayNumber: 2,
      //               alreadyRepaySalesMoney: 3060,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 2,
      //               alreadyRepaySalesMoneyNow: 3060,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             },
      //             {
      //               withdrawDateString: "2018-08-09",
      //               repayDateString: "2018-09-12",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1800,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 3,
      //               alreadyRepaySalesMoneyNow: 4860,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         },
      //         {
      //           withdrawdateString: "2018-08-07",
      //           totalWithdrawNumber: 1,
      //           totalSalesMoney: 1200,
      //           totalCostsMoney: 960,
      //           alreadyRepayTotalSalesMoney: 1224,
      //           needRepayTotalSalesMoney: null,
      //           alreadyRepayotalLatefree: 24,
      //           list: [
      //             {
      //               withdrawDateString: "2018-08-07",
      //               repayDateString: "2018-08-14",
      //               alreadyRepayNumber: 1,
      //               alreadyRepaySalesMoney: 1224,
      //               needRepayMoney: null,
      //               alreadyRepayLatefree: null,
      //               alreadyRepayTotalSalesMoney: null,
      //               needRepayTotalSalesMoney: null,
      //               alreadyRepayotalLatefree: null,
      //               alreadyRepayNumberNow: 1,
      //               alreadyRepaySalesMoneyNow: 1224,
      //               needRepayMoneyNow: null,
      //               alreadyRepayLatefreeNow: null
      //             }
      //           ]
      //         }
      //       ],
      //       type: 0,
      //       countVo: null
      //     }
      //   };
      //   this.npage = data.data.npage;
      //   this.total = data.data.allpage;
      //   this.tableData = data.data.list;
      //   var currentDate = this.tableData[0].list[0].repayDateString;
      //   var dateArr = [];
      //   if (this.date) {
      //     for (var num = 1; num <= 7; num++) {
      //       for (var num = 0; num < 7; num++) {
      //         dateArr.push(
      //           miment(currentDate)
      //             .add(num, "MM")
      //             .format("YYYY-MM")
      //         );
      //       }
      //     }
      //   } else {
      //     for (var num = 0; num < 7; num++) {
      //       dateArr.push(
      //         miment(currentDate)
      //           .add(num, "DD")
      //           .format("YYYY-MM-DD")
      //       );
      //     }
      //   }
      //   this.tableData.forEach(v => {
      //     dateArr.forEach(d => {
      //       var arr = v.list.find(a => a.repayDateString == d);
      //       v[d] = arr;
      //     });
      //   });
      //   this.dateArr = dateArr;
      // });
    },
    handleSearch() {
      if (this.date) {
        this.getData(
          this.npage,
          this.pagesize,
          2,
          this.preM ? this.preM + "-01" : null,
          this.afterM ? this.afterM + "-01" : null
        );
      } else {
        if (this.search.time && this.search.time.length) {
          this.getData(
            this.npage,
            this.pagesize,
            1,
            this.search.time[0] + " 00:00:00",
            timeFormat(this.search.time[1], 1) + " 00:00:00"
          );
        } else {
          this.getData(this.npage, this.pagesize, 1);
        }
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
      this.getData(this.npage, this.pagesize, this.date ? 2 : 1);
    }
  },
  mounted() {
    this.getData(this.npage, this.pagesize, this.date ? 2 : 1);
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
