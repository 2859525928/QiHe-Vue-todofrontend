<template>
  <div class="HelloWorld">
    <el-tabs :tab-position="tabPosition" style="height: 600px;">
  <el-tab-pane>
    <span slot="label"><i class="el-icon-date"></i> 代办任务</span>
    <el-col :span="12">
       <el-input v-model="entry_words" placeholder="请输入您想要查询的任务名称" ></el-input>
       </el-col>
      <el-button type="primary" round @click="showTaskDialog">添加任务</el-button>
      <el-button type="success" round @click="compositorTaskLevel">任务等级排序</el-button>
      <el-button type="info" round @click="degreeOfCompletion">任务完成度排序</el-button>
    
    <div class="commissionTask">
       <el-table
      :data="seek(entry_words)"
      style="width: 100%"
       :row-class-name="tableRowClassName">

    <el-table-column
        label="代办任务序号"
        width="180">
      <template slot-scope="scope">
      <span>{{ scope.$index + 1 }}</span>
      </template>        
      </el-table-column>

       <el-table-column
        label="代办任务名称"
        width="180">
       <template slot-scope="scope">
          <i class="el-icon-star-off"></i>
        <span>{{scope.row.missionName}}</span>
        </template> 
      </el-table-column>

      <el-table-column
        label="代办任务类型"
        width="180">
       <template slot-scope="scope">
        <span>{{scope.row.type | MissionType}}</span>
        </template>  
      </el-table-column> 
      
      <el-table-column
        label="代办任务等级"
        width="180">
      <template slot-scope="scope">
       <span>{{scope.row.missionLevel | MissionLevel }}</span>
      </template>   
      </el-table-column>


      <el-table-column
        label="起始时间"
        width="250">
       <template slot-scope="scope">
        <span>{{scope.row.beginDate | dateFormat}}</span>
        <span>{{scope.row.beginTime | TimeFormat }}</span>
        </template> 
      </el-table-column>

      <el-table-column
        label="预计结束时间"
        width="250">
       <template slot-scope="scope">
                                
        <span>{{scope.row.overDate | dateFormat}} </span>
                                  
        <span>{{scope.row.overTime | TimeFormat}} </span>
        </template> 
      </el-table-column>

       <el-table-column
        prop="caozuo"
        label="操作">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" circle @click="editDialog(scope.$index,scope.row)"></el-button>
          <el-button type="success" icon="el-icon-check" circle @click="accomplishedMission(scope.$index,scope.row)"></el-button>
          <el-button type="danger" icon="el-icon-delete" circle @click="deleteMission(scope.$index,scope.row)"></el-button>
        </template>
      </el-table-column>      
    </el-table> 
    </div> 
  </el-tab-pane>

  <el-tab-pane>
     <span slot="label"><i class="el-icon-success"></i> 完成任务</span>
     <el-col :span="12">
           <el-input v-model="entry_words" placeholder="请您输入您想要查询的已完成任务"></el-input>
     </el-col>
    <el-table :data="seekFinishMission(entry_words)" style="width: 100%"
              :row-class-name="tableRowClassName">
                     <el-table-column label="序号" width="180">
                         <template slot-scope="scope">
                             <span>{{ scope.$index + 1 }}</span>
                         </template>
                     </el-table-column>

                      <el-table-column label="任务名称" width="180">
                         <template slot-scope="scope">
                           <i class="el-icon-star-on"></i>
                             <span>{{scope.row.missionName}}</span>
                         </template>
                     </el-table-column>

                      <el-table-column label="任务起始时间" width="180">
                         <template slot-scope="scope">
                             <span>{{scope.row.beginDate | dateFormat}}</span>
                             <span>{{scope.row.beginTime | TimeFormat}}</span>
                         </template>
                     </el-table-column>

                     <el-table-column label="任务截止时间">
                         <template slot-scope="scope">
                             <span>{{scope.row.overDate | dateFormat}}</span>
                             <span>{{scope.row.overTime | TimeFormat}}</span>
                         </template>
                     </el-table-column>

                     <el-table-column label="实际完成时间">
                         <template slot-scope="scope">
                             <span>{{scope.row.finished_time | dateFormat}} {{scope.row.finished_time | TimeFormat}}</span>
                         </template>
                     </el-table-column>

                     <el-table-column label="任务类型">
                        <template slot-scope="scope">
                            <span>{{scope.row.type | MissionType }}</span>
                        </template>
                    </el-table-column>

                     <el-table-column prop="options" label="操作">
                         <template slot-scope="scope">
                           <el-button type="danger" icon="el-icon-delete" circle @click="deletedFromdoneMission(scope.$index,scope.row)"></el-button>
                         </template>
                     </el-table-column>
                 </el-table>
  </el-tab-pane>
</el-tabs>


  <el-dialog
  title="编辑"
  :visible.sync="dialogFormVisible"
  width="50%"
  :before-close="handleClose">
    
     <el-form ref="form" :model="additionMission" label-width="80px">

     <el-form-item label="任务名称">
     <el-input v-model="additionMission.missionName"></el-input>
     </el-form-item>

    <el-form-item label="任务程度">
    <el-select v-model="additionMission.missionLevel" placeholder="请选择任务程度">
      <el-option label="一般" value="0"></el-option>
      <el-option label="重要" value="1"></el-option>
      <el-option label="紧急" value="2"></el-option>
    </el-select>
  </el-form-item>

   <el-form-item label="任务起始时间">
    <el-col :span="11">
      <el-date-picker type="date" placeholder="请您选择起始日期"
       v-model="additionMission.beginDate" style="width: 100%;">
       </el-date-picker>
    </el-col>
    <el-col class="line" :span="2">-</el-col>
    <el-col :span="11">
      <el-time-picker  placeholder="请您选择起始时间" type="time"
       v-model="additionMission.beginTime" style="width: 100%;"></el-time-picker>
    </el-col>
  </el-form-item>

    <el-form-item label="任务完成时间">
    <el-col :span="11">
      <el-date-picker type="date" placeholder="选择日期" v-model="additionMission.overDate" style="width: 100%;"></el-date-picker>
    </el-col>
    <el-col class="line" :span="2">-</el-col>
    <el-col :span="11">
      <el-time-picker placeholder="任务的完成时间" type="time"  v-model="additionMission.overTime" style="width: 100%;"></el-time-picker>
    </el-col>
  </el-form-item>

    <el-form-item label="是否提醒">
      <!-- 默认不提醒 -->
    <el-switch v-model="additionMission.isRemind"></el-switch>
  </el-form-item>
  
    <el-form-item label="任务类型">
    <el-select v-model="additionMission.type" placeholder="请选择任务类型">
      <el-option label="日常" value="3"></el-option>
      <el-option label="学习" value="4"></el-option>
      <el-option label="其他" value="5"></el-option>
    </el-select>
  </el-form-item>

   <el-form-item label="完成度">
  <div class="FinishDegree">
    <el-slider
      v-model="additionMission.sliderDefault"
      show-input>
    </el-slider>
  </div>
  </el-form-item>
    
      <el-form-item label="任务内容">
    <el-input type="textarea" v-model="additionMission.MissionContent"></el-input>
  </el-form-item>
  <el-form-item>
    <el-button type="primary" @click="addMission">确定</el-button>
  </el-form-item>
</el-form>
</el-dialog>

  </div>
</template>




<script>
export default {
  data(){
    return{
       tabPosition: 'left',
       activeName: 'first',
       isAdd : false,
       entry_words: '',
        dialogFormVisible: false,
        editIndex: 0,
        additionMission: {
          missionName: '',
          missionLevel: 0,
          beginDate: '',
          beginTime: '',
          overDate: '',
          overTime: '',
          isRemind: false,
          type: 3,
          sliderDefault : 0,
          MissionContent: '',
        },
                 unDoneMission: [{
                         missionName: "观看演唱会",
                         MissionContent: "地点：北京",
                         missionLevel: 0,
                         beginDate : "2019-12-13",
                         beginTime : "2019-12-13 18:50:14",
                         overDate : "2019-12-20",
                         overTime : "2019-12-20 00:00:00",
                         type : 3,
                         isRemind : false,
                         sliderDefault: 20

                     },
                     {
                         missionName: "马拉松比赛",
                         MissionContent: "注意开始时间",
                         beginDate : "2019-12-22",
                         beginTime : "2019-12-28 10:00:08",
                         overDate : "2019-12-30",
                         overTime : "2019-12-30 00:00:00",
                         missionLevel: 1,
                         isRemind : true,
                         type :4,
                         sliderDefault: 60

                     },
                     {
                         missionName: "编写程序",
                         MissionContent: "记得下载vue！",
                         beginDate : "2019-11-20",
                         beginTime : "2019-11-20 08:00:00",
                         overDate : "2019-11-30",
                         overTime : "2019-11-30 18:00:00",
                         missionLevel: 1,
                         isRemind : true,
                         type :4,
                         sliderDefault: 0
                     }
                 ],
                 doneMission: [{
                     missionName: "计算机网络实验",
                     MissionContent: "写计算机网络报告",
                     beginDate: '2019-12-18',
                     beginTime : '2019-12-18 12:10:25',                   
                     overDate: '2019-12-29',
                     overTime: '2019-12-29 20:00:00',
                     deleted_time: 'null',
                     finished_time: new Date(),
                     missionLevel: 2,
                     type: 4,
                     sliderDefault: 100
                 },
                 {
                     missionName: "观看比赛",
                     MissionContent: "加油",
                     beginDate: '2019-12-30',
                     beginTime : '2019-12-30 00:00:00',                   
                     overDate: '2019-12-31',
                     overTime: '2019-12-31 00:00:00',
                     deleted_time: 'null',
                     finished_time: new Date(),
                     missionLevel: 2,
                     type: 4,
                     sliderDefault: 100
                 }
                 
                 
                 ],
    }
  },
    methods: {
      tableRowClassName({row, rowIndex}) {
        if (rowIndex === 0) {
          return 'warning-row';
        } 
        if (rowIndex === 1) {
          return 'success-row';
        }
        if (rowIndex === 2) {
          return 'general-row';
        return '';
        }
      },
       handleClick(tab, event) {
        console.log(tab, event);
      },
       handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
      },
             showTaskDialog() {
                 this.additionMission.missionName = ""
                 this.additionMission.MissionContent = ""
                 this.additionMission.beginDate = null
                 this.additionMission.beginTime = null
                 this.additionMission.overDate = null
                 this.additionMission.overTime = null
                 this.additionMission.missionLevel = 0
                 this.additionMission.sliderDefault = 0
                  this.additionMission.type = 3
                this.additionMission.isRemind = false
                 this.dialogFormVisible = true
                 this.isAdd = true
             },
             addMission() {
                 if (this.isAdd) {
                     var resmble = JSON.parse(JSON.stringify(this.additionMission))
                     this.unDoneMission.push(resmble)
                     this.isAdd = false
                 } else if (!this.isAdd) {
                     this.unDoneMission.splice(this.editIndex, 1, JSON.parse(JSON.stringify(this.additionMission)))
                     this.editIndex = 0
                 }
                 this.additionMission.missionName = ""
                 this.additionMission.MissionContent = ""
                 this.additionMission.beginDate = null
                 this.additionMission.beginTime = null
                 this.additionMission.overDate = null
                 this.additionMission.overTime = null
                 this.additionMission.missionLevel = 0
                 this.additionMission.sliderDefault = 0
                this.additionMission.type = 3
                this.additionMission.isRemind= false
                 this.dialogFormVisible = false

             },
             editDialog(index, item) {
                 this.dialogFormVisible = true
                 this.isAdd = false
                 this.editIndex = index
                 this.additionMission = JSON.parse(JSON.stringify(item))
             },

             seek(keywords) {
                 var newList = []
                 this.unDoneMission.forEach(item => {
                     if (item.missionName.indexOf(keywords) != -1) {

                         newList.push(item)
                     }
                 })
                 return newList
             },
             seekFinishMission(keywords) {
                 var newList = []
                 this.doneMission.forEach(item => {
                     if (item.missionName.indexOf(keywords) != -1) {
                         newList.push(item)
                     }
                 })
                 return newList
             },
             compositorTaskLevel() {
                 this.unDoneMission.sort(this.relative('missionLevel'))
             },
             degreeOfCompletion() {
                 this.unDoneMission.sort(this.relative('sliderDefault'))
             },
             relative(property) {
                 return function (value1, value2) {
                     var value1 = value1[property]
                     var value2 = value2[property]
                     return value2 - value1
                 }
             },
             deleteMission(index, item) {
                 this.unDoneMission.splice(index, 1)
             },
             accomplishedMission(index, item) {
                 item.finished_time = new Date()
                 this.doneMission.push(JSON.parse(JSON.stringify(item)))
                 this.unDoneMission.splice(index, 1)

             },
             deletedFromdoneMission(index, item) {
                 this.doneMission.splice(index, 1)
             },
    },

     filters:{
             dateFormat: function (datestr) {
                 var dt = new Date(datestr)
                 var year = dt.getFullYear()
                 var month = dt.getMonth() + 1
                 var day = dt.getDate()

                 return `${year}-${month}-${day}`
             },
             TimeFormat: function (datestr) {
                 var dt2 = new Date(datestr)
                 var hour = dt2.getHours()
                 var minute = dt2.getMinutes()
                 var second = dt2.getSeconds()

                 return `${hour}:${minute}:${second}`
             },
             MissionLevel: function (num) {
                 if (num == 0 || num == '0') {
                     return '一般'
                 }
                 if (num == 1 || num == '1') {
                     return '重要'
                 }
                 if (num == 2 || num == '2') {
                     return '紧急'
                 }
             },
             MissionType: function (num) {
                 if (num == 3 || num == '3') {
                     return '日常生活'
                 }
                 if (num == 4 || num == '4') {
                     return '工作学习'
                 }
                 if (num == 5 || num == '5') {
                     return '特殊'
                 }
             },
    }
}

</script>


<style>
  .el-table .warning-row {
    background:#409EFF;
  }

  .el-table .success-row {
    background:#67C23A;
  }

  .el-table .general-row{
    background:#EEEE11
  }


</style>
