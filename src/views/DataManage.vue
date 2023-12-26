<template>
    <div>
        <!-- <h1>DataManage.vue</h1> -->
        <el-row>
            <el-divider content-position="left">数据列表</el-divider>

            <el-table ref="deviceTable" :data="devicesData" highlight-current-row @current-change="handleCurrentChange"
                style="width: 100%" v-loading="devLoading">

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
        </el-row>

        <!--分页-->
        <Pagination />
    </div>
</template>

<script>
import Pagination from '../components/Pagination.vue'
export default {
    components: {
        Pagination
    },
    data() {
        return {
            devLoading: false,//设备列表加载状态标志
            currentRow: null,//当前选中的行
            devicesData: [
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
                {
                    "sn_number": "SN001",
                    "category": "TN",
                    "online_status": "1",
                    "data": "0.11",
                    "updated_at": "2023-12-02 20:49:34",
                },
            ],
        };
    },
    methods: {
        handleCurrentChange(val) {
            this.currentRow = val;
            this.devLoading = false;// 隐藏加载状态
        },
    }
};
</script>