<template>
    <div class="manage">
        <!-- <h1>DataManage.vue</h1> -->
        <el-divider content-position="left">数据列表</el-divider>

        <el-table ref="deviceTable" :data="tableData" highlight-current-row @current-change="handleCurrentChange"
            style="width: 100%" v-loading="devLoading" height="100%">

            <el-table-column type="selection" width="50">
            </el-table-column>
            <el-table-column type="index" label="序号" width="70" show-overflow-tooltip>
            </el-table-column>

            <el-table-column property="sn_number" label="设备SN编号" width="150" show-overflow-tooltip>
            </el-table-column>

            <el-table-column property="category" label="设备分类" width="150" show-overflow-tooltip>
            </el-table-column>

            <el-table-column property="online_status" label="在线状态" width="150" show-overflow-tooltip>
            </el-table-column>

            <el-table-column property="data" label="数据(mg/L)" width="150" show-overflow-tooltip>
            </el-table-column>

            <el-table-column property="updated_at" label="检测时间" width="200" show-overflow-tooltip>
            </el-table-column>

            <el-table-column width="100">
                <template slot-scope="scopeEdit">
                    <el-button type="primary" @click="editDevice(scopeEdit.row)" size="mini">编辑数据</el-button>
                </template>
            </el-table-column>

            <el-table-column>
                <template slot-scope="scopeDel">
                    <el-button type="danger" @click="delDevice(scopeDel.row)" size="mini">删除数据</el-button>
                </template>
            </el-table-column>

        </el-table>

        <!--分页-->
        <template>
            <el-pagination background layout="prev, pager, next" :total="total" @current-change="handlePage"
                @prev-click="handlePrevClick">
            </el-pagination>
        </template>
    </div>
</template>

<script>
export default {
    components: {
    },
    data() {
        return {
            devLoading: false,//设备列表加载状态标志
            currentRow: null,//当前选中的行
            devicesData: [{
                "sn_number": "SN001",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN002",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN003",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN004",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN005",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN006",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN007",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN008",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN009",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN010",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN011",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN012",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN013",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN014",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN015",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN016",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN017",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN018",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN019",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN020",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "sn_number": "SN021",
                "category": "TN",
                "online_status": "1",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            ],
            tableData: [],
            total: 0,//当前的总条数，默认是0，获取到数据后将total重新给值为tableData的长度
            pageSizes: 10,//一页数据的个数

        };
    },
    methods: {
        changeData(num) {
            var i = 0;
            this.tableData = [];//清空数组
            for ((i = num * 10 - 10); i < num * 10; i++) {
                if (this.devicesData[i] === undefined) {
                    break;
                } else {
                    this.tableData[i - ((num - 1) * 10)] = this.devicesData[i];
                }
            }
            // console.log(this.tableData)
        },
        handlePage(num) {//点击页面页码的回调函数,返回的是点击的页码数
            // console.log(num)//修改tableData显示的数据
            // this.$refs.deviceTable.doLayout();
            this.changeData(num)
        },
        handlePrevClick(num) {//点击页面上一页的回调函数,返回的是点击的页码数，这个函数其实是不需要的
            // console.log(num)
            this.changeData(num)
        },
        handleCurrentChange(val) {
            this.currentRow = val;
            this.devLoading = false;// 隐藏加载状态
        },
        getData() {
            this.changeData(1);
            this.total = this.devicesData.length || 0;
            // console.log(this.total)
        },
    },
    mounted() {
        this.getData();
    }
};
</script>

<style lang="less" scoped>

</style>