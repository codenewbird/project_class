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
                border
                :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)"
                style="width: 100%"
                @selection-change="handleSelectionChange">
                <el-table-column
                type="selection"
                width="55">
                </el-table-column>
                <el-table-column
                label="序号"
                property="id"
                width="120">
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
                <template slot-scope="scope">
                    <el-button icon="el-icon-shopping-bag-2" @click="viewDetail(scope.$index)">
                    详情
                    </el-button>
                </template>

                
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
            <el-form v-model="newForm" label-width="100px" style="width: auto; padding: 10px 20px;"> 
                <el-row>
                    <el-col :span="12">
                        <el-form-item label="涉及企业名称">
                            <el-input :readonly="isView" v-model="newForm.company" style="width: 200px"/>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="涉及企业层级">
                            <el-select :readonly="isView" v-model="newForm.level" placeholder="Select" style="width: 200px">
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
                <el-row>
                    <el-col :span="12">
                        <el-form-item label="填报人">
                            <el-input :readonly="isView" v-model="newForm.informant" style="width: 200px;"/>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="填报时间">
                            <el-input :readonly="isView" v-model="newForm.fillingTime" style="width: 200px;"/>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row>
                    <el-col :span="12">
                        <el-form-item label="风险事件名称">
                            <el-input :readonly="isView" v-model="newForm.riskEvent" style="width: 200px;"/>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="事件发生时间">
                            <el-input :readonly="isView" v-model="newForm.startTimeOfRiskEvent" style="width: 200px;"/>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row>
                    <el-col :span="12">
                        <el-form-item label="是否境外">
                            <el-radio-group :readonly="isView" v-model="newForm.beyondTheBorders">
                                <el-radio label="境内" value="0"></el-radio>
                                <el-radio label="境外" value="1"></el-radio>
                            </el-radio-group>
                        </el-form-item>
                    </el-col>
                    <el-col :span="12">
                        <el-form-item label="是否涉诉">
                            <el-radio-group :readonly="isView" v-model="newForm.involvedInAlawsuit">
                                <el-radio label="涉诉" value="0"></el-radio>
                                <el-radio label="不涉诉" value="1"></el-radio>
                            </el-radio-group>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row>
                    <el-col>
                        <el-form-item label="风险类别">
                            <el-select :readonly="isView" v-model="newForm.level" placeholder="Select" style="width: 200px">
                                <el-option
                                v-for="item in status"
                                :key="item.value"
                                :label="item.label"
                                :value="item.value">
                                </el-option>
                            </el-select>
                            <el-select :readonly="isView" v-model="newForm.level" placeholder="Select" style="width: 200px">
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
                <el-form-item label="损失（风险） 金额（万元）" style="align-self: center;">
                    <el-input :readonly="isView" v-model="newForm.lossAmount"></el-input>
                </el-form-item>
                <el-form-item label="处置进展情况">
                    <el-input :readonly="isView" v-model="newForm.handleProgress" type="textarea" maxlength="2"></el-input>
                </el-form-item>
                <el-form-item label="当前情况描述">
                    <el-input :readonly="isView" v-model="newForm.describe" type="textarea" maxlength="2"></el-input>
                </el-form-item>
            </el-form>

            <span style="width: 200px; margin-left: 0px; text-align: left; background-color: aqua;">重大变化/重大进展续报</span>
            
            <el-table
                border
                style="width: 100%">
                <el-table-column
                prop="date"
                label="序号">
                </el-table-column>
                <el-table-column
                prop="name"
                label="涉及企业名称(信用代码)">
                </el-table-column>
                <el-table-column
                prop="name"
                label="涉及企业层级">
                </el-table-column>
                <el-table-column
                prop="name"
                label="风险事件名称">
                </el-table-column>
                <el-table-column
                prop="name"
                label="风险类别">
                </el-table-column>
                <el-table-column
                prop="name"
                label="事件发生时间">
                </el-table-column>
                <el-table-column
                prop="name"
                label="当前情况描述">
                </el-table-column>
                <el-table-column
                prop="name"
                label="损失（风险） 金额（万元）">
                </el-table-column>
                <el-table-column
                prop="name"
                label="处置进展情况">
                </el-table-column>
                <el-table-column
                prop="name"
                label="当前情况描述">
                </el-table-column>
                <el-table-column
                prop="name"
                label="续报时间">
                </el-table-column><el-table-column
                prop="name"
                label="附件">
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

            <span align='left' style="display: block; width: 100;">专项整改报告</span>

            <el-form>
                <el-form-item label="基本情况"><el-input :readonly="isView" v-model="newForm.baseCondition" type="textarea" maxlength="2"></el-input></el-form-item>
                <el-form-item label="原因分析"><el-input :readonly="isView" v-model="newForm.analysis" type="textarea" maxlength="2"></el-input></el-form-item>
                <el-form-item label="处理结果"><el-input :readonly="isView" v-model="newForm.addressResult" type="textarea" maxlength="2"></el-input></el-form-item>
                <el-form-item label="其他需要报告的情况">
                    <el-upload
                        :readonly="isView" 
                        class="upload-demo"
                        drag
                        action=""
                        :on-preview="handlePreview"
                        :on-remove="handleRemove"
                        :file-list="fileList"
                        multiple>
                        <i class="el-icon-upload"></i>
                        <div class="el-upload__text">Drop file here or <em>click to upload</em></div>
                        <div class="el-upload__tip" slot="tip">只能上传word/excel/图片/PDF文件，且不超过10MB</div>
                        </el-upload>
                </el-form-item>
            </el-form>

            <span slot="footer" class="dialog-footer">
                <el-button @click="newDialogVisible = false" v-if="!isView">Cancel</el-button>
                <el-button type="primary" @click="newDialogVisible = false" v-if="!isView">Confirm</el-button>
            </span>
        </el-dialog>

    </div>
</template>


<script> 
export default {
    data() {
        return{
            newDialogVisible: false,
            isView: true, //是否只是查看
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
            fileList: [],
            //表格数据
            tableData: [
                {
                    id: 3,
                    company: 'G',
                    auditStatus: 'GG',
                    submitStatus: 'GG',
                    level: 1,
                    informant: '',
                    fillingTime: '',
                    riskEvent: 'GG',
                    startTimeOfRiskEvent: '',
                    beyondTheBorders: '',
                    involvedInAlawsuit: '',
                    riskType: '',
                    lossAmount: '',
                    handleProgress: '',
                    describe: 'GG',
                    baseCondition: '',
                    analysis: '',
                    addressResult: '',
                    fileList: ''
                },
                {
                    id: 3,
                    company: 'G',
                    auditStatus: 'GG',
                    submitStatus: 'GG',
                    level: 1,
                    informant: '',
                    fillingTime: '',
                    riskEvent: 'GG',
                    startTimeOfRiskEvent: '',
                    beyondTheBorders: '',
                    involvedInAlawsuit: '',
                    riskType: '',
                    lossAmount: '',
                    handleProgress: 'GG',
                    describe: 'GG',
                    baseCondition: '',
                    analysis: '',
                    addressResult: '',
                    fileList: ''
                },
                {
                    id: 3,
                    company: 'G',
                    auditStatus: 'GG',
                    submitStatus: 'GG',
                    level: 1,
                    informant: '',
                    fillingTime: '',
                    riskEvent: 'GG',
                    startTimeOfRiskEvent: '',
                    beyondTheBorders: '',
                    involvedInAlawsuit: '',
                    riskType: '',
                    lossAmount: '',
                    handleProgress: '',
                    describe: '',
                    baseCondition: '',
                    analysis: '',
                    addressResult: '',
                    fileList: ''
                },
                // {
                //     number,
                //     auditStatus,
                //     submitStatus,
                //     company,
                //     riskEvent,
                //     StartTimeOfRiskEvent,
                //     describe,informant
                // }
            ],
            newForm: {
                id: 3,
                company: 'G',
                auditStatus: 'GG',
                submitStatus: 'GG',
                level: 1,
                informant: '',
                fillingTime: '',
                riskEvent: 'GG',
                startTimeOfRiskEvent: '',
                beyondTheBorders: '',
                involvedInAlawsuit: '',
                riskType: '',
                lossAmount: '',
                handleProgress: '',
                describe: '',
                baseCondition: '',
                analysis: '',
                addressResult: '',
                fileList: ''
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
        },
        handlePreview(){

        },
        handleRemove(){},
        viewDetail(index) {
            this.newForm = this.tableData[index]
            this.newDialogVisible = true
            this.isView = true
        },
        handleSelectionChange(){},
        handleClose(){
            this.newDialogVisible = false
            this.isView = false
        },
    }

}
</script>