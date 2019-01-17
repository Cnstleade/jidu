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
                    <!-- <el-button-group>
                      <el-button :type="execeedtimeType==0?'info':''" @click="changeExeceedtimeType(0)">重置</el-button>
                      <el-button :type="execeedtimeType==1?'primary':''" @click="changeExeceedtimeType(1)">M1</el-button>
                      <el-button :type="execeedtimeType==2?'success':''" @click="changeExeceedtimeType(2)">M2</el-button>
                      <el-button :type="execeedtimeType==3?'warning':''" @click="changeExeceedtimeType(3)">M3</el-button>
                      <el-button :type="execeedtimeType==4?'danger':''" @click="changeExeceedtimeType(4)">M3+</el-button>
                    </el-button-group>     -->
                    <el-button  type="primary" @click="reset">重置</el-button>
                    <div class="l20">
                        <el-input
                        style="padding:0px 10px 0px 0px"
                          placeholder="关键字"
                          v-model="search.input"
                          clearable>
                        </el-input> 
                    </div>  
                    <el-select class="l20" v-model="search.type" placeholder="请选择用户类型">
                      <el-option
                        v-for="item in types"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                      </el-option>
                    </el-select>                                                       
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
            ref="multipleTable" 
            tooltip-effect="dark"
                highlight-current-row style="width: 100%;font-weight:bold"
            class="m20"
            @selection-change="handleSelectionChange"
            v-loading="loading"
            id="text"
          >
           <el-table-column
             type="selection"
             width="55">   
          </el-table-column>                    
            <el-table-column prop="userId" fixed label="用户ID" align="center" width="80" sortable>
            </el-table-column>
            <el-table-column prop="phoneNumber" label="用户号码" align="center" width="140" sortable>
            </el-table-column>

            <el-table-column prop="userName" label="用户姓名" align="center" width="100" >
            </el-table-column>
            <el-table-column prop="idCard" label="身份证" align="center" width="110" sortable>
                                                       <template slot-scope="scope">
                    {{scope.row.idCard }}
                </template>
            </el-table-column>  
            <el-table-column prop="score" label="芝麻分" align="center" width="80" sortable>
            </el-table-column> 
                <el-table-column prop="custAuth"  align="center" label="认证步骤" width="100"  >
                  <template slot-scope="scope">
                      <el-tag
                          :type="scope.row.custAuth===1?'success':scope.row.custAuth===2?'success':'danger'"
                      >{{scope.row.custAuth===1?'活体认证':scope.row.custAuth===2?'运营商认证':scope.row.custAuth===3?'认证成功':'绑卡认证'}}</el-tag>
                  </template>                   
                </el-table-column>               
            <!-- <el-table-column prop="registerIp" label="注册IP" align="center" width="140" sortable>
            </el-table-column>   
           <el-table-column prop="sourceTypeStr" label="来源" align="center" width="100" >
            </el-table-column>               -->
            <el-table-column prop="typeStr" label="用户类型" align="center" width="140"
            :filters="[{ text: '有意向', value: '有意向' }, { text: '无意向', value: '无意向' }, { text: '弃用', value: '弃用' }]"
             :filter-method="filterTypeStr"
             >
            </el-table-column>  
             <el-table-column prop="userType" label="客户状态" align="center"  min-width="120">
              <template slot-scope="scope">
                  <el-tag
                      :type="scope.row.userType==1?'success':scope.row.userType==2?'info':scope.row.userType==3?'warning':scope.row.userType==4?'danger':''"
                  >{{scope.row.userType==1?'陌生':scope.row.userType==2?'未提交':scope.row.userType==3?'机审中':scope.row.userType==4?'审核分配中':scope.row.userType==5?'审核中':scope.row.userType==6?'未提现':scope.row.userType==7?'审核拒绝':scope.row.userType==8?'已经提现':scope.row.userType==9?'有额度':'审核驳回中'}}</el-tag>
              </template>  
            </el-table-column>                                                                                                
            <!-- <el-table-column prop="lastLoginTimeStr" label="最后登陆时间" align="center" width="140" sortable>
            </el-table-column>   -->
           <el-table-column prop="userCreateTimeStr" label="注册时间" align="center" width="140" sortable>
            </el-table-column>  
            <el-table-column prop="detailAddress" label="详细地址" align="center" min-width="180" >
                         <template slot-scope="scope">
                          <el-tooltip class="item" effect="dark" :content="scope.row.detailAddress" placement="top">
                              <span>{{scope.row.detailAddress}}</span>
                          </el-tooltip>
                    </template>               
            </el-table-column>   
            <el-table-column type="expand" label="回访详情" width="80" >
              <template slot-scope="props" >
                <el-alert
                  title="回访记录"
                  type="success"
                  :closable="false"
                  center
                  >
                </el-alert>
                <el-table
                  :data="props.row.chbdetail"
                  border 
                      highlight-current-row style="width: 100%;font-weight:bold"
                  >
                      <el-table-column prop="customName" fixed label="客户姓名" align="center" sortable width="100"></el-table-column>
                      <el-table-column prop="salesmanName" label="电销员姓名" align="center" sortable width="100"></el-table-column>
                      <el-table-column prop="recallType" label="回访类型" align="center" sortable width="100"></el-table-column>
                      <el-table-column prop="recallResult" label="回访内容" align="center" sortable ></el-table-column>
                      <el-table-column prop="updateTime" label="最近回访时间" align="center" sortable width="180">
                        <template slot-scope="scope">
                            {{scope.row.updateTime|dateServer}}
                        </template>                          
                      </el-table-column>
                      <el-table-column prop="createTime" label="创建时间" align="center" sortable width="180">
                        <template slot-scope="scope">
                            {{scope.row.createTime|dateServer}}
                        </template>                          
                      </el-table-column>                      
                      <el-table-column prop="type" label="客户状态" align="center" width="180" ></el-table-column>                      
                </el-table>
              </template>
            </el-table-column>                                                
                <el-table-column prop="cz"  align="center" label="操作"  width="460" >
                    <template slot-scope="scope">
                        <el-button
                            size="mini"
                            type="success"
                            @click="handleAdd(scope.$index, scope.row)"
                           >通讯录</el-button> 
                        <el-button
                            size="mini"
                            type="success"
                            @click="handleDetail(scope.$index, scope.row)"
                           >详情</el-button>                                                
                      <el-button
                            size="mini"
                            type="success"
                            @click="handleAdd2(scope.$index, scope.row)"
                           >添加回访</el-button>
                      <el-button
                            size="mini"
                            type="primary"
                            @click="handleSMS(scope.$index, scope.row)"
                           >添加短信</el-button>                           
                    </template> 
                </el-table-column>            
        </el-table>     
        <el-row class="m20" v-if="total>0">
            <el-button type="primary" style="float:left" @click="handelConfigAll" :disabled="multipleSelection.length==0">批量发送</el-button>          
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
          title="通讯录"
          :visible.sync="dialogVisible"
          center
          width="30%"
          > 
                        <el-table
                  :data="tableData2"
                  border 
                      highlight-current-row style="width: 100%;font-weight:bold"
                  >
                             <el-table-column
                      type="index"
                      align="center"
                      sortable
                      width="50"></el-table-column>
                      <el-table-column prop="name" fixed label="姓名" align="center"  width="100"></el-table-column>
                      <el-table-column prop="mobile" label="号码" align="center"  min-width="100"></el-table-column>
                  
                  </el-table>
        </el-dialog>  
        <el-dialog
          title="添加回访记录"
          :visible.sync="dialogVisible3"
          center
          width="40%"
          > 
             
                             <!-- <el-table-column
                      type="index"
                      align="center"
                      sortable
                      width="50"></el-table-column>
                      <el-table-column prop="name" label="姓名" align="center"  width="100"></el-table-column>
                      <el-table-column prop="mobilePhone" label="号码" align="center"  min-width="100"></el-table-column> -->
                  
                 
            <el-row>
              <el-col :span="20" >
            <el-form :model="ruleForm1" status-icon  ref="ruleForm1" label-width="100px" :rules="rules" >
              <el-form-item label="客户姓名:" prop="userName" >
                <el-input type="input" v-model="ruleForm1.userName" auto-complete="off" disabled></el-input>
              </el-form-item>
              <el-form-item label="客户电话:" prop="userMobile" >
                <el-input type="input" v-model="ruleForm1.userMobile" auto-complete="off" disabled></el-input>
              </el-form-item>
              <el-form-item label="客户ID:" prop="custUserId" >
                <el-input v-model.number="ruleForm1.custUserId" disabled></el-input>
              </el-form-item>  
              <el-form-item label="客户状态:" prop="type" >
                <el-input  disabled v-model="ruleForm1.typeStr"></el-input>
              </el-form-item>  
              <!-- <el-form-item label="所剩额度:" prop="money" >
                <el-input  disabled></el-input>
              </el-form-item>   -->
              <!-- <el-form-item label="电销人员:"  >
                <el-input  disabled>{{ruleForm1.salesman}}</el-input>
       </el-form-item>   -->
              <el-form-item label="回访类型:" prop="recallType">
                 <el-select v-model="ruleForm1.recallType" placeholder="回访类型">
                  <template v-for="(temp,index) in recallType">
                    <el-option  :key="index" :label="temp.label" :value="temp.value">
                    </el-option>
                  </template>  
                </el-select>                
              </el-form-item>              
              <el-form-item label="备注:" prop="remark">
                 <el-input type="textarea" v-model="ruleForm1.remark" ></el-input>
              </el-form-item>
              <el-form-item label="回访结果:" prop="recallResult">
                <el-input type="textarea" v-model="ruleForm1.recallResult"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="submitForm('ruleForm1')">提交</el-button>
                <el-button type="primary" style="margin-left:30px" @click="quxiao1">取消</el-button>
              </el-form-item>
            </el-form>
              </el-col>
            </el-row>
           
        </el-dialog>  
        <el-dialog
          title="发送短信"
          :visible.sync="dialogVisible2"
          center
          width="30%"
          >
              <el-row>
                <el-tag
                class="l20 m20"
                  v-for="tag in dynamicTags"
                  :key="tag.userId"
                  type="danger">
                  {{tag.phoneNumber}}
                </el-tag>
              </el-row>          
            <el-form class="m20" :model="smsForm" status-icon  ref="smsForm" label-width="100px" :rules="rules" >
              <el-form-item label="短信平台:" prop="platformName">
                 <el-select v-model="smsForm.platformName" placeholder="短信平台" @change="changePlatformName">
                  <template v-for="(temp,index) in eleMsgPlatform">
                    <el-option  :key="index" :label="temp.platformName" :value="temp.id">
                    </el-option>
                  </template>  
                </el-select>                
              </el-form-item> 
              <el-form-item label="发送签名:" prop="eleSignatures">
                 <el-select v-model="smsForm.eleSignatures" placeholder="短信签名">
                  <template v-for="(temp,index) in  eleSignatures">
                    <el-option  :key="index" :label="temp.signature" :value="temp.signature">
                    </el-option>
                  </template>  
                </el-select>                
              </el-form-item>   
              <el-form-item label="内容:" prop="content">
                <el-input type="textarea" v-model="smsForm.content"></el-input>
              </el-form-item>    
              <el-form-item>
                <el-button type="primary" @click="submitForm('smsForm')">提交</el-button>
                <el-button type="primary" style="margin-left:30px" @click="dialogVisible2=false">取消</el-button>
              </el-form-item>                                    
            </el-form>            
        </el-dialog> 
        <el-dialog
            :visible.sync="checkVisible"
              width="95%"
              title="客户信息表"
              center
            
         >

            <el-row style="overflow: hidden;">
                <table class="table" >
                    <tr>
                        <th valign="middle" colspan="6" class="bgcolor">
                            客户基本信息
                        </th>
                    </tr>
                    <tr class="title_6" >
                        <td colspan="6">
                            <el-table
                                    :data="userData"
                                    border
                                        highlight-current-row style="width: 100%;font-weight:bold"
                            >
                                <el-table-column prop="realName" fixed label="姓名" align="center" width="120"></el-table-column>
                                <el-table-column prop="status"  align="center" label="状态"   width="150"
                                  >
                                  <template slot-scope="scope">
                                                           <el-tag
                          :type="scope.row.status===1?'':'danger'"
                      >{{scope.row.status===1?'正常':'冻结'}}</el-tag>
                                  </template>   
                                </el-table-column> 
                                <el-table-column prop="idcard" label="身份证号" align="center" width="180"></el-table-column>
                                <el-table-column prop="phoneNumber" label="手机号" align="center" width="140"></el-table-column>
                                <el-table-column prop="bankcard" label="银行卡号" align="center" width="180" ></el-table-column>
                                <el-table-column prop="wxNumber" label="微信号" align="center" width="180" ></el-table-column>
                                <!-- <el-table-column prop="qqNumber" label="QQ" align="center" ></el-table-column> -->
                                <el-table-column prop="detailAddress" label="地址" align="center" width="500"></el-table-column>
                                <!-- <el-table-column prop="mariage"  align="center" label="婚姻状况"  width="100"
                                 >
                                  <template slot-scope="scope">
                                      <el-tag
                                          :type="scope.row.mariage===1?'':scope.row.mariage===2?'success':'danger'"
                                      >{{scope.row.mariage===1?'未婚':scope.row.mariage===2?'已婚':'离婚'}}</el-tag>
                                  </template>                     
                                </el-table-column>     -->
                                <!-- <el-table-column prop="eduBack"  align="center" label="教育背景"   width="100"
                                  >
                                  <template slot-scope="scope">
                                      <el-tag
                                          :type="scope.row.eduBack===1?'':scope.row.eduBack===2?'success':scope.row.eduBack===3?'info':scope.row.eduBack===4?'warning':scope.row.eduBack===5?'danger':''"
                                      >{{scope.row.eduBack===1?'高中':scope.row.eduBack===2?'大专':scope.row.eduBack===3?'本科':scope.row.eduBack===4?'硕士':scope.row.eduBack===5?'博士':'其它'}}</el-tag>
                                  </template>   
                                </el-table-column>    -->
                                <el-table-column prop="source"  align="center" label="来源"  width="80"
                                   >
                                  <template slot-scope="scope">
                                      <el-tag
                                          :type="scope.row.source===1?'':scope.row.source===2?'success':'danger'"
                                      >{{scope.row.source===1?'苹果':scope.row.source===2?'安卓':'网页'}}</el-tag>
                                  </template> 
                                </el-table-column>                                                                                         
                            </el-table>
                        </td>
                    </tr>
                     
                    <!-- <tr>
                        <th  class="bgcolor "  >联系人信息</th>
                        <td colspan="5" >
                            <el-table
                                    :data="rzData"
                                    border
                                        highlight-current-row style="width: 100%;font-weight:bold">
                                <el-table-column prop="relationship" align="center" label="关系"  width="100"></el-table-column>
                                <el-table-column prop="real_name" align="center" label="姓名"  width="100"></el-table-column>
                                <el-table-column prop="mob_number" align="center"  label="电话号码" width="120"></el-table-column>
                                <el-table-column prop="address"  align="center" label="地址" ></el-table-column>
                            </el-table>
                        </td>
                    </tr> -->
                    <!-- <tr>
                        <th  class="bgcolor">联系人信息</th>
                        <td colspan="5" >
                            <table class="table table_2">
                                &nbsp;
                            </table>
                        </td>
                    </tr> -->
                    <!-- <tr>
                        <th  class="bgcolor">授权验证</th>
                        <td colspan="5" >
                            <el-table
                                    :data="sqData"
                                    border
                                        highlight-current-row style="width: 100%;font-weight:bold">
                                <el-table-column prop="xxw"   label="学信网密码"   ></el-table-column>
                                <el-table-column prop="sjfw"  label="手机服务密码"></el-table-column>
                                <el-table-column prop="tbzh"  label="淘宝帐号" ></el-table-column>
                                <el-table-column prop="tbmm"  label="淘宝密码" ></el-table-column>
                                <el-table-column prop="jdzh"  label="京东帐号" ></el-table-column>
                                <el-table-column prop="jdmm"  label="京东密码" ></el-table-column>
                            </el-table>
                        </td>
                    </tr> -->
                    <tr>
                        <th  class="bgcolor">认证资料</th>
                        <td colspan="5" >
                            <el-row >
                                <el-col :span="3"   v-for="(o, index) in idCardImgs" :key="o.label" :offset="index > 0 ? 1 : 0">
                                    <template>
                                        <el-card :body-style="{ padding: '0px' }" shadow="hover">
                                            <img @click="handleImgShow(o.src)" :src="o.src" class="image">
                                            <div style="padding: 14px;text-align:center">
                                                <span>{{o.label}}</span>
                                            </div>
                                        </el-card>
                                    </template>
                                </el-col>
                            </el-row>
                        </td>
                    </tr>
                    <tr>
                        <th valign="middle" colspan="6" style="color: #436EEE;background-color: #f3f3f3;">
                            关联信息
                        </th>
                    </tr>
                    <tr>
                        <th  class="bgcolor">钱包信息</th>
                        <td colspan="5" >
                            <el-table
                                    :data="qbData"
                                    border
                                        highlight-current-row style="width: 100%;font-weight:bold">
                                <el-table-column prop="status" fixed   label="钱包状态"   align="center" ></el-table-column>
                                <el-table-column prop="createTime"  label="注册时间" align="center">
                                  <template slot-scope="scope" align="center">
                                      {{scope.row.createTime|dateServer}}
                                  </template>                                    
                                </el-table-column>
                                <el-table-column prop="applyAmt"  label="申请金额" align="center" ></el-table-column>
                                <el-table-column prop="approveAmt"  label="签约金额" align="center" ></el-table-column>    
                                <el-table-column prop="applyTime"  label="申请时间" align="center">
                                  <template slot-scope="scope" align="center">
                                      {{scope.row.applyTime|dateServer}}
                                  </template>                                    
                                </el-table-column>                                
                            </el-table>
                        </td>
                    </tr>
                   <tr>
                        <th  class="bgcolor "  >滴滴出行信息</th>
                        <td colspan="5" >
                            <template v-if='ddShow'>
                                <el-table
                                        :data="ddData.slice(0,5)"
                                        class="textcontent"
                                        border
                                            highlight-current-row style="width: 100%;font-weight:bold">
                                    <el-table-column prop="cityName" fixed align="center" label="城市"  width="100"></el-table-column>
                                    <el-table-column prop="productName" align="center" label="类别"  width="100"></el-table-column>
                                    <el-table-column prop="actualPayFee" align="center"  label="消费记录" width="120"></el-table-column>
                                  <el-table-column prop="fromAddress"  align="center" label="起始地" min-width="200" >
                                        <template slot-scope="scope">
                                              <el-tooltip class="item" effect="dark" :content="scope.row.fromAddress" placement="top">
                                                  <span>{{scope.row.fromAddress}}</span>
                                              </el-tooltip>
                                        </template>                                     
                                  </el-table-column>
                                    <el-table-column prop="toAddress"  align="center" label="目的地" min-width="200">
                                        <template slot-scope="scope">
                                              <el-tooltip class="item" effect="dark" :content="scope.row.toAddress" placement="top">
                                                  <span>{{scope.row.toAddress}}</span>
                                              </el-tooltip>
                                        </template>                                       
                                    </el-table-column>
                                    <el-table-column prop="beginTime"  align="center" label="时间" width="160"></el-table-column>
                                </el-table>                          
                            </template>
                            <template v-if='!ddShow'>
                                <el-table
                                        :data="ddData"
                                        border
                                            highlight-current-row style="width: 100%;font-weight:bold">
                                    <el-table-column prop="cityName" fixed align="center" label="城市"  width="100"></el-table-column>
                                    <el-table-column prop="productName" align="center" label="类别"  width="100"></el-table-column>
                                    <el-table-column prop="actualPayFee" align="center"  label="消费记录" width="120"></el-table-column>
                                  <el-table-column prop="fromAddress"  align="center" label="起始地" min-width="200" >
                                        <template slot-scope="scope">
                                              <el-tooltip class="item" effect="dark" :content="scope.row.fromAddress" placement="top">
                                                  <span>{{scope.row.fromAddress}}</span>
                                              </el-tooltip>
                                        </template>                                     
                                  </el-table-column>
                                    <el-table-column prop="toAddress"  align="center" label="目的地" min-width="200">
                                        <template slot-scope="scope">
                                              <el-tooltip class="item" effect="dark" :content="scope.row.toAddress" placement="top">
                                                  <span>{{scope.row.toAddress}}</span>
                                              </el-tooltip>
                                        </template>                                       
                                    </el-table-column>
                                    <el-table-column prop="beginTime"  align="center" label="时间" width="160"></el-table-column>
                                </el-table>                          
                            </template>
                            <el-button class="m20" @click="ddShow = !ddShow">{{ddShow?'展开滴滴信息':'收起滴滴信息'}}</el-button>
                        </td>
                    </tr>                    
                    <tr>
                        <th  class="bgcolor">银行卡信息</th>
                        <td colspan="5" >
                            <el-table
                                    :data="yhkData"
                                    border
                                        highlight-current-row style="width: 100%;font-weight:bold">
                                <el-table-column prop="cardNum"  fixed  label="卡号"   align="center" width="240" ></el-table-column>
                                <el-table-column prop="name"   label="姓名"   align="center" width="100"></el-table-column>
                                <el-table-column prop="bankName"   label="银行"   align="center" width="240"></el-table-column>
                                <el-table-column prop="idNum"   label="身份证"   align="center" width="240"></el-table-column>
                                <el-table-column prop="createTime"  label="时间" align="center" width="240">
                                  <template slot-scope="scope" align="center">
                                      {{scope.row.createTime|dateServer}}
                                  </template>                                    
                                </el-table-column>
                                <!-- <el-table-column prop="protocolNo"  label="宝付返还接口" align="center" min-width="300" ></el-table-column> -->
                              
                            </el-table>
                        </td>
                    </tr>                    
                    <tr>
                        <th  class="bgcolor">提现账单信息</th>
                        <td colspan="5" >
                            <el-table
                                    :data="fqzdData"
                                    border
                                        highlight-current-row style="width: 100%;font-weight:bold">
                                <el-table-column prop="id" fixed  align="center" label="ID"  width="50" ></el-table-column>
                                <el-table-column prop="borrowDay" align="center" label="借款期限" width="200"></el-table-column>
                                <el-table-column prop="borrowTime" align="center" width="180" label="借款时间">
                                  <template slot-scope="scope">
                                      {{scope.row.borrowTime|dateServer}}
                                  </template>                                    
                                </el-table-column>  
                                <el-table-column prop="returnTime" align="center" width="180" label="应还款日期">
                                  <template slot-scope="scope">
                                      {{scope.row.returnTime|dateServer2}}
                                  </template>                                    
                                </el-table-column>  
                                <el-table-column prop="returnMoney" align="center"  label="还款金额" ></el-table-column>
                                <el-table-column prop="raiseMoney" align="center"  label="提现金额" ></el-table-column>
                                <el-table-column prop="status" label="状态" align="center" 
                                >
                                  <template slot-scope="scope">
                                      <el-tag
                                          :type="scope.row.status===0?'':scope.row.status===1?'success':scope.row.status===2?'danger':scope.row.status===4?'success':scope.row.status===4?'info':scope.row.status===5?'':'warning'"
                                      >{{scope.row.status===0?'放款中':scope.row.status===1?'放款成功':scope.row.status===2?'逾期':scope.row.status===3?'还款成功':scope.row.status===4?'放款失败':scope.row.status===5?'还款中':'还款失败'}}</el-tag>
                                  </template>                         
                                </el-table-column>                            
                            </el-table>
                        </td>
                    </tr>
                    <tr>
                        <th  class="bgcolor">还款记录</th>
                        <td colspan="5" >
                            <el-table
                                    :data="hkjlData"
                                    border
                                        highlight-current-row style="width: 100%;font-weight:bold">
                                <el-table-column prop="withdrawInfoId" fixed align="center"  label="id"  width="50" ></el-table-column>
                                <el-table-column prop="type"  align="center" label="还款类型"   width="150"
                                  >
                                  <template slot-scope="scope">
                                      <el-tag
                                          :type="scope.row.type===1?'':'success'"
                                      >{{scope.row.type===1?'app还款':'线下还款'}}</el-tag>
                                  </template>   
                                </el-table-column>                                 
                                <el-table-column prop="mustPayBackAmt" align="center" label="应还款" width="200"></el-table-column>
                                              <el-table-column prop="actualPayBackAmt" align="center"  label="实际还款" >         
                                                                          <!-- <template slot-scope="scope">                         
                                                                                          {{((scope.row.actualPayBackAmt/100).toFixed(2))}} 
                                                                                                                            </template>   -->
                                                                                                                             </el-table-column>
                                <el-table-column prop="backDate"  align="center" width="180" label="还款时间">
                                  <template slot-scope="scope">
                                      {{scope.row.backDate|dateServer}}
                                  </template>                                    
                                </el-table-column>  
                                <el-table-column prop="createTime" align="center"  width="180" label="更新时间">
                                  <template slot-scope="scope">
                                      {{scope.row.createTime|dateServer}}
                                  </template>                                    
                                </el-table-column>                                                                  
                                <el-table-column prop="bankCardNumber"  align="center" width="180" label="还款银行卡卡号" ></el-table-column>
                                <el-table-column prop="custname"  align="center" label="用户名" ></el-table-column>
                                <el-table-column prop="success"  align="center" width="140" label="是否还款成功" >
                                  <template slot-scope="scope">
                                      <el-tag
                                          :type="scope.row.success?'success':'danger'"
                                      >{{scope.row.success?'成功':'失败'}}</el-tag>
                                  </template>                                     
                                </el-table-column>
                                <!-- <el-table-column prop="nomal"  align="center"  width="140"  label="是否是正常还款" >
                                  <template slot-scope="scope">
                                      <el-tag
                                          :type="scope.row.nomal?'success':'danger'"
                                      >{{scope.row.nomal?'是':'否'}}</el-tag>
                                  </template>                                     
                                </el-table-column> -->
                            </el-table>
                        </td>
                    </tr>
                    <tr>
                        <th  class="bgcolor">滞纳金记录</th>
                        <td colspan="5" >
                            <el-table
                                    :data="zljData"
                                    border
                                        highlight-current-row style="width: 100%;font-weight:bold">
                                <el-table-column prop="applyTime" fixed  align="center" label="申请时间"  width="180" >
                                  <template slot-scope="scope">
                                      {{scope.row.applyTime|dateServer}}
                                  </template>    
                                </el-table-column>
                                <el-table-column prop="revertTime" align="center" label="理应还款时间" width="180" >
                                  <template slot-scope="scope">
                                   {{scope.row.revertTime?(scope.row.revertTime|dateServer2):''}}
                                  </template> 
                                </el-table-column>
                                <el-table-column prop="repayStatus" align="center"  label="还款情况" >
                                  <template slot-scope="scope">
                                      <el-tag
                                          :type="scope.row.repayStatus==1?'success':'danger'"
                                      >{{scope.row.repayStatus==1?'已还':'未还'}}</el-tag>
                                  </template>   
                                </el-table-column>
                                <el-table-column prop="overdueDay"  align="center" label="逾期天数" ></el-table-column>
                                <el-table-column prop="lateFee"  label="滞纳金" ></el-table-column>
                                <el-table-column prop="updateTime" align="center"  label="最后更新时间" >
                                  <template slot-scope="scope">
                                      {{scope.row.updateTime|dateServer}}
                                  </template> 
                                </el-table-column>
                                <el-table-column prop="withdraw" align="center"  label="提现金额" ></el-table-column>
                            </el-table>
                        </td>
                    </tr>
                    <tr>
                        <th valign="middle" colspan="6" style="color: #436EEE;background-color: #f3f3f3;">
                            客户回访
                        </th>
                    </tr>
                    <tr>
                        <th  class="bgcolor" style="color: #436EEE">回访记录</th>
                        <td colspan="5" >
                            <el-table
                                    :data="hfjlData"
                                    border
                                        highlight-current-row style="width: 100%;font-weight:bold">
                                <el-table-column prop="id" fixed  align="center" label="序号" width="100"></el-table-column>
                                <el-table-column prop="userName" align="center" label="用户名" width="100" ></el-table-column>
                                <el-table-column prop="userMobile" align="center" label="手机号" width="120" ></el-table-column>
                                <el-table-column prop="salesmanName"  align="center" label=催收员 width="100"></el-table-column>
                                <el-table-column prop="createTime"  align="center" label="创建时间" width="180" >
                        <template slot-scope="scope">
                            {{scope.row.createTime|dateServer}}
                        </template>                                      
                                </el-table-column>
                                <el-table-column prop="updateTime" align="center" label="更新时间" width="180" >
                        <template slot-scope="scope">
                            {{scope.row.updateTime|dateServer}}
                        </template>                                      
                                </el-table-column>
                                <el-table-column prop="recallResult" align="center" label="回访结果"  ></el-table-column>
                                <el-table-column prop="remark" align="center" label="备注" ></el-table-column>
                            </el-table>
                        </td>
                    </tr>
                    <!-- <tr>
                        <th  class="bgcolor">通话记录</th>
                        <td colspan="5" >
                            <table class="table table_2">
                                &nbsp;
                            </table>
                        </td>
                    </tr>
                    <tr>
                        <th  class="bgcolor">信用变更记录</th>
                        <td colspan="5" >
                            <table class="table table_2">
                                &nbsp;
                            </table>
                        </td>
                    </tr>
                    <tr>
                        <th  class="bgcolor">客户信用调整</th>
                        <td colspan="5" >
                            <el-row >
                                <el-col :span="3">
                                  当前信用度：<strong style="color:red">{{score.score}}</strong>
                                </el-col>                                
                                <el-col :span="21">
                                   <el-form :inline="true" :model="score" class="demo-form-inline">

                                    <el-form-item >
                                      <el-select v-model="score.method" placeholder="加">
                                            <el-option
                                                    v-for="item in score.methods"
                                                    :key="item.value"
                                                    :label="item.label"
                                                    :value="item.value">
                                            </el-option>
                                      </el-select>
                                    </el-form-item>
                                    <el-form-item >
                                      <el-select v-model="score.scores" placeholder="1分">
                                            <el-option
                                                    v-for="item in score.scoress"
                                                    :key="item.value"
                                                    :label="item.label"
                                                    :value="item.value">
                                            </el-option>
                                      </el-select>
                                    </el-form-item> 
                                    <el-form-item label="调整原因">
                                      <el-input v-model="score.message" placeholder="请输入调整原因"></el-input>
                                    </el-form-item>                                                                       
                                    <el-form-item>
                                      <el-button type="primary" @click="onSubmit">确定</el-button>
                                    </el-form-item>
                                   </el-form>
                                </el-col>
                            </el-row>
                        </td>
                    </tr> -->
                </table>
            </el-row> 
            <el-dialog
              id="dialog"
              width="80%"
              top="100px"
              title="照片信息"
              :visible.sync="innerImgVisible"
              append-to-body>
                    <!-- <template v-for="(o,index) in idCardImgs">
                      <el-card v-if="index==  Math.abs(imgIndex)"  :key="o.label" :body-style="{ padding: '0px' }"  shadow="never">
                          <img @click="handleImgShow" :src="o.src" class="imgCenter">
               
                      </el-card>
                    </template> -->
                    <template>
                      <el-card  :body-style="{ padding: '0px' }"  shadow="never">
                          <img  :src="imgStr" class="imgCenter">
                          <!-- <div style="padding: 14px;text-align:center">
                            <span>{{o.label}}</span>
                          </div>                                     -->
                      </el-card>
                    </template>
                    <!-- <button type="button" @click="changImgIndex(-1)" class="el-carousel__arrow el-carousel__arrow--left"><i class="el-icon-arrow-left"></i></button>   
                    <button type="button"   @click="changImgIndex(1)" class="el-carousel__arrow el-carousel__arrow--right"><i class="el-icon-arrow-right"></i></button>                        -->
            </el-dialog>   
        </el-dialog>                                                            
    </div>
</template>

<script>
import { mapGetters } from "vuex";
import { timeFormat } from "../../../config/time";
import {
  httpPreSalest,
  httpMobileList,
  getEleSalesmanRecall,
  getReplies,
  httpEleSignatures,
  httpEleMsgPlatform,
  httpSendMsg,
  getCustomerPage
} from "../../../service/http";
export default {
  data() {
    return {
      title: "售前营销",
      dialogVisible: false,
      dialogVisible2: false,
      dialogVisible3: false,
      tableData2: [],
      dynamicTags: [],
      search: {
        input: "",
        time: ""
      },
      ruleForm1: {
        userName: "",
        userMobile: "",
        salesmanId: "",
        loanApplyId: "",
        loanOrderId: "",
        recallType: "",
        remark: "",
        recallResult: "",
        type: "",
        money: "",
        custUserId: ""
      },
      tableData: [],
      loading: true,
      npage: 1,
      pagesize: 10,
      total: 0,
      multipleSelection: [], //全部选中嘛
      types: [
        // { label: "陌生客户", value: 1 },
        // { label: "有意向", value: 2 },
        // { label: "未提现", value: 3 },
        // { label: "已提现", value: 4 },
        // { label: "有余额", value: 5 },
        // { label: "提现失败", value: 6 },
        { value: 1, label: "陌生 " },
        { value: 2, label: "未提交" },
        { value: 3, label: "机审中" },
        { value: 4, label: "审核分配中" },
        { value: 5, label: "审核中" },
        // { value: 6, label: "未提现" },
        // { value: 7, label: "审核拒绝" },
        // { value: 8, label: "已经提现" },
        // { value: 9, label: "有额度" },
        { value: 10, label: "审核驳回中" }
      ],
      rules: {
        remark: [{ required: true, message: "请填写备注", trigger: "blur" }],
        recallResult: [
          { required: true, message: "请填写回访结果", trigger: "blur" }
        ],
        content: [
          { required: true, message: "请填写短信内容", trigger: "blur" }
        ],
        salesmanId: [
          { required: true, message: "请选择电销人员", trigger: "change" }
        ],
        recallType: [
          { required: true, message: "请选择类型", trigger: "change" }
        ],
        platformName: [
          { required: true, message: "请选择平台", trigger: "change" }
        ],
        eleSignatures: [
          { required: true, message: "请选择签名", trigger: "change" }
        ]
      },
      recallType: [
        { value: 1, label: "有意向" },
        { value: 2, label: "无意向" },
        { value: 3, label: "弃用" }
      ],
      eleMsgPlatform: [],
      smsForm: {},
      eleSignatures: [],
      platformId: null,
      checkVisible: false,
      innerImgVisible: false,
      imgIndex: 0,
      total: 0,
      npage: 1,
      pagesize: 10,
      userData: [],
      rzData: [],
      sqData: [
        {
          xxw: "-",
          sjfw: "-",
          tbzh: "-",
          tbmm: "-",
          jdzh: "-",
          jdmm: "-"
        }
      ],
      ddData: [],
      idCardImgs: [],
      qbData: [],
      fqzdData: [],
      yhkData: [],
      hkjlData: [
        {
          xh: "253",
          id: "254",
          hkje: 500.0,
          qs: 14,
          hklx: "分月",
          zlj: "40.00",
          hksj: "2017-03-28 15:06:49",
          cz: "查看"
        }
      ],
      zljData: [],
      hfjlData: [],
      ddShow: true,
      imgStr: ""
    };
  },
  computed: {
    // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      "loginId"
      // ...
    ])
  },

  methods: {
    changePlatformName(v) {
      this._httpEleSignatures(v);
    },
    _httpSendMsg(mobiles, content, signature, msgPlatformId) {
      httpSendMsg(mobiles, content, signature, msgPlatformId)
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.$message({
              message: "添加成功",
              type: "success"
            });
            this.dialogVisible2 = false;
          } else {
            this.$message.error(data.msg);
          }
        })
        .catch(err => {
          this.$message.error("请检查网络连接或联系管理员");
        });
    },

    //查询短信签名
    _httpEleSignatures(platformId) {
      httpEleSignatures(platformId)
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.eleSignatures = data.data;
          } else {
            this.$message.error(data.msg);
          }
        })
        .catch(err => {
          let data = {
            code: 200,
            msg: "提交成功",
            data: [
              {
                id: 1,
                signature: "【极度钱包】",
                createTime: null,
                type: null,
                platformId: 1
              },
              {
                id: 2,
                signature: "【极度钱包】",
                createTime: null,
                type: null,
                platformId: 2
              }
            ]
          };
          if (data.code == 200) {
            this.eleSignatures = data.data;
            // this.smsForm.eleSignatures = null;
          } else {
            this.$message.error(data.msg);
          }
          this.$message.error("请检查网络连接或联系管理员");
        });
    },
    //查询短信平台
    _httpEleMsgPlatform() {
      httpEleMsgPlatform()
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.eleMsgPlatform = data.data;
          } else {
            this.$message.error(data.msg);
          }
        })
        .catch(err => {
          let data = {
            code: 200,
            msg: "提交成功",
            data: [
              { id: 1, platformName: "华信" },
              { id: 2, platformName: "创蓝" }
            ]
          };
          if (data.code == 200) {
            this.eleMsgPlatform = data.data;
          } else {
            this.$message.error(data.msg);
          }
          this.$message.error("请检查网络连接或联系管理员");
        });
    },
    _getEleSalesmanRecall(
      userName,
      userMobile,
      salesmanId,
      loanApplyId,
      loanOrderId,
      recallType,
      remark,
      recallResult,
      type,
      money,
      userId
    ) {
      let _this = this;
      getEleSalesmanRecall(
        userName,
        userMobile,
        salesmanId,
        loanApplyId,
        loanOrderId,
        recallType,
        remark,
        recallResult,
        type,
        money,
        userId
      )
        .then(res => {
          let data = res.data;
          if (data.code === 200) {
            this.$message({
              message: "添加成功",
              type: "success"
            });
            _this.dialogVisible3 = false;
          }
        })
        .catch();
    },
    _httpMobileList(id) {
      httpMobileList(id)
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            this.tableData2 = data.data;
            this.dialogVisible = true;
          } else {
            this.$message.error(data.msg);
          }
        })
        .catch(err => {
          this.$message.error("请检查网络连接或联系管理员");
        });
    },
    _httpPreSalest(
      salesmanId,
      pageNumber,
      pageSize,
      keywords,
      startDate,
      endDate,
      type
    ) {
      this.loading = true;
      httpPreSalest(
        salesmanId,
        pageNumber,
        pageSize,
        keywords,
        startDate,
        endDate,
        type
      )
        .then(res => {
          let data = res.data;
          if (data.code == 200) {
            let tableData = data.data.list;
            if (tableData.length == 0) {
              this.loading = false;
              this.total = data.data.allsize;
              this.tableData = tableData;
            } else {
              for (let a = 0; a < tableData.length; a++) {
                getReplies(tableData[a].userId)
                  .then(re => {
                    tableData[a].chbdetail = re.data.data;
                    // getSaleman(tableData[a].custUserId)
                    //   .then(re => {
                    //     tableData[a].chbSale = re.data.data;
                    //     _this.tableData = tableData;
                    //     _this.total = data.data.allpage;
                    //     _this.loading = false;
                    //   })
                    //   .catch();
                    tableData[a].chbSale = re.data.data;
                    this.total = data.data.allsize;
                    this.tableData = tableData;
                    this.loading = false;
                  })
                  .catch();
              }
            }

            // this.total = 1;
            // this.tableData = [
            //   {
            //     userId: 30,
            //     phoneNumber: "113067199670",
            //     detailAddress: "安徽省六安市金安区东河口镇月牙塘村吴大庄组",
            //     source: 2,
            //     userName: "王尚彬",
            //     idCard: "34240119921104859X",
            //     companyId: 1,
            //     companyName: null,
            //     score: 756,
            //     type: 5,
            //     registerIp: "121.225.246.159",
            //     typeStr: "有余额",
            //     lastLoginTimeStr: "2018-07-30 18:25:40",
            //     userCreateTimeStr: "2018-07-30 17:24:52",
            //     sourceTypeStr: "android"
            //   }
            // ];
          } else {
            this.$message.error(data.msg);
            this.loading = false;
          }
        })
        .catch(err => {
          this.$message.error("网络错误请联系管理员");
        });
    },
    filterTypeStr(value, row) {
      return row.typeStr === value;
    },
    getData() {
      let _this = this;
      url()
        .then(res => {
          let data = res.data;
        })
        .catch(err => {
          _this.tableData = [];
          _this.loading = false;
        });
    },
    handleSearch(type = true) {
      if (type) {
        this.npage = 1;
        this.pagesize = 10;
      }
      if (this.search.time && this.search.time.length) {
        this._httpPreSalest(
          this.loginId,
          this.npage,
          this.pagesize,
          this.search.input,
          this.search.time[0] + " 00:00:00",
          timeFormat(this.search.time[1], 1) + "00:00:00",
          this.search.type
        );
      } else {
        this._httpPreSalest(
          this.loginId,
          this.npage,
          this.pagesize,
          this.search.input,
          "",
          "",
          this.search.type
        );
      }
    },
    handleAllocation(index, row) {},
    handleCurrentChange(val) {
      this.npage = val;
      this.handleSearch(false);
    },
    handleSizeChange(val) {
      this.pagesize = val;
      this.handleSearch(false);
    },
    reset() {
      this.npage = 1;
      this.pagesize = 10;
      this.search = {};
      this._httpPreSalest(this.loginId, this.npage, this.pagesize);
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    //点击全选
    handelConfigAll() {
      this.dynamicTags.length = 0;
      for (var temp of this.multipleSelection) {
        this.dynamicTags.push({
          phoneNumber: temp.phoneNumber,
          useId: temp.userId
        });
      }
      this.dialogVisible2 = true;
      this.smsForm = {};
    },
    //
    handleSMS(index, row) {
      this.dynamicTags.length = 0;
      this.dialogVisible2 = true;
      this.smsForm = {};
      this.dynamicTags.push({
        phoneNumber: row.phoneNumber,
        useId: row.userId
      });
    },
    handleAdd(index, row) {
      let companyId = row.userId;
      this._httpMobileList(companyId);
    },
    handleAdd2(index, row) {
      this.dialogVisible3 = true;
      let companyId = row.companyId;
      this.ruleForm1 = {
        userName: row.userName,
        userMobile: row.phoneNumber,
        // salesmanId: row.salesmanId,
        // salesman: row.salesmanName,
        loanApplyId: row.loanApplyId,
        loanOrderId: row.loanOrderId,
        recallType: "",
        remark: "",
        recallResult: "",
        type: row.type,
        typeStr: row.typeStr,
        money: row.money,
        custUserId: row.userId
      };
      //    this._getEleCompanyId(row.companyId);
      //   this._httpMobileList(row.companyId);
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },

    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          if (formName == "ruleForm1") {
            this._getEleSalesmanRecall(
              this.ruleForm1.userName,
              this.ruleForm1.userMobile,
              this.loginId,
              this.ruleForm1.loanApplyId,
              this.ruleForm1.loanOrderId,
              this.ruleForm1.recallType,
              this.ruleForm1.remark,
              this.ruleForm1.recallResult,
              this.ruleForm1.type,
              this.ruleForm1.money,
              this.ruleForm1.custUserId
            );
            this.dialogVisible3 = false;
            setTimeout(() => {
              console.log('++++++++')
              this._httpPreSalest(this.loginId, this.npage, this.pagesize);
            }, 2000);
            this.resetForm(formName);
          } else if (formName == "smsForm") {
            let mobile = [];
            this.dynamicTags.forEach(v => {
              mobile.push(v.phoneNumber);
            });
            this._httpSendMsg(
              mobile,
              this.smsForm.content,
              this.smsForm.eleSignatures,
              this.smsForm.platformName
            );
          }
        } else {
          return false;
        }
      });
    },
    quxiao1() {
      this.dialogVisible = false;
      this.resetForm("ruleForm1");
    },
    handleDetail(index, row) {
      console.log(row);
      let _this = this;
      let CustUserId = row.userId;
      _this.userData ? (_this.userData.length = 0) : (_this.userData = []);
      _this.rzData ? (_this.rzData.length = 0) : (_this.rzData = []);
      _this.qbData ? (_this.qbData.length = 0) : (_this.qbData = []);
      _this.fqzdData ? (_this.fqzdData.length = 0) : (_this.fqzdData = []);
      _this.ddData ? (_this.ddData.length = 0) : (_this.ddData = []);
      _this.yhkData ? (_this.yhkData.length = 0) : (_this.yhkData = []);
      _this.hkjlData ? (_this.hkjlData.length = 0) : (_this.hkjlData = []);
      _this.zljData ? (_this.zljData.length = 0) : (_this.zljData = []);
      _this.hfjlData ? (_this.hfjlData.length = 0) : (_this.hfjlData = []);
      _this.idCardImgs
        ? (_this.idCardImgs.length = 0)
        : (_this.idCardImgs = []);
      getCustomerPage(CustUserId)
        .then(res => {
          let data = res.data;
          _this.userData.push(row);
          _this.rzData = data.contactUserDOList;
          _this.idCardImgs = [
            {
              src:
                data.custIdcardImageList && data.custIdcardImageList[0]
                  ? data.custIdcardImageList[0].fullfaceUrl
                  : "",
              label: "身份证正面"
            },
            {
              src:
                data.custIdcardImageList && data.custIdcardImageList[0]
                  ? data.custIdcardImageList[0].reverseStorageUrl
                  : "",
              label: "身份证反面"
            },
            {
              src:
                data.custIdcardImageList && data.custIdcardImageList[0]
                  ? data.custIdcardImageList[0].handStorageUrl
                  : "",
              label: "活体认证照片"
            }
            // {
            //   src:
            //     data.custIdcardImageList && data.custIdcardImageList[0]
            //       ? data.custIdcardImageList[0].handStorageUrl
            //       : "",
            //   label: "手持照"
            // }
          ];
          _this.qbData.push(data.walletMessage);
          _this.fqzdData = data.withDrawDOList;
          _this.hkjlData = data.loanRepaymentList;
          _this.ddData = data.didiOrderVoList;
          _this.yhkData = data.bankCardDTOVoList;
          _this.zljData = data.overdueList;

          _this.hfjlData = data.salesmanRecallList;
          _this.checkVisible = true;
        })
        .catch();
    }
  },
  mounted() {
    this._httpPreSalest(this.loginId, this.npage, this.pagesize);
    this._httpEleMsgPlatform();
    this._httpEleSignatures(1);
  }
};
</script>

<style>
#text .cell {
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}
</style>
<style >
#dialog .el-dialog__header {
  display: none;
}
#dialog .el-dialog {
  padding: 0;
  background: rgba(0, 0, 0, 0);
}
#dialog .el-dialog__body {
  padding: 0;
  background: rgba(0, 0, 0, 0.1);
}
#dialog .el-card__body {
  background: rgba(0, 0, 0, 0.1);
}
#dialog .el-card {
  background: rgba(0, 0, 0, 0.1);
  border: none;
}
#text .cell {
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}
</style>

<style>
#tb1 .el-table thead {
  display: none;
}
</style>

<style scoped>
.col-flex {
  display: flex;
  justify-content: flex-start;
}
.table {
  border: double 3px #555;
  margin: 5px auto;
  border-collapse: collapse;
  border-spacing: 0;
  width: 100%;
  table-layout: fixed;
  border-collapse: separate;
}
.table tr th,
.table tr td {
  padding: 8px;
  line-height: 1.42857143;
  vertical-align: top;
  border-top: 1px solid #ddd;
}

.title_6 {
  padding: 10px;
}
.table_2 {
  border: double 0px #d3d3d3;
  margin: 5px auto;
  line-height: 30px;
}
.table tbale {
  background-color: #fff;
}
.image {
  width: 100%;
  height: 180px;
  display: block;
  cursor: pointer;
}
.imgCenter {
  clear: both;
  display: block;
  margin: auto;
}
.bgcolor {
  color: #436eee;
  background-color: #f3f3f3;
}
.w100 {
  width: 20%;
}
</style>