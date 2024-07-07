<template>
    <div>
        <div>
            <el-row><!--TODO: model没有绑定-->
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
        <div><!--TODO: 样式没改-->
            <el-button @click="newDialogVisible = true">新增</el-button>
            <el-button @click="update">修改</el-button>
            <el-button @click="remove">删除</el-button>
            <el-button @click="submit">提交</el-button>
            <el-button @click="backup">撤回</el-button>
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
                label="审核状态"
                width="120">
                <template slot-scope="scope">
                <span v-if="scope.row.auditStatus == 1">已审核</span>
                <span v-if="scope.row.auditStatus == 0">未审核</span>
                </template>
                </el-table-column>
                <el-table-column
                label="上报状态"
                width="120">
                <template slot-scope="scope">
                <span v-if="scope.row.submitStatus == 1">已上报</span>
                <span v-if="scope.row.submitStatus == 0">未上报</span>
                </template>
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
                                v-for="item in levelOptions"
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
                            <el-date-picker
                            style="width: 200px;"
                            v-model="newForm.fillingTime"
                            type="datetime"
                            placeholder="选择日期时间"
                            default-time="12:00:00">
                            </el-date-picker>
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
                            <el-date-picker
                            style="width: 200px;"
                            v-model="newForm.fillingTime"
                            type="datetime"
                            placeholder="选择日期时间"
                            default-time="12:00:00">
                            </el-date-picker>
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
                        <el-form-item label="风险类别"> <!--TODO: 没写了-->
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
                    <el-input :readonly="isView" v-model="newForm.handleProcess" type="textarea" maxlength="2"></el-input>
                </el-form-item>
                <el-form-item label="当前情况描述">
                    <el-input :readonly="isView" v-model="newForm.description" type="textarea" maxlength="2"></el-input>
                </el-form-item>
            </el-form>

            <span style="width: 200px; margin-left: 0px; text-align: left; background-color: aqua;">重大变化/重大进展续报</span>
            
            <el-table
                border
                :data="changeHistory.slice((currentPage-1)*pageSize,currentPage*pageSize)"
                style="width: 100%">
                <el-table-column
                prop="id"
                label="序号">
                </el-table-column>
                <el-table-column
                prop="company"
                label="涉及企业名称(信用代码)">
                </el-table-column>
                <el-table-column
                prop="level"
                label="涉及企业层级">
                </el-table-column>
                <el-table-column
                prop="riskEvent"
                label="风险事件名称">
                </el-table-column>
                <el-table-column
                prop="riskType"
                label="风险类别">
                </el-table-column>
                <el-table-column
                prop="startTimeOfRiskEvent"
                label="事件发生时间">
                </el-table-column>
                <el-table-column
                prop="description"
                label="当前情况描述">
                </el-table-column>
                <el-table-column
                prop="lossAmount"
                label="损失（风险） 金额（万元）">
                </el-table-column>
                <el-table-column
                prop="handleProcess"
                label="处置进展情况">
                </el-table-column>
                <el-table-column
                prop="changeTime"
                label="续报时间">
                </el-table-column><el-table-column
                prop="fieldList"
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
                :total="changeHistory.length">
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
            levelOptions: [
            {
                    label: "一级",
                    value: 1
                },
                {
                    label: "二级",
                    value: 2
                }
            ],
            //表格分页参数
            pageSize: 10,
            currentPage: 1,
            fileList: [],
            selected_id: [],
            selected_cnt: 0,
            selected_row: [],
            auditStatusOption: [
                {
                    label: "已审核",
                    value: 1
                },
                {
                    label: "未审核",
                    value:  0
                }
            ],
            //表格数据
            tableData: [
                {
                    id: 1,
                    company: 'G',
                    auditStatus: 1,
                    submitStatus: 0,
                    level: 1,
                    informant: '',
                    fillingTime: '',
                    riskEvent: 'GG',
                    startTimeOfRiskEvent: '',
                    beyondTheBorders: '',
                    involvedInAlawsuit: '',
                    riskType: '',
                    lossAmount: '',
                    handleProcess: '',
                    description: 'GG',
                    baseCondition: '',
                    analysis: '',
                    addressResult: '',
                    fileList: ''
                },
                {
                    id: 2,
                    company: 'G',
                    auditStatus: 0,
                    submitStatus: 0,
                    level: 1,
                    informant: '',
                    fillingTime: '',
                    riskEvent: 'GG',
                    startTimeOfRiskEvent: '',
                    beyondTheBorders: '',
                    involvedInAlawsuit: '',
                    riskType: '',
                    lossAmount: '',
                    handleProcess: 'GG',
                    description: 'GG',
                    baseCondition: '',
                    analysis: '',
                    addressResult: '',
                    fileList: ''
                },
                {
                    id: 3,
                    company: 'G',
                    auditStatus: 0,
                    submitStatus: 1,
                    level: 1,
                    informant: '',
                    fillingTime: '',
                    riskEvent: 'GG',
                    startTimeOfRiskEvent: '',
                    beyondTheBorders: '',
                    involvedInAlawsuit: '',
                    riskType: '',
                    lossAmount: '',
                    handleProcess: '',
                    description: '',
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
                handleProcess: '',
                description: '',
                baseCondition: '',
                analysis: '',
                addressResult: '',
                fileList: '',
            },
            changeHistory: [
                {
                    id: 3,
                    company: 'G',
                    level: 1,
                    riskEvent: 'GG',
                    startTimeOfRiskEvent: '',
                    riskType: '',
                    lossAmount: '',
                    handleProcess: '',
                    description: '',
                    changeTime: '',
                    fileList: '',
                },
            ]
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
        handleSelectionChange(selection) {
            this.selected_row = selection.map((item) => this.tableData.indexOf(item));
            this.selected_id = selection.map((item) => item.id)
            this.selected_cnt = this.selected_row.length
        },

        handleClose(){
            this.newDialogVisible = false
            this.isView = false
        },

        update() {
            if (this.selected_cnt == 0) {
                return
            }
            if (this.selected_cnt > 1) {
                alert("只允许选中修改一项修改")
                return
            }
            this.newForm = this.tableData[this.selected_row[0]]
            this.newDialogVisible = true
        },
        remove() {
            if (this.selected_cnt == 0) return
            console.log(this.selected_id)
            alert("没有mock了，自己删")
        },
        submit(){
            this.selected_row.forEach((index) => {
                this.tableData[index].submitStatus = 1
            })
            // 调用修改
        },
        backup(){
            this.selected_row.forEach((index) => {
                this.tableData[index].submitStatus = 0
            })
            // 调用修改
        },
    }

}
</script>