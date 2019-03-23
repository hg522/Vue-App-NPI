<template>
  <div id="home" class="container main-container d-flex flex-column p-0 m-0">
    <div class="homenav">
      <nav class="navbar sticky-top text-white navbar-dark bg-dark navbarcolor d-flex justify-content-between">
        <div>
          <img src = "../assets/North-Park-logo.png" class="rounded-circle" height="35px" width="35px" alt="">
        </div>
        <div class="d-flex align-items-center justify-content-end cursor" v-on:click="logout()">
          <h6 class="m-0">Sign Out</h6>
          <button type="button" title="Sign out" class="btn btn-default p-0 m-0 pl-2" aria-label="Right Align">
            <font-awesome-icon icon="power-off" :style="{ color: 'white' }"/>
          </button>
        </div>
      </nav>
    </div>
    <div class="container page-container pt-2">
      <div class="row summary-bar">
        <div class="col">
          <div class="container bg-color1 text-white d-flex align-items-center justify-content-center rounded">
            <div>
              <h5 class="font-weight-bold pt-1">{{numTickets.toLocaleString()}}</h5>
              <h6 class="font-14">Total Number of Tickets</h6>
            </div>
          </div>
        </div>
        <div class="col">
          <div class="container bg-color2 text-white d-flex align-items-center justify-content-center rounded">
            <div>
              <h5 class="font-weight-bold pt-1">{{summaryData.maxFunctionalArea}} ({{summaryData.maxFunctionalAreaNum.toLocaleString()}})</h5>
              <h6 class="font-14">Functional Area with Max Tickets</h6>
            </div>
          </div>
        </div>
        <div class="col">
          <div class="container bg-color3 text-white d-flex align-items-center justify-content-center rounded">
            <div>
              <h5 class="font-weight-bold pt-1">{{summaryData.maxTicketType}} ({{summaryData.maxTicketTypeNum.toLocaleString()}})</h5>
              <h6 class="font-14">Ticket Type with Max Tickets</h6>
            </div>
          </div>
        </div>
      </div>
      <div class="row chart-bar pt-1">
        <div class="col-sm-4">
          <div class=" d-flex align-items-center justify-content-center rounded">
            <div class="container dou-chart-container p-0 m-0">
              <doughnut-chart v-if="'severityChartData != null'" :chartData="severityChartData" :options="douoptions"></doughnut-chart>
            </div>
          </div>
        </div>
        <div class="col-sm-4">
            <div class=" d-flex align-items-center justify-content-center rounded">
              <div class="container bar-chart-container p-0 m-0">
                <bar-chart v-if="'areaChartData != null'" :chartData="areaChartData" :options="baroptions"></bar-chart>
              </div>
            </div>
        </div>
        <div class="col-sm-4">
          <div class=" d-flex align-items-center justify-content-center rounded">
            <div class="container pie-chart-container p-0 m-0">
              <pie-chart v-if="'typeChartData != null'" :chartData="typeChartData" :options="pieoptions"></pie-chart>
            </div>
          </div>
        </div>
      </div>
      <div class="row table-container">
        <div class="col">
            <datatable :data="data"></datatable>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import json from '../assets/json/Sample Data.json'
  import DoughnutChart from "../components/DoughnutChart.vue"
  import PieChart from "../components/PieChart.vue"
  import BarChart from "../components/BarChart.vue"
  import datatable from "../components/DataTable.vue"

  export default {
    name: 'home',
    components: { DoughnutChart,PieChart,BarChart,datatable },
    data() {
      return {
          data:json,
          numTickets:0,
          severityChartData:{},
          typeChartData:{},
          areaChartData:{},
          summaryData:{
            "maxFunctionalArea":"",
            "maxFunctionalAreaNum":0,
            "FunctionalAreaMap":{},
            "maxTicketType":"",
            "maxTicketTypeNum":0,
            "TicketTypeMap":{},
            "SeverityMap":{},
            "PriorityMap":{}
          },
        douoptions:{
          responsive: true,
          legend: {
            position: "right",
            labels:{
              boxWidth:20,
              fontColor:"#fff"
            }

          },
          title: {
            display: true,
            text: "Ticket Severity Chart",
            fontColor:"#fff"
          },
          animation: {
            animateScale: true,
            animateRotate: true
          }
        },
        pieoptions: {
          responsive: true,
          legend: {
            position: "right",
            labels:{
              boxWidth:20,
              fontColor:"#fff"
            }

          },
          title: {
            display: true,
            text: "Ticket Type Chart",
            fontColor:"#fff"
          },
          animation: {
            animateScale: true,
            animateRotate: true
          }
        },
        baroptions:{
          responsive: true,
          legend: {
            display:false,
            position: "top",
            labels:{
              boxWidth:20,
              fontColor:"#fff"
            }

          },
          title: {
            display: true,
            text: "Functional Area Chart",
            fontColor:"#fff"
          },
          animation: {
            animateScale: true,
            animateRotate: true
          },
          scales:{
            yAxes:[{
              gridLines: {
                display: false,
              },
              ticks:{
                beginAtZero:true,
                fontColor: "#fff"
              }
            }],
            xAxes: [{
              gridLines: {
                display: false,
              },
              ticks: {
                fontColor: "#fff"
              }
            }],
          }
        }
      }
    },
    mounted(){
      this.numTickets = this.data.length;
      this.getSummaryData();
      this.getSeverityChartData();
      this.getTypeChartData();
      this.getAreaChartData();
    },
    methods:{
      logout(){
        this.$emit("validUser", false);
        this.$router.replace({ name: "login" });
      },
      getSummaryData(){
        var maxfiledgainst = {};
        var ticketType = {}
        var ticketPriority = {};
        var ticketSeverity = {};

        for(var i=0;i<this.numTickets;i++){
          if(this.data[i].FiledAgainst != null){
            if(maxfiledgainst.hasOwnProperty(this.data[i].FiledAgainst)){
              maxfiledgainst[this.data[i].FiledAgainst]+=1;
            }else{
              maxfiledgainst[this.data[i].FiledAgainst]=1;
            }
          }

          if(this.data[i].TicketType != null){
            if(ticketType.hasOwnProperty(this.data[i].TicketType)){
              ticketType[this.data[i].TicketType]+=1;
            }else{
              ticketType[this.data[i].TicketType]=1;
            }
          }

          if(this.data[i].Priority != null){
            var priort = this.data[i].Priority.split(" - ");
            if(ticketPriority.hasOwnProperty(priort[1])){
              ticketPriority[priort[1]]+=1;
            }else{
              ticketPriority[priort[1]]=1;
            }
          }

          if(this.data[i].Severity != null){
            var sever = this.data[i].Severity.split(" - ");
            if(ticketSeverity.hasOwnProperty(sever[1])){
              ticketSeverity[sever[1]]+=1;
            }else{
              ticketSeverity[sever[1]]=1;
            }
          }

        }
        var maxfileag="";
        var maxfil = 0;
        for (var key1 in maxfiledgainst) {
          if (maxfiledgainst.hasOwnProperty(key1)) {
            if(maxfiledgainst[key1]>maxfil){
              maxfil = maxfiledgainst[key1];
              maxfileag = key1;
            }
          }
        }

        var maxticketType="";
        var ticketTypeNum = 0;
        for (var key2 in ticketType) {
          if (ticketType.hasOwnProperty(key2)) {
            if(ticketType[key2]>ticketTypeNum){
              ticketTypeNum = ticketType[key2];
              maxticketType = key2;
            }
          }
        }
        this.summaryData.maxFunctionalArea = maxfileag;
        this.summaryData.maxFunctionalAreaNum = maxfil;
        this.summaryData.maxTicketType = maxticketType;
        this.summaryData.maxTicketTypeNum = ticketTypeNum;
        this.summaryData.FunctionalAreaMap = maxfiledgainst;
        this.summaryData.TicketTypeMap = ticketType;
        this.summaryData.SeverityMap = ticketSeverity;
        this.summaryData.PriorityMap = ticketPriority;
      },
      getSeverityChartData(){
        var data ={
          datasets:null,
          labels:[]
        }

        var colors=["rgba(255, 99, 132, 1)","rgba(255, 159, 64, 1)","rgba(255, 205, 86, 1)",
          "rgba(75, 192, 192, 1)","rgba(54, 162, 235, 1)","rgba(153, 102, 255, 1)","rgba(201, 203, 207, 1)"];

        var backgroundColor= [];
        var borderColor = [];
        var chdata=[];
        var j = 0;
        for(var key in this.summaryData.SeverityMap){
          data.labels.push(key);
          chdata.push(this.summaryData.SeverityMap[key]);
          if(j==colors.length-1){
            j=0;
          }
          backgroundColor.push(colors[j]);
          borderColor.push(colors[j]);
          j++;
        }
        data.datasets=[{
          data:chdata,
          backgroundColor:backgroundColor,
          borderColor:borderColor
        }]
        this.severityChartData = data;
      },
      getTypeChartData(){
        var data ={
          datasets:null,
          labels:[]
        }

        var colors=["rgba(255, 99, 132, 1)","rgba(255, 159, 64, 1)","rgba(255, 205, 86, 1)",
          "rgba(75, 192, 192, 1)","rgba(54, 162, 235, 1)","rgba(153, 102, 255, 1)","rgba(201, 203, 207, 1)"];

        var backgroundColor= [];
        var borderColor = [];
        var chdata=[];
        var j = 0;
        for(var key in this.summaryData.TicketTypeMap){
          data.labels.push(key);
          chdata.push(this.summaryData.TicketTypeMap[key]);
          if(j==colors.length-1){
            j=0;
          }
          backgroundColor.push(colors[j]);
          borderColor.push(colors[j]);
          j++;
        }
        data.datasets=[{
          data:chdata,
          backgroundColor:backgroundColor,
          borderColor:borderColor
        }]
        this.typeChartData = data;
      },
      getAreaChartData(){
        var data ={
          datasets:null,
          labels:[]
        }

        var colors=["rgba(255, 99, 132, 1)","rgba(255, 159, 64, 1)","rgba(255, 205, 86, 1)",
          "rgba(75, 192, 192, 1)","rgba(54, 162, 235, 1)","rgba(153, 102, 255, 1)","rgba(201, 203, 207, 1)"];

        var backgroundColor= [];
        var borderColor = [];
        var chdata=[];
        var j = 0;
        for(var key in this.summaryData.FunctionalAreaMap){
          data.labels.push(key);
          chdata.push(this.summaryData.FunctionalAreaMap[key]);
          if(j==colors.length-1){
            j=0;
          }
          backgroundColor.push(colors[j]);
          borderColor.push(colors[j]);
          j++;
        }
        data.datasets=[{
          data:chdata,
          backgroundColor:backgroundColor,
          borderColor:borderColor
        }]
        this.areaChartData = data;
      }
    }
  }
</script>

<style scoped>
  .homenav{
    /*height: 55px;*/
  }

  .navbarcolor{
    background-color: #1e2124 !important;
  }


  #app{
    /*background-image: linear-gradient(to right top, #111315, #181a1c, #1e2124, #25282b, #2c2f33);*/
  }

  .main-container{
    /*height: 100%;*/
    width: 100%;
    max-width: 100%;
    /*background-image: linear-gradient(to right top, #111315, #181a1c, #1e2124, #25282b, #2c2f33);*/
    /*background: #f5f5f5;*/

  }

  .page-container{
    /*height: 100%;*/
    width: 100%;
    max-width: 100%;
    /*background: #2b3035;*/
    /*background-image: linear-gradient(to right top, #21262a, #292e32, #32373b, #3b3f43, #44484c);*/
    /*background-image: linear-gradient(to right top, #111315, #181a1c, #1e2124, #25282b, #2c2f33);*/
    /*background:linear-gradient(-135deg,#c850c0,#4158d0);*/
  }

  .table-container .container{
    height: 100%;
    width:100%;
  }

  .chart-bar{
    /*height: 325px;*/
  }

  .chart-bar .container{
    height: 100%;
  }

  .summary-bar{
    /*height: 70px;*/
  }

  .summary-bar .container{
    height: 100%;
    width: 100%;
  }

  .bg-color1{
    color: #2b3035;
    background-color: rgba(146, 112, 201, 0.5) !important;
    /*border-color: rgba(146, 112, 201, 1) !important;
    border-width: 2px !important;*/
  }
  .bg-color2{
    color: #2b3035;
    background-color: rgba(	255, 150, 113, 0.5) !important;
    /*border-color: rgba(255, 150, 113, 1) !important;
    border-width: 2px !important;*/

  }
  .bg-color3{
    color: #2b3035;
    background-color: rgba(255, 199, 95, 0.5) !important;
    /*border-color: rgba(255, 199, 95, 1) !important;
    border-width: 2px !important;*/
  }
  .dou-chart-container{
    width: 270px !important;
    height: 100% !important;
  }
  .pie-chart-container{
    width: 270px !important;
    height: 100% !important;
  }
  .bar-chart-container{
    width: 270px !important;
    height: 100% !important;
  }
  .font-14{
    font-size:14px;
  }
  .cursor{
    cursor:pointer;
  }



</style>