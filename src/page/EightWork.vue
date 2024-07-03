<template>
    <div>
        <div>
            <el-row>
                <el-col :span="2">
                    <el-select v-model="selected_status" placeholder="Select">
                        <el-option
                        v-for="item in status"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                        </el-option>
                    </el-select>
                </el-col>
                <el-col :span="6">
                    <el-input placeholder="事件名称"></el-input>
                </el-col>

                <el-col :span="6">
                    <el-input placeholder="输入职能部门或机构名称搜索"></el-input>
                </el-col>

                <el-col :span="1">
                    <el-button type="success" icon="el-icon-search">搜索</el-button>
                </el-col>
            </el-row>

        </div>
        <div>
            <el-button @click="newDialogVisible = true">新增</el-button>
            <el-button>修改</el-button>
            <el-button>删除</el-button>
            <el-button>提交</el-button>
            <el-button>撤回</el-button>
        </div>
        <div>
            <el-table
                ref="multipleTable"
                border=true
                :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)"
                style="width: 100%"
                @selection-change="handleSelectionChange">
                <el-table-column
                type="selection"
                width="55">
                </el-table-column>
                <el-table-column
                label="序号"
                property="number"
                width="120">
                <!-- <template slot-scope="scope">{{ scope.row.date }}</template> -->
                </el-table-column>
                <el-table-column
                property="auditStatus"
                label="审核状态"
                width="120">
                </el-table-column>
                <el-table-column
                property="submitStatus"
                label="上报状态"
                width="120">
                </el-table-column>
                <el-table-column
                property="company"
                label="涉及企业名称(信用代码)"
                show-overflow-tooltip>
                </el-table-column>
                <el-table-column
                property="riskEvent"
                label="风险事件名称"
                show-overflow-tooltip>
                </el-table-column>
                <el-table-column
                property="startTimeOfRiskEvent"
                label="风险发生时间"
                show-overflow-tooltip>
                </el-table-column>
                <el-table-column
                property="describe"
                label="情况描述"
                show-overflow-tooltip>
                </el-table-column>
                <el-table-column
                property="operation"
                label="操作"
                show-overflow-tooltip>
                <el-button icon="el-icon-shopping-bag-2">
                    详情
                </el-button>
                </el-table-column>
            </el-table>
            <el-pagination align='left' 
                @size-change="handleSizeChange" 
                @current-change="handleCurrentChange"
                :current-page="currentPage" 
                :page-sizes="[1,5,10,20]" 
                :page-size="pageSize" 
                layout="total, sizes, prev, pager, next, jumper" 
                :total="tableData.length">
            </el-pagination>
            
        </div>
        <el-dialog
            title="专项整改报告"
            :visible.sync="newDialogVisible"
            width="70%"
            :before-close="handleClose">
            <el-form :model="newForm">
                    <el-row>
                        <el-col :span="8">
                            <el-form-item label="涉及企业名称">
                                <el-input :model="newForm.company" style="width: 400px"/>
                            </el-form-item>
                        </el-col>
                        <el-col :span="12">
                            <el-form-item label="涉及企业层级">
                                <el-select v-model="newForm.level" placeholder="Select">
                                    <el-option
                                    v-for="item in status"
                                    :key="item.value"
                                    :label="item.label"
                                    :value="item.value">
                                    </el-option>
                                </el-select>
                            </el-form-item>
                        </el-col>
                    </el-row>
            </el-form>
            
            <span slot="footer" class="dialog-footer">
                <el-button @click="newDialogVisible = false">Cancel</el-button>
                <el-button type="primary" @click="newDialogVisible = false">Confirm</el-button>
            </span>
        </el-dialog>

    </div>
</template>


<script> 
export default {
    data() {
        return{
            newDialogVisible: false,
            selected_status: {
                label: '状态',
                value: -1
            },
            status: [
                {
                    label: "待上报",
                    value: 0
                },
                {
                    label: "已上报",
                    value: 1
                }
            ],
            //表格分页参数
            pageSize: 10,
            currentPage: 1,
            //表格数据
            tableData: [
                {
                    number: 1,
                    auditStatus: 'GG',
                    submitStatus: 'GG',
                    company: 'GG',
                    riskEvent: 'GG',
                    startTimeOfRiskEvent: 'GG',
                    describe: 'GG',
                },
                {
                    number: 1,
                    auditStatus: 'GG',
                    submitStatus: 'GG',
                    company: 'GG',
                    riskEvent: 'GG',
                    startTimeOfRiskEvent: 'GG',
                    describe: 'GG',
                },
                // {
                //     number,
                //     auditStatus,
                //     submitStatus,
                //     company,
                //     riskEvent,
                //     StartTimeOfRiskEvent,
                //     describe,
                // }
            ],
            newForm: {
                company: '',
                level: 1
            },
        }
    },
    methods: {
        //每页条数改变时触发 选择一页显示多少行
        handleSizeChange(val) {
            console.log(`每页 ${val} 条`);
            this.currentPage = 1;
            this.pageSize = val;
        },
        //当前页改变时触发 跳转其他页
        handleCurrentChange(val) {
            console.log(`当前页: ${val}`);
            this.currentPage = val;
        }
    }

}
</script>