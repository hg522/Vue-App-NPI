<template>
    <div class="container main-container">
        <div class="row">
            <div class="col-sm-12 d-flex align-items-center justify-content-left">
                <div class="row search-page-bar">
                    <div class="col-sm-4">
                        <form id="search" class="p-2" style="width:auto">
                            <div class="input-group">
                                <input type="text" name="search" class="form-control table-search pl-2" v-model="searchQuery" required="" autocomplete="off" placeholder="Enter to search table">
                                <div class="input-group-append">
                                    <span class="input-group-text search-icon"><font-awesome-icon icon="search" class=""/></span>
                                </div>
                            </div>
                        </form>
                    </div>
                    <div class="col-sm-8 d-flex align-items-center justify-content-end">
                        <div class="row">
                            <div>
                                <ul class="pagination pagination-sm p-0 m-0">
                                    <li class="total-pages page-link mr-2 rounded">Page {{currPage}} of {{lastPage}}</li>
                                    <li class="page-item" :class="{ disabled: currPage == 1 }" @click="getPagedData(1)"><a class="page-link" href="#">First</a></li>
                                    <li class="page-item" :class="{ disabled: currPage == 1 }" @click="prevPage"><a class="page-link" href="#">Previous</a></li>
                                    <!--<li class="page-item" :class="{ active: currPage == 1 }"><a class="page-link" href="#">1</a></li>-->
                                    <li class="page-item"><input type="text" class="page-link page-num-search" name="pageNum" v-model="pageNum"></li>
                                    <li class="page-item" :class="{ disabled: currPage == lastPage }" @click="nextPage"><a class="page-link" href="#">Next</a></li>
                                    <li class="page-item" :class="{ disabled: currPage == lastPage }" @click="getPagedData(-1)"><a class="page-link" href="#">Last</a></li>
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <div class="table-responsive table-borderless table-sm table-dark table-hover">
                    <table class="table">
                        <thead>
                        <tr>
                            <th v-for="key in ticketColumns" @click="sortBy(key)" :class="{ active: sortKey == key }" :key="key">
                                {{ colMap[key] }}
                                <font-awesome-icon icon="sort-up" v-if='sortDir == "asc" && sortKey==key' :style="{ color: 'white' }"/>
                                <font-awesome-icon icon="sort-down" v-if='sortDir == "desc" && sortKey==key' :style="{ color: 'white' }"/>
                            </th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr v-for="el in filteredData" :key="el.ticket" @click="openModal(el)">
                            <td v-for="col in ticketColumns" :key="col">
                                {{el[col]}}
                            </td>
                        </tr>
                        <modal v-if="showModal" @close="closeModal">
                            <h4 slot="header">{{modalHeaderData}}</h4>
                            <div slot="body">
                                <ul class="list-group list-group-flush">
                                    <li v-for="(value, key) in modalBodyData" :key="key" class="list-group-item d-flex justify-content-between align-items-center">
                                        <!--{{ key.charAt(0).toUpperCase() + key.slice(1) }}-->
                                        {{ colMap[key] }}
                                        <span class="badge badge-primary badge-pill">{{ value }}</span>
                                    </li>
                                </ul>
                            </div>
                        </modal>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import modal from "../components/Modal.vue"

    export default {
        name: "datatable",
        props:['data'],
        components: { modal },
        data(){
            return{
                tdata:this.data,
                searchQuery:"",
                ticketColumns:[],
                allTicketColumns:["ticket","Requestor","RequestorSeniority","ITOwner","FiledAgainst","TicketType",
                    "Severity","Priority","daysOpen","Satisfaction","Ticket Creation Date"],
                sortKey:"ticket",
                sortDir:"asc",
                pageSize:8,
                currPage:1,
                lastPage:1,
                prevDataLength:0,
                filteredData:[],
                showModal:false,
                modalHeaderData:"TICKET DATA",
                modalBodyData:{},
                modalFooterData:"",
                dataProcessFlag:1,
                paginationSize:5,
                sortedData:{},
                pageNum:1,
                colMap:{"ticket":"Ticket",
                    "Requestor":"Requestor",
                    "RequestorSeniority":"Requestor Seniority",
                    "ITOwner":"IT Owner",
                    "FiledAgainst":"Filed Against",
                    "TicketType":"Ticket Type",
                    "Severity":"Severity",
                    "Priority":"Priority",
                    "daysOpen":"Days Open",
                    "Satisfaction":"Satisfaction",
                    "Ticket Creation Date":"Ticket Creation Date"}
            }
        },
        mounted(){
            /*this.getTableColumns();*/
            this.ticketColumns = this.allTicketColumns.slice(0,8);
            this.getSortedData();
            this.getPagedData(1);
        },
        watch:{
            searchQuery(){
                this.getSortedData();
                this.getPagedData(1);
            },
            pageNum(){
                if(this.pageNum!=this.currPage){
                    this.currPage = this.pageNum;
                    this.getPagedData();
                }
            },
            currPage(){
                this.pageNum=this.currPage;
            }
        },
        methods:{
            getTableColumns(){
                if(this.tdata.length!=0){
                    var j=0;
                    for(var key in this.tdata[0]){
                        if(j>7)
                            break;
                        this.ticketColumns.push(key);
                        j++;
                    }
                }
            },
            sortBy(s) {
                if(s === this.sortKey) {
                    this.sortDir = this.sortDir==='asc'?'desc':'asc';
                }
                this.sortKey = s;
                this.getSortedData();
                this.getPagedData();
            },
            nextPage() {
                if((this.pageSize*this.currPage) < this.sortedData.length){
                    this.currPage++;
                    this.getPagedData();
                }
            },
            prevPage() {
                if(this.currPage>1) {
                    this.currPage--;
                    this.getPagedData();
                }
            },
            getPagedData(val){
                if(val && val==1){
                    this.prevDataLength = this.sortedData.length;
                    this.currPage = 1;
                }else  if(val && val==-1){
                    this.currPage = this.lastPage;
                }

                var currPage = this.currPage;
                var pageSize = this.pageSize;
                var pagedData1 = this.sortedData.filter(function (row,index) {
                    var start = (currPage-1)*pageSize;
                    var end = currPage*pageSize;
                    if(index >= start && index < end) return true;
                })
                var p = this.sortedData.length/this.pageSize;
                this.lastPage = this.sortedData.length%this.pageSize==0?Math.floor(p):Math.floor(p)+1;
                this.filteredData = pagedData1;
            },
            openModal(data){
                this.showModal=true;
                this.modalBodyData = data;
            },
            closeModal(){
                this.showModal=false;
            },
            getSortedData(){
                var sortKey = this.sortKey;
                var sortDir = this.sortDir;
                var filterKey = this.searchQuery && this.searchQuery.toLowerCase();
                var ticketData = this.tdata;
                if (filterKey) {
                    ticketData = ticketData.filter(function (row) {
                        return Object.keys(row).some(function (key) {
                            /*return String(row[key]).toLowerCase() == filterKey.toLowerCase();*/
                            return String(row[key]).toLowerCase().indexOf(filterKey) > -1;
                        })
                    })
                }
                if (sortKey) {
                    ticketData = ticketData.slice().sort(function (a, b) {
                        var modifier = 1;
                        if(sortDir === 'desc') modifier = -1;
                        if(a[sortKey] < b[sortKey]) return -1 * modifier;
                        if(a[sortKey] > b[sortKey]) return 1 * modifier;
                        return 0;
                    })
                }

                this.sortedData = ticketData;
            }
        }
    }
</script>

<style scoped>
    .main-container{
        height: 100%;
        width:100%;
        max-width: 100% !important;
    }
    .table-search{
        background-color: #2c2f33 !important;
        color:#fff;
        border: 1px solid transparent;
        /*border-width: 1px;
        border-style: inset;
        border-color: #fff;*/
        height: 30px;
        /*width:350px;*/
        max-width:350px;
        /*border-image: initial;*/
    }

    table {
        border: 2px solid #2c2f33;
        border-radius: 2px;
        color: #fff;
        background-color: rgba(0, 0, 0, 0.0);
        font-size:14px;
    }

    .table-dark{
        background-color: rgba(0, 0, 0, 0.0);
    }

    th {
        background-color: #1B1C1E;
        cursor: pointer;
    }

    tbody>tr{
        border: 1px solid #2c2f33;
    }

    tbody>tr:hover{
        cursor:pointer;
    }
    .search-page-bar{
        width:100%;
    }

    th.active {
        color: rgba(146, 112, 201, 1);
    }

    .page-link {
        color: #fff;
        /*background-color: radial-gradient(circle, #1c1d21, #191b1e, #17181b, #141618, #111315);*/
        background-color: #141618;
        border: 1px solid #111315;
    }
    .page-link:hover {
        color: #fff;
        background-color: #9270C9;
        border: 1px solid #9270C9;
    }
    .page-item.active .page-link {
        z-index: 1;
        color: #fff;
        /*background-color: #007bff;*/
        background-color: rgba(146, 112, 201, 1);
        background-image: None;
        border-color: rgba(146, 112, 201, 1);
    }

    .page-item.disabled .page-link {
        color: #6c757d;
        pointer-events: none;
        cursor: auto;
        background-color: #141618;
        border-color: #141618;
    }

    .list-group-item {
        position: relative;
        display: block;
        padding: 2px;
        margin-bottom: -1px;
        background-color: transparent;
        border: 1px solid transparent;
        font-size:16px;
    }
    .badge {
        font-size: 90%;
        font-weight: normal;
    }
    .badge-primary {
        color: #fff;
        background-color: rgba(146, 112, 201, 0.5);
    }

    .page-num-search {
        width: 100px;
        text-align: center;
        background-color: #202325;
        border: 1px solid #141618;
    }

    .page-num-search:hover {
        color: #fff;
        background-color: #202325;
        border: 1px solid #9270C9;
    }

    .total-pages{
        color: #fff;
        background-color: #141618;
    }

    .total-pages:hover {
        color: #fff;
        background-color: #141618;
        border: 1px solid #141618;
    }

    input[type=text]:focus {
        background-color: #2c2f33;
    }
    input[type=text]::selection{
        background-color: #2c2f33;
    }

    .search-icon{
        background-color: #2c2f33 !important;
        border:None;
        height: 1.8rem;
        margin-top: 0.05rem;
    }
</style>