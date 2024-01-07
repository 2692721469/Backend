<template>
    <el-card body-style="padding-bottom: 0;">

        <div class="manage">
            <el-divider content-position="left">数据操作</el-divider>

            <div class="infoConatiner">
                <el-row style="border-radius: 5px;">
                    <el-col :span="17">
                        <el-form :model="searchData" ref="searchData" label-width="15%">
                            <el-form-item label="设备SN编号：" prop="SNnumber" :rules="[
                                { required: true, message: 'SN编号为空是查询所有设备', trigger: 'blur' },
                            ]">
                                <el-col :span="18">
                                    <el-input v-model="searchData.SNnumber" placeholder="请输入设备SN编号..." clearable></el-input>
                                </el-col>
                                <el-col :span="4" style="display: flex;justify-content: center;">
                                    <el-button type="primary" @click="searchDevice(searchData.SNnumber)"
                                        :loading="false">搜索</el-button>
                                </el-col>
                            </el-form-item>
                        </el-form>
                    </el-col>
                    <el-col :span="7">
                        <el-col :span="12">
                            <el-button type="primary" @click="exportToExcel('select')" size="middle"
                                style="margin-right: 20px;">导出选定数据</el-button>
                        </el-col>
                        <el-col :span="12">
                            <el-button type="primary" @click="exportToExcel('all')" size="middle">导出全部数据</el-button>
                        </el-col>
                    </el-col>
                </el-row>

            </div>

            <div class="dataListContainer">
                <el-divider content-position="left">数据列表</el-divider>

                <el-table ref="deviceTable" :data="tableData" highlight-current-row @current-change="handleCurrentChange"
                    style="width: 100%" v-loading="devLoading" height="100%" @selection-change="handleSelectionChange">

                    <el-table-column type="selection" width="50">
                    </el-table-column>

                    <el-table-column property="index" label="序号" width="70" show-overflow-tooltip>
                    </el-table-column>

                    <el-table-column property="sn_number" label="设备SN编号" width="150" show-overflow-tooltip>
                    </el-table-column>

                    <el-table-column property="category" label="设备分类" width="150" show-overflow-tooltip>
                    </el-table-column>

                    <el-table-column property="data" label="数据(mg/L)" width="150" show-overflow-tooltip>
                    </el-table-column>

                    <el-table-column property="updated_at" label="检测时间" width="230" show-overflow-tooltip>
                    </el-table-column>

                    <el-table-column width="100">
                        <template slot-scope="scopeEdit">
                            <el-button type="primary" @click="editDevice(scopeEdit.row)" size="mini"
                                disabled>编辑数据</el-button>
                        </template>
                    </el-table-column>

                    <el-table-column>
                        <template slot-scope="scopeDel">
                            <el-button type="danger" @click="delDevice(scopeDel.row)" size="mini" disabled>删除数据</el-button>
                        </template>
                    </el-table-column>

                </el-table>
            </div>


            <!--分页-->
            <template>
                <div style="padding: 20px;">
                    <el-pagination background layout="prev, pager, next" :total="total" @current-change="handlePage"
                        @prev-click="handlePrevClick">
                    </el-pagination>
                </div>
            </template>

            <!-- Form -->
            <el-dialog title="导出数据" :visible.sync="dialogFormVisible">
                <el-form :model="excelForm" :rules="formRules">
                    <el-form-item label="导出文件名称：" :label-width="formLabelWidth" prop="excelName">
                        <el-input v-model="excelForm.excelName" autocomplete="off"></el-input>
                    </el-form-item>

                    <el-row :gutter="24">
                        <el-col :span="12" style="display: flex;justify-content: center;">
                            <el-form-item label="设置表头颜色：" :label-width="'100%'">
                                <el-color-picker v-model="excelForm.headerColor"></el-color-picker>
                            </el-form-item>
                        </el-col>
                        <el-col :span="12" style="display: flex;justify-content: center;">
                            <el-form-item label="设置表格颜色：" :label-width="'100%'">
                                <el-color-picker v-model="excelForm.bodyColor"></el-color-picker>
                            </el-form-item>
                        </el-col>
                    </el-row>

                    <el-row :gutter="24">
                        <el-col :span="12">
                            <el-form-item label="设置表头字体大小：" :label-width="'70%'" prop="headerFontSize">
                                <el-input v-model.number="excelForm.headerFontSize" autocomplete="off"></el-input>
                            </el-form-item>
                        </el-col>
                        <el-col :span="12">
                            <el-form-item label="设置表格字体大小：" :label-width="'70%'" prop="bodyFontSize">
                                <el-input v-model.number="excelForm.bodyFontSize" autocomplete="off"></el-input>
                            </el-form-item>
                        </el-col>
                    </el-row>

                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click="dialogFormVisible = false">取 消</el-button>
                    <el-button type="primary" @click="exportData()">确 定</el-button>
                </div>
            </el-dialog>

        </div>

    </el-card>
</template>

<script>
import * as XLSX from 'xlsx';
import ExcelJS from 'exceljs';

export default {
    components: {
    },
    data() {
        return {
            searchData: {// 搜素框输入
                SNnumber: ''
            },
            dialogFormVisible: false,// 表格的初始可见开关变量
            excelForm: {
                excelName: '',
                headerColor: '#FFFFFF',
                bodyColor: '#FFFFFF',
                headerFontSize: 13,
                bodyFontSize: 12,
            },
            formLabelWidth: '20%',
            formRules: {
                excelName: [
                    { required: true, message: '请输入导出文件名称', trigger: 'blur' },
                ],
                headerFontSize: [
                    { required: true, message: '请输入字体大小', trigger: 'blur' },
                    { type: 'number', message: '字体大小必须为数字值' },
                    {
                        validator: (rule, value, callback) => {
                            const numberValue = parseFloat(value);
                            if (!isNaN(numberValue) && numberValue >= 5 && numberValue <= 25) {
                                callback(); // 验证通过
                            } else {
                                callback(new Error('字体大小应在5~25之间'));
                            }
                        }, trigger: 'blur',
                    },
                ],
                bodyFontSize: [
                    { required: true, message: '请输入字体大小', trigger: 'blur' },
                    { type: 'number', message: '字体大小必须为数字值' },
                    {
                        validator: (rule, value, callback) => {
                            const numberValue = parseFloat(value);
                            if (!isNaN(numberValue) && numberValue >= 5 && numberValue <= 25) {
                                callback(); // 验证通过
                            } else {
                                callback(new Error('字体大小应在5~25之间'));
                            }
                        }, trigger: 'blur',
                    },
                ],
            },
            exportMethod: 'select',
            devLoading: false,// 设备列表加载状态标志
            currentRow: null,// 当前选中的行
            devicesData: [{
                "index": 1,
                "sn_number": "SN001",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 2,
                "sn_number": "SN002",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 3,
                "sn_number": "SN003",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 4,
                "sn_number": "SN004",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 5,
                "sn_number": "SN005",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 6,
                "sn_number": "SN006",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 7,
                "sn_number": "SN007",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 8,
                "sn_number": "SN008",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 9,
                "sn_number": "SN009",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 10,
                "sn_number": "SN010",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 11,
                "sn_number": "SN011",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 12,
                "sn_number": "SN012",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 13,
                "sn_number": "SN013",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 14,
                "sn_number": "SN014",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 15,
                "sn_number": "SN015",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 16,
                "sn_number": "SN016",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 17,
                "sn_number": "SN017",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 18,
                "sn_number": "SN018",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 19,
                "sn_number": "SN019",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 20,
                "sn_number": "SN020",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            {
                "index": 21,
                "sn_number": "SN021",
                "category": "TN",
                "data": "0.11",
                "updated_at": "2023-12-02 20:49:34",
            },
            ],
            tableData: [],
            total: 0,// 当前的总条数，默认是0，获取到数据后将total重新给值为tableData的长度
            pageSizes: 10,// 一页数据的个数
            multipleSelection: [],// 存放选中的数据
        };
    },
    methods: {
        changeData(num) {
            var i = 0;
            this.tableData = [];// 清空数组
            for ((i = num * 10 - 10); i < num * 10; i++) {
                if (this.devicesData[i] === undefined) {
                    break;
                } else {
                    this.tableData[i - ((num - 1) * 10)] = this.devicesData[i];
                }
            }
            // console.log(this.tableData)
        },
        handlePage(num) {// 点击页面页码的回调函数,返回的是点击的页码数
            // console.log(num)//修改tableData显示的数据
            // this.$refs.deviceTable.doLayout();
            this.changeData(num)
        },
        handlePrevClick(num) {// 点击页面上一页的回调函数,返回的是点击的页码数，这个函数其实是不需要的
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
        exportToExcel(action) {
            if (action === 'all') {
                this.exportMethod = 'all'
                this.dialogFormVisible = true// 打开表格样式等的调整框
            } else {
                if (this.multipleSelection.length === 0) {
                    this.$message.error('没有数据被选中!');
                } else {
                    this.exportMethod = 'select'
                    this.dialogFormVisible = true// 打开表格样式等的调整框
                    // console.log(this.multipleSelection)
                }
            }
        },
        async exportData() {
            if (this.excelForm.excelName === '') {
                this.$message.error('请输入导出文件的名称!');
            } else {
                this.dialogFormVisible = false

                const workbook = new ExcelJS.Workbook();
                const worksheet = workbook.addWorksheet('Sheet1');

                // 添加表头行
                const headerRow = worksheet.addRow([
                    '序号',
                    '序列号',
                    '设备类别',
                    '检测结果(mg/L)',
                    '检测时间'
                ]);
                // console.log(this.excelForm.headerColor.slice(1))

                // 添加数据行
                if (this.exportMethod === 'all') {
                    this.devicesData.forEach(data => {
                        const rowData = Object.values(data);
                        worksheet.addRow(rowData);
                    });
                } else {
                    this.multipleSelection.forEach(data => {
                        const rowData = Object.values(data);
                        worksheet.addRow(rowData);
                    });
                }

                // 自适应列宽
                worksheet.columns.forEach((column, index) => {
                    let maxCellLength = 0;

                    // 遍历列中的每个单元格
                    column.eachCell({ includeEmpty: true }, cell => {
                        const cellLength = cell.value ? String(cell.value).length + 10 : 0;
                        maxCellLength = Math.max(maxCellLength, cellLength);
                    });

                    // 设置列宽
                    column.width = maxCellLength < 12 ? 15 : maxCellLength;

                    // 设置字体格式
                    column.font = {
                        bold: false,
                        color: { argb: '000000' }, // 字体颜色为黑色
                        size: this.excelForm.bodyFontSize,
                        underline: false, // 添加下划线
                        italic: false, // 设置为斜体
                    };

                    // 设置填充
                    column.fill = {
                        type: 'pattern',
                        pattern: 'solid',
                        fgColor: { argb: this.excelForm.bodyColor.slice(1) }
                    };

                    // 设置边框
                    column.border = {
                        top: { style: 'thin' },
                        left: { style: 'thin' },
                        bottom: { style: 'thin' },
                        right: { style: 'thin' },
                    };
                });

                // 表头样式
                headerRow.eachCell(cell => {
                    // 设置居中
                    cell.alignment = { horizontal: 'center', vertical: 'middle' };

                    // 设置字体格式
                    cell.font = {
                        bold: true,
                        color: { argb: '000000' }, // 字体颜色为黑色
                        size: this.excelForm.headerFontSize,
                        underline: false, // 添加下划线
                        italic: false, // 设置为斜体
                    };

                    // 设置填充
                    cell.fill = {
                        type: 'pattern',
                        pattern: 'solid',
                        fgColor: { argb: this.excelForm.headerColor.slice(1) }
                    };

                    // 设置边框
                    cell.border = {
                        top: { style: 'thin' },
                        left: { style: 'thin' },
                        bottom: { style: 'thin' },
                        right: { style: 'thin' },
                    };

                });

                // 生成 Blob
                const blob = await workbook.xlsx.writeBuffer();

                // 创建下载链接
                const link = document.createElement('a');
                link.href = URL.createObjectURL(new Blob([blob]));
                link.download = this.excelForm.excelName + '.xlsx';

                // 触发下载
                link.click();
            }
        },

        getColumnWidths(worksheet) {
            const range = XLSX.utils.decode_range(worksheet['!ref']);
            const colWidths = new Array(range.e.c - range.s.c + 1).fill(0);
            for (let R = range.s.r; R <= range.e.r; ++R) {
                for (let C = range.s.c; C <= range.e.c; ++C) {
                    const cellAddress = { c: C, r: R };
                    const cellRef = XLSX.utils.encode_cell(cellAddress);
                    const cell = worksheet[cellRef];

                    if (cell && cell.v != null) {
                        const cellText = XLSX.utils.format_cell(cell);

                        if (cellText.length > colWidths[C]) {
                            colWidths[C] = cellText.length;
                        }
                    }
                }
            }
            return colWidths;
        },
        handleSelectionChange(val) {
            console.log(val)
            this.multipleSelection = val;
        },
    },
    mounted() {
        this.getData();
    }
};
</script>

<style lang="less" scoped>
.el-row {
    margin-bottom: 20px;

    &:last-child {
        margin-bottom: 0;
    }
}

.dataListContainer {
    height: 60vh;
}

.infoConatiner {
    height: 5vh;
}

.el-col {
    border-radius: 4px;
}
</style>
