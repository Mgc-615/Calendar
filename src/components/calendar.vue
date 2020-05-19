<template>
  <div class="hello">
    <div class="threetype">
      <span class='typeflag flagtwo'></span>&nbsp;工作日&nbsp;
      <span class='typeflag flagthree'></span>&nbsp;今日日期&nbsp;
      <span class='typeflag flagone'></span>&nbsp;休息日&nbsp;
    </div>
    <el-calendar :first-day-of-week="'7'" class='main' >
      <template slot="dateCell" slot-scope="{date, data}">
        <div class="item">
           <div class="top">
        <!-- {{solarDate2lunar(data.day) }} -->
          <span class="onedate">{{solarDate2lunar(data.day,'one')}}</span>
          <span class="twodate">{{solarDate2lunar(data.day,'two')}}</span>
          <span class='flag' :class="getMyDay(new Date(data.day))?'flagone':getToday(data.day)?'flagthree':'flagtwo'"></span>
           </div>
           <div class="center" :class="getFes(data.day)?'centerone':'centertwo'">
             {{getFes(data.day)}}
           </div>
         <div>
             <span class="logo unactive"> </span>
              <img class="logoimg" src="@/assets/logo.png" alt="" />
        </div>
        <span v-for='(item,key) in msg' @dblclick="aclick(data,item)" :key="key"
        >
        <el-tooltip  placement="right" :disabled="item.msg.length<=3">
          <div slot="content">
            　<p v-for='(msg,i) in item.msg' :key="i">
                <span style="display:inline-block;width:70px;">{{msg.split('|')[0]}}</span>
                <span>{{msg.split('|')[1]}} </span>
            </p>
          </div>
        <ul class='ulmsg' v-if='item.date===data.day'>
          <div v-if="item.msg.length>0" >
             <span class="logo active"> </span>
              <img class="logoimg" src="@/assets/logo.png"/>
          </div>
          <li v-for='(msg,i) in item.msg' :key="i">
            <span style="display:inline-block;width:70px;">{{msg.split('|')[0]}} </span>
            <span>{{msg.split('|')[1]}} </span>
          </li>
          </ul>
        </el-tooltip>
        </span>
        </div>
      </template>
    </el-calendar>
    <el-dialog title="修改备注" :visible.sync="dialogFormVisible" :close-on-click-modal="false">
        <el-button type="primary" size="mini" @click="add(changemsg)" class="addbtn">添加</el-button>
        <p v-for='(msg,i) in changemsg.msg' :key="i">
                <el-input
                  style="display:inline-block;width:140px;"
                  :placeholder="msg"
                  :disabled="true">
                </el-input>
                <el-button type="primary" size="mini" icon="el-icon-edit" circle @click="edit(changemsg,i)"></el-button>
                <el-button type="danger" size="mini"  icon="el-icon-delete" circle @click="dele(changemsg,i)"></el-button>
        </p>
    </el-dialog>

  </div>
</template>

<script>
import calendar from "@/assets/calendar.js";
export default {
  name: "HelloWorld",
  data() {
    return {
      msg: [{date:"2020-03-12",msg:['8:00-9:30|1人','9:30-11:00|8人','14:30-16:00|1人','14:30-16:00|1人']},{date:"2020-03-21",msg:['9:30-11:00|8人','14:30-16:00|1人']}],
      dialogFormVisible:false,
      changemsg:{},
      input:'',
      inputshow:true
    };
  },
  created(){
  },
  methods:{
    aclick(data,msg){
      console.log(data,msg)
      this.dialogFormVisible =true
      this.changemsg= msg
    },
    solarDate2lunar(solarDate,type) {
      var solar = solarDate.split("-");
      var lunar = calendar.solar2lunar(solar[0], solar[1], solar[2]);
      if(type==='one'){
         if(lunar.IDayCn==='初一'){
            // return lunar.IMonthCn + lunar.IDayCn+ "\n\n" + solar[1] + "-" + solar[2] ;//三月初一 03-24
            return lunar.IMonthCn + lunar.IDayCn;//三月初一 03-24
          }
          return lunar.IDayCn;
      }
      return solar[2]+"日" ;
    },
    getToday(date){
      let today = new Date()
      let month = today.getMonth()+1>10?today.getMonth()+1:'0'+(today.getMonth()+1)
      let day = today.getDate()>10?today.getDate():'0'+(today.getDate())
      let s = today.getFullYear()+'-'+month+'-'+day
      return date==s
    },
    getMyDay(date){
      var week;
      if(date.getDay()==0) week="周日"
      if(date.getDay()==6) week="周六"
      return week;
    },
    getFes(solarDate){
      var solar = solarDate.split("-");
      var lunar = calendar.solar2lunar(solar[0], solar[1], solar[2]);
      return lunar.festival? lunar.festival:lunar.lunarFestival
    },
    edit(obj,i){
      let str =obj.msg[i]
      str=str.substr(0, str.length - 1);  
      this.$prompt('请输入修改', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          inputValue:str,
          inputPattern: /^([0-9]|1[0-9]|2[0-3]):[0-5][0-9]-([0-9]|1[0-9]|2[0-3]):[0-5][0-9][|][0-9]*[1-9][0-9]*$/,
          inputErrorMessage: '格式不正确',
          closeOnClickModal:false,
        }).then(({ value }) => {
          this.changemsg.msg.splice(i,1,value+'人')
          this.$message({
            type: 'success',
            message: '修改成功'
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '取消修改'
          });       
        });
    },
    dele(obj,i){
      console.log(obj,i)
      obj.msg.splice(i,1)
      this.changemsg=obj
    },
    add(obj){
      this.$prompt('请输入添加', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          inputValue:'0:00-0:00|1',
          inputPattern: /^([0-9]|1[0-9]|2[0-3]):[0-5][0-9]-([0-9]|1[0-9]|2[0-3]):[0-5][0-9][|][0-9]*[1-9][0-9]*$/,
          inputErrorMessage: '格式不正确',
          closeOnClickModal:false,
        }).then(({ value }) => {
          this.changemsg.msg.push(value+'人')
          this.$message({
            type: 'success',
            message: '添加成功'
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '取消添加'
          });       
        });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  .el-calendar__title{
    margin-left: 50%;
    transform: translate(-50%);
    font-size: 1.5em;
    font-weight: 500;
  }
  .el-calendar__button-group{
    margin-right: 20%;
  }
  .el-calendar-table .el-calendar-day{
    height: 100px;
  }
  .el-calendar-table thead th{
    font-weight: 500;
  }
</style>
<style scoped>
.main{
  width: 80%;
}
.threetype{
  position: absolute;
  left: 65%;
  top: 10.5%;
  font-size: 13px;
}
.typeflag{
  height: 8px;
  width: 8px;
  display: inline-block;
  border-radius: 50%; 
}
.item{
  position: relative;
}
.top{
  height: 10px;
  font-size: 13px;
}
.center{
  position: absolute;
  width: 112%;
  height: 20px;
  line-height: 20px;
  left: -10px;
  margin-top: 10px;
  margin-left: 0;
  border-bottom: 1px dashed #EBE8EC;
  font-size: 13px;
}
.centerone{
  background: #F0F5F1;
  color:#6CBE77;
}
.centertwo{
  background: rgba(255, 255, 255, 0.1);
  color:#fff;
}
.logo{
  width: 0;
  height: 0;
  border: 24px solid;
  border-left: 0;
  transform:rotate(90deg);
  display: inline-block;
  position: absolute;
  left: 3px;
  top: -20px;
}
.onedate{
  float: left;
}
.twodate{
  float: right;
}
.flag{
  height: 8px;
  width: 8px;
  float: right;
  display: block;
  position: relative;
  top: 5px;
  left: -5px;
  border-radius: 50%; 
}
.flagone{
  background: #ccc;
}
.flagtwo{
  background: #28B541;
}
.flagthree{
  background: #f00;
}
.unactive{
   border-color: transparent transparent #F4F6F8;
   top: -21px;
}
.active{
   border-color: transparent transparent #64A7F9;
}
.logoimg{
  height: 15px;
  width: 15px;
   position: absolute;
  left: -8px;
  top: -10px;
}
.ulmsg{
  margin-top: 30px;
  height: 50px;
  overflow: hidden;
}
.ulmsg li{
  list-style: none;
  font-size: 13px;
  text-align: left;
  z-index: -1;
}
.addbtn{
  position: absolute;
  right: 10%;
  bottom: 15%;
}
</style>
