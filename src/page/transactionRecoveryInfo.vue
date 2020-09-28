<template>
    <div class="fillcontain">
        <headTop></headTop>
        <div class="table_container">
            <div style="margin-top: 15px;margin-bottom: 10px;display: flex;justify-content: flex-start">
                <div style="margin-left: 2%">
                    <el-input v-model="selected" placeholder="请输入应用名"></el-input>
                </div>
                <div style="margin-left: 2%">
                    <el-select v-model="transType" placeholder="事务模式">
                        <el-option
                                v-for="x in typeLists"
                                :key="x"
                                :label="x"
                                :value="x">
                        </el-option>
                    </el-select>
                </div>
                <div style="margin-left: 2%">
                    <el-select v-model="status" placeholder="事务状态">
                        <el-option
                                v-for="option in statusList"
                                :key="option.value"
                                :value="option.value"
                                :label="option.label">
                        </el-option>
                    </el-select>
                </div>
                <div style="margin-left: 2%">
                    <el-button @click="query">查询</el-button>
                </div>
                <div style="clear:both"></div>
            </div>
            <div style="margin-top: 15px;margin-bottom: 10px;display: flex;justify-content: flex-start">
                <div style="margin-left: 2%">
                    <el-date-picker v-model="createTime" type="datetime" placeholder="选择时间"
                                    value-format="yyyy-MM-dd HH:mm"
                                    format="yyyy-MM-dd HH:mm">

                    </el-date-picker>
                </div>
                <div style="margin-left: 2%">
                    <el-date-picker v-model="updateTime" type="datetime" placeholder="选择时间"
                                    value-format="yyyy-MM-dd HH:mm"
                                    format="yyyy-MM-dd HH:mm">

                    </el-date-picker>
                </div>
            </div>
            <el-table
                    :border=true
                    ref="multipleTable"
                    :data="tableData"
                    highlight-current-row
                    style="width: 100%">
                <el-table-column
                        type="expand">
                    <template slot-scope="props">
                        <el-form label-position="left" inline class="demo-table-expand">
                            <el-table
                                    :row-style="subRow"
                                    ref="subMultipleTable"
                                    :data="props.row.participantVOS"
                                    @selection-change="(v) => handleChange(v, props.row.transId)"
                                    style="width: 100%">
                                <el-table-column
                                        align="center"
                                        type="selection"
                                        width="40">
                                </el-table-column>
                                <el-table-column
                                        align="center"
                                        fixed="right"
                                        label="操作"
                                        width="100">
                                    <template slot-scope="scope">
                                        <el-button type="text" @click="editClicked(scope.row)" size="small">编辑</el-button>
                                        <el-button type="text" @click="compensationClicked(scope.row)" size="small">补偿</el-button>
                                    </template>
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        width="120"
                                        prop="appName"
                                        label="应用名">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        width="120"
                                        :show-overflow-tooltip=true
                                        prop="transId"
                                        label="全局事务ID">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        width="120"
                                        prop="participantId"
                                        label="分支事务ID">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        :show-overflow-tooltip=true
                                        width="120"
                                        prop="role"
                                        label="参与者角色">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        width="120"
                                        prop="transType"
                                        label="模式类型">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        width="120"
                                        prop="status"
                                        label="分支事务状态">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        :show-overflow-tooltip=true
                                        width="120"
                                        prop="retry"
                                        label="重试次数">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        :show-overflow-tooltip=true
                                        width="120"
                                        prop="version"
                                        label="版本">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        width="120"
                                        prop="targetClass"
                                        label="事务接口">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        width="180"
                                        prop="targetMethod"
                                        label="事务方法">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        width="240"
                                        :show-overflow-tooltip=true
                                        prop="confirmMethod"
                                        label="confirm方法">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        width="120"
                                        prop="cancelMethod"
                                        label="cancel方法">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        min-width="200"
                                        align="center"
                                        property="createTime"
                                        label="创建时间">
                                </el-table-column>
                                <el-table-column
                                        label-class-name="subTableHeaderFont"
                                        min-width="200"
                                        align="center"
                                        property="updateTime"
                                        label="最后执行时间">
                                </el-table-column>
                            </el-table>
                        </el-form>
                        <el-dialog title="改变重试次数" :visible.sync="dialogFormVisible">
                            <el-form :model="form">
                                <div style="margin-left: 0px;width: 80%">
                                    <el-form-item label="重试次数：" :label-width="formLabelWidth">
                                        <el-input v-model="form.newRetryCount" auto-complete="off"></el-input>
                                    </el-form-item>
                                </div>
                            </el-form>
                            <div slot="footer" class="dialog-footer">
                                <el-button @click="dialogFormVisible = false">取 消</el-button>
                                <el-button type="primary" @click="updateRetryCount">确 定</el-button>
                            </div>
                        </el-dialog>
                    </template>
                </el-table-column>
                <el-table-column
                        property="transId"
                        min-width="120"
                        align="center"
                        label="全局事务ID">
                </el-table-column>
                <el-table-column
                        min-width="120"
                        property="transType"
                        align="center"
                        label="模式类型">
                </el-table-column>
                <el-table-column
                        min-width="120"
                        property="appName"
                        align="center"
                        label="发起者">
                </el-table-column>
                <el-table-column
                        align="center"
                        min-width="120"
                        property="status"
                        label="事务状态">
                </el-table-column>
                <el-table-column
                        align="center"
                        min-width="120"
                        property="participationsNum"
                        label="事务分支数">
                </el-table-column>
                <el-table-column
                        min-width="200"
                        align="center"
                        property="createTime"
                        label="创建时间">
                </el-table-column>
                <el-table-column
                        min-width="200"
                        align="center"
                        property="updateTime"
                        label="最后执行时间">
                </el-table-column>
            </el-table>
            <div style="">
                <div style="margin-top: 20px; margin-left:20px;float: left">
                    <el-button type="danger" @click="deleteAll()">删除勾选数据</el-button>
                </div>
                <div class="Pagination" style="text-align: left;margin-top: 20px;float: right">
                    <el-pagination
                            @size-change="handleSizeChange"
                            @current-change="handleCurrentChange"
                            :current-page="paging.currentPage"
                            :page-sizes="[10,20,50, 100, 200]"
                            :page-size="paging.limit"
                            layout="total, sizes, prev, pager, next, jumper"
                            :total="count">
                    </el-pagination>
                </div>
            </div>
        </div>
        <!-- Form -->
        <el-dialog title="改变重试次数" :visible.sync="dialogFormVisible">
            <el-form :model="form">
                <div style="margin-left: 0px;width: 80%">
                    <el-form-item label="重试次数：" :label-width="formLabelWidth">
                        <el-input v-model="form.newRetryCount" auto-complete="off"></el-input>
                    </el-form-item>
                </div>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="updateRetryCount">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
    import headTop from '../components/headTop'
    import ElButton from "../../node_modules/element-ui/packages/button/src/button.vue";

    export default {
        data() {
            return {

                tableData: [],
                subTableData: [],
                searchValue: "",
                paging: {
                    limit: 10,
                    currentPage: 1
                },
                count: 0,
                res: null,
                options: [],
                statusList:[
                    {
                        label: '开始执行try',
                        value: 'PRE_TRY',
                    },
                    {
                        label: 'confirm阶段',
                        value: 'CONFIRMING',
                    },
                    {
                        label: 'cancel阶段',
                        value: 'CANCELING',
                    },
                    {
                        label: '删除状态',
                        value: 'DELETE',
                    }
                ],
                typeLists:["TCC","AT"],
                selected: "",
                retryCount: '',
                txGroupId: "",
                transId: "",
                transType: "",
                createTime: "",
                updateTime: "",
                status: "",
                participantId: "",
                selectedRowMap: new Map(),
                //更改重试次数
                dialogFormVisible: false,
                form: {
                    newRetryCount: null
                },
                formLabelWidth: '120px',
                currentRow: null,
                baseUrl:document.getElementById("httpPath").innerHTML
            }
        },
        components: {
            ElButton,
            headTop,
        },
        /*created: function created() {
            var _this = this;

            this.$http.get(this.baseUrl + '/application/listAppName', {}).then(function (response) {
                if (response.body.code == 200 && response.body.data != null) {
                    _this.options = response.body.data;
                    _this.selected = _this.options[0];
                } else {
                    _this.$message({
                        type: 'error',
                        message: '获取数据失败或者数据为空!'
                    });
                }
                console.log("success!");
            }, function (response) {
                _this.$message({
                    type: 'error',
                    message: response
                });
            });
        },*/
        methods: {
            editClicked(row) {
                this.dialogFormVisible = true;
                this.currentRow = row;
            },
            compensationClicked(row) {
                this.currentRow = row;
                this.$http.post(this.baseUrl + '/repository/compensationInfo', {
                    "id": this.currentRow.participantId
                }).then(
                    response => {
                        if (response.body.code == 200) {
                            const h = this.$createElement;
                            this.$msgbox({
                                title: '补偿信息',
                                message: h(
                                    'div',
                                    {
                                        style: {
                                            width: '100%',
                                            wordBreak: 'break-all',
                                        },
                                    },
                                    response.body.data,
                                ),
                            });
                        } else {
                            this.$message({
                                type: 'error',
                                message: response.body.message
                            });
                        }
                    },
                    response => {
                        this.$message({
                            type: 'error',
                            message: response
                        });
                    }
                )
            },
            handleChange(selectedRow, rowId) {
                console.log(selectedRow, rowId);
                this.selectedRowMap.set(rowId, selectedRow);
            },
            updateRetryCount: function () {
                let tData = this.tableData;
                this.$http.post(this.baseUrl + '/repository/updateHmilyParticipantRetry', {
                    "appName": this.selected,
                    "retry": this.form.newRetryCount,
                    "id": this.currentRow.participantId
                }).then(
                    response => {
                        if (response.body.code == 200) {
                            this.tableData.forEach(row => {
                                if (!(row.participantVOS == undefined)) {
                                    row.participantVOS.forEach(o => {
                                        if(o.participantId == this.currentRow.participantId) {
                                            o.retry = this.form.newRetryCount;
                                        }
                                    })
                                }
                            })
                            this.dialogFormVisible = false;
                            this.$message({
                                type: 'success',
                                message: '更新数据成功!'
                            });
                        }else {
                            this.$message({
                                type: 'error',
                                message: response.body.message
                            });
                        }
                    },
                    response => {
                        this.$message({
                            type: 'error',
                            message: response
                        });
                    }
                )
            },
            query: function query() {
                var _this3 = this;

                this.$http.post(this.baseUrl + '/repository/listPage', {
                    "pageParameter": {
                        "currentPage": this.paging.currentPage,
                        "pageSize": this.paging.limit
                    },
                    "appName": this.selected,
                    "transType": this.transType,
                    "createTime": this.createTime,
                    "updateTime": this.updateTime,
                    "status": this.status
                }).then(function (response) {
                    if (response.body.code == 200 && response.body.data != null) {
                        var rp = response.body;
                        _this3.count = rp.data.page.totalCount;
                        _this3.tableData = rp.data.dataList;
                    } else {
                        _this3.$message({
                            type: 'error',
                            message: '获取数据失败或者数据为空!'
                        });
                    }

                    console.log("success!");
                }, function (response) {
                    _this3.$message({
                        type: 'error',
                        message: response
                    });
                });
            },
            deleteAll: function () {
                var groupIds = [];
                this.tableData.forEach(row => {
                    if(!(this.selectedRowMap.get(row.transId) == undefined)){
                        this.selectedRowMap.get(row.transId).forEach(o => {
                            groupIds.push(o.participantId);
                        });
                    }
                });
                console.log(groupIds);
                this.$http.post(this.baseUrl + '/repository/batchRemoveHmilyParticipant', {
                    "ids": groupIds
                }).then(
                    response => {
                        if (response.body.code == 200) {
                            this.$message({type: 'success', message: '删除数据成功!'});
                            //delete row and update tableData but don't send post request to update all data
                            this.filterData(groupIds);
                            this.selectedRowMap = new Map();
                        } else {
                            this.$message({
                                type: 'error',
                                message: '删除数据失败!'
                            });
                        }
                    },
                    response => {
                        this.$message({
                            type: 'error',
                            message: response
                        });
                    }
                )

            },
            // 过滤删除的数据
            filterData(groupId) {
                this.tableData.forEach(row => {
                    if (row.participantVOS) {
                        const subTable = row.participantVOS;
                        row.participantVOS = subTable.filter(sub => !groupId.includes(sub.participantId));
                    }
                })
            },
            subRow: function subRow() {
                return { "font-size": "0.85em" };
            },
            subTableHeader: function subTableHeader() {
                return { "background-color": "red", "font-size": "0.6em" };
            },
            handleSizeChange: function handleSizeChange(val) {
                this.paging.limit = val;
            },
            handleCurrentChange: function handleCurrentChange(val) {
                this.paging.currentPage = val;
            }
        },
        watch: {
            paging: {
                handler: function handler() {
                    var _this5 = this;

                    this.$http.post(this.baseUrl + '/repository/listPage', {
                        "pageParameter": {
                            "currentPage": this.paging.currentPage,
                            "pageSize": this.paging.limit
                        },
                        "appName": this.selected,
                        "retry": this.retryCount,
                        "transType": this.transType,
                        "createTime": this.createTime,
                        "updateTime": this.updateTime,
                        "status": this.status
                    }).then(function (response) {
                        if (response.body.code == 200) {
                            var rp = response.body;
                            _this5.count = rp.data.page.totalCount;
                            _this5.tableData = rp.data.dataList;
                        } else {
                            _this5.$message({
                                type: 'error',
                                message: '获取数据失败!'
                            });
                        }

                        console.log("success!");
                    }, function (response) {
                        _this5.$message({
                            type: 'error',
                            message: response
                        });
                    });
                },
                deep: true
            }

        }
    }
</script>

<style lang="less">
    @import '../style/mixin';

    .table_container {
        padding: 0px;
    }

    .subTableHeaderFont {
        font-size: 0.9em;
        padding: 0px;
        text-align: center;
        color: rebeccapurple !important;
        background-color: white !important;
    }

    .el-input {

    }

    .el-table__expanded-cell {
        padding: 5px !important;
    }
</style>
