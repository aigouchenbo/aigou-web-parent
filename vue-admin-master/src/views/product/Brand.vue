<template>
    <section>
        <!--工具条-->
        <el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
            <el-form :inline="true" :model="filters">
                <el-form-item>
                    <el-input v-model="filters.keyword" placeholder="关键字"></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" v-on:click="getBrands">查询</el-button>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="handleAdd">新增</el-button>
                </el-form-item>
            </el-form>
        </el-col>

        <!--列表-->
        <el-table :data="brands" highlight-current-row v-loading="listLoading" @selection-change="selsChange"
                  style="width: 100%;">
            <el-table-column type="selection" width="55">
            </el-table-column>
            <el-table-column type="index" width="60">
            </el-table-column>
            <el-table-column prop="name" label="名称" sortable>
            </el-table-column>
            <el-table-column prop="englishName" label="英文名" sortable>
            </el-table-column>
            <el-table-column prop="firstLetter" label="首字母" sortable>
            </el-table-column>
            <el-table-column prop="sortIndex" label="序号" sortable>
            </el-table-column>
            <el-table-column prop="logo" label="品牌logo" sortable>
            </el-table-column>
            <el-table-column prop="productType.name" label="商品类型" sortable>
            </el-table-column>
            <el-table-column prop="description" label="描述" sortable>
            </el-table-column>
            <el-table-column label="操作" width="150">
                <template scope="scope">
                    <el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                    <el-button type="danger" size="small" @click="handleDel(scope.$index, scope.row)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>

        <!--工具条-->
        <el-col :span="24" class="toolbar">
            <el-button type="danger" @click="batchRemove" :disabled="this.sels.length===0">批量删除</el-button>
            <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="size"
                           :total="total" style="float:right;">
            </el-pagination>
        </el-col>

        <!--编辑界面-->
        <el-dialog title="编辑" :visible.sync="editFormVisible" :close-on-click-modal="false">
            <el-form :model="editForm" label-width="80px" :rules="editFormRules" ref="editForm">
                <el-form-item label="名称" prop="name">
                    <el-input v-model="editForm.name" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="英文名" prop="englishName">
                    <el-input v-model="editForm.englishName" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="序号" prop="sortIndex">
                    <el-input v-model="editForm.sortIndex" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="logo">
                    <el-upload
                            class="upload-demo"
                            action="http://localhost:9527/services/common/file/upload"
                            :before-remove="handleLogoRemove"
                            :file-list="logoList"
                            list-type="picture"
                            :limit="1"
                            :on-success="handleUploadSeccess"
                            :on-exceed="handleOutOfRange">
                        <el-button size="small" type="primary">点击上传</el-button>
                    </el-upload>
                </el-form-item>
                <el-form-item label="商品类型" prop="productTypeId">
                    <el-cascader style="width:100%"
                                 :options="productTypes"
                                 v-model="editForm.productTypeId"
                                 :props="productProps">
                    </el-cascader>
                </el-form-item>
                <el-form-item label="描述" prop="description">
                    <el-input v-model="editForm.description" type="textarea" auto-complete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click.native="editFormVisible = false">取消</el-button>
                <el-button type="primary" @click.native="editSubmit" :loading="editLoading">提交</el-button>
            </div>
        </el-dialog>

        <!--新增界面-->
        <el-dialog title="新增" :visible.sync="addFormVisible" :close-on-click-modal="false">
            <el-form :model="addForm" label-width="80px" :rules="addFormRules" ref="addForm">
                <el-form-item label="名称" prop="name">
                    <el-input v-model="addForm.name" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="英文名" prop="englishName">
                    <el-input v-model="addForm.englishName" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="序号" prop="sortIndex">
                    <el-input v-model="addForm.sortIndex" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="logo">
                    <el-upload
                            class="upload-demo"
                            action="http://localhost:9527/services/common/file/upload"
                            :before-remove="handleLogoRemove"
                            :file-list="logoList"
                            list-type="picture"
                            :limit="1"
                            :on-success="handleUploadSeccess"
                            :on-exceed="handleOutOfRange">
                        <el-button size="small" type="primary">点击上传</el-button>
                    </el-upload>
                </el-form-item>
                <el-form-item label="商品类型" prop="productTypeId">
                    <el-cascader style="width:100%"
                                 :options="productTypes"
                                 v-model="addForm.productTypeId"
                                 :props="productProps">
                    </el-cascader>
                </el-form-item>
                <el-form-item label="描述" prop="description">
                    <el-input v-model="addForm.description" type="textarea" auto-complete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click.native="addFormVisible = false">取消</el-button>
                <el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
            </div>
        </el-dialog>
    </section>
</template>

<script>
    export default {
        data() {
            return {
                //商品类型
                productTypes: [],
                //级联选择器的属性配置
                productProps: {
                    label: "name",
                    value: "id"
                },
                //logo
                logoList: [],
                filters: {
                    keyword: ''
                },
                brands: [],
                total: 0,
                page: 1,
                size: 10,
                listLoading: false,
                sels: [],//列表选中列

                editFormVisible: false,//编辑界面是否显示
                editLoading: false,
                editFormRules: {
                    name: [
                        {required: true, message: '请输入姓名', trigger: 'blur'}
                    ]
                },
                //编辑界面数据
                editForm: {
                    name: '',
                    englishName: '',
                    sortIndex: null,
                    productTypeId: null,
                    description: '',
                    logo:null
                },

                addFormVisible: false,//新增界面是否显示
                addLoading: false,
                addFormRules: {
                    name: [
                        {required: true, message: '请输入姓名', trigger: 'blur'}
                    ],
                    englishName: [
                        {required: true, message: '请输入英文名', trigger: 'blur'}
                    ],
                    sortIndex: [
                        {required: true, message: '请输入序号', trigger: 'blur'}
                    ],
                    productTypeId: [
                        {required: true, message: '请选择商品类型', trigger: 'blur'}
                    ]

                },
                //新增界面数据
                addForm: {
                    name: '',
                    englishName: '',
                    sortIndex: null,
                    productTypeId: null,
                    description: '',
                    logo:null
                }

            }
        },
        methods: {
            //文件上传成功钩子函数
            handleUploadSeccess(response){
                this.addForm.logo = response.data;
            },
            //文件超出个数
            handleOutOfRange(){
                this.$message({
                    message: '只能上传一张图片',
                    type: 'warning'
                });
            },
            //删除文件
            handleLogoRemove(file, fileList){
                console.log("file",file);//删除的回掉
                this.$http.get("/common/file/delete",{
                    params:{
                        fileId:file.response.data
                    }
                }).then(res=>{
                    let data = res.data;
                    if(data.success){
                        this.$message({
                            message: '删除成功',
                            type: 'success'
                        });
                    }else{
                        this.$message({
                            message: '删除失败',
                            type: 'error'
                        });
                        return false;//阻止删除
                    }
                })
            },
            //性别显示转换
            formatSex: function (row, column) {
                return row.sex == 1 ? '男' : row.sex == 0 ? '女' : '未知';
            },
            handleCurrentChange(val) {
                this.page = val;
                this.getBrands();
            },
            //获取商品品牌列表
            getBrands() {
                this.listLoading = true;
                //NProgress.start();

                this.$http.post("/product/brand/page", {
                    page: this.page,
                    size: this.size,
                    keyword: this.filters.keyword
                }).then((res) => {
                    this.listLoading = false;
                    let data = res.data;//PageList

                    this.brands = data.rows;
                    this.total = data.total;
                })

                /*
                getUserListPage(para).then((res) => {
                    this.total = res.data.total;
                    this.users = res.data.users;
                    this.listLoading = false;
                    //NProgress.done();
                });*/
            },
            //加载商品类型
            getProductTypes() {
                this.$http.get("/product/productType/tree").then((res) => {
                    this.productTypes = this.getTreeData(res.data);
                })
            },
            getTreeData(data) {
                // 循环遍历json数据
                for (var i = 0; i < data.length; i++) {

                    if (data[i].children.length < 1) {
                        // children若为空数组，则将children设为undefined
                        data[i].children = undefined;
                    } else {
                        // children若不为空数组，则继续 递归调用 本方法
                        this.getTreeData(data[i].children);
                    }
                }
                return data;
            },
            //删除
            handleDel: function (index, row) {
                this.$confirm('确认删除该记录吗?', '提示', {
                    type: 'warning'
                }).then(() => {
                    this.listLoading = true;
                    this.$http.delete("/product/brandDL/"+row.id).then((res) => {
                        let data = res.data;
                        if(data.success){
                            this.$message({
                                message: '删除成功',
                                type: 'success'
                            });
                            this.getBrands();
                        }else{
                            this.$message({
                                message: '删除失败',
                                type: 'error'
                            });
                        }
                    })
                }).catch(() => {

                });
            },
            //显示编辑界面
            handleEdit: function (index, row) {
                this.editFormVisible = true;
                this.$http.get("/product/brand/"+row.id).then((res) => {
                    let data = res.data;
                    this.editForm={
                        name: data.name,
                        englishName: data.englishName,
                        sortIndex: data.sortIndex,
                        productTypeId: null,
                        description: data.description,
                        logo:data.logo
                    }
                })
                // this.editForm = Object.assign({}, row);
            },
            //显示新增界面
            handleAdd: function () {
                this.addFormVisible = true;
                this.addForm= {
                    name: '',
                    englishName: '',
                    sortIndex: null,
                    productTypeId: null,
                    description: '',
                    logo:null
                }
            },
            //编辑
            editSubmit: function () {
                this.$refs.editForm.validate((valid) => {
                    if (valid) {
                        this.$confirm('确认提交吗？', '提示', {}).then(() => {
                            this.editLoading = true;
                            //NProgress.start();
                            let para = Object.assign({}, this.editForm);
                            para.birth = (!para.birth || para.birth == '') ? '' : util.formatDate.format(new Date(para.birth), 'yyyy-MM-dd');
                            editUser(para).then((res) => {
                                this.editLoading = false;
                                //NProgress.done();
                                this.$message({
                                    message: '提交成功',
                                    type: 'success'
                                });
                                this.$refs['editForm'].resetFields();
                                this.editFormVisible = false;
                                this.getBrands();
                            });
                        });
                    }
                });
            },
            //新增
            addSubmit: function () {
                //this.$refs.addForm 获取到组件对象
                this.$refs.addForm.validate((valid) => {
                    if (valid) {
                        this.$confirm('确认提交吗？', '提示', {}).then(() => {
                            this.addLoading = true;
                            //NProgress.start();
                            //格式化部分参数  productTypeId=[1,2,3]  ===> productTypeId=1
                            let para = Object.assign({}, this.addForm);
                            para.productTypeId = para.productTypeId[para.productTypeId.length - 1];
                            //发送请求
                            this.$http.post("/product/brand", para).then((res) => {
                                this.addLoading = false;
                                //NProgress.done();
                                let data = res.data;
                                if (data.success) {
                                    this.$message({
                                        message: '提交成功',
                                        type: 'success'
                                    });
                                    //重置表单
                                    this.$refs['addForm'].resetFields();
                                    //关闭模态框
                                    this.addFormVisible = false;
                                    //重新加载table
                                    this.getBrands();
                                } else {
                                    this.$message({
                                        message: data.message,
                                        type: 'error'
                                    });
                                }
                            })
                        });
                    }
                });
            },
            selsChange: function (sels) {
                this.sels = sels;
            },
            //批量删除
            batchRemove: function () {
                var ids = [];
                for(var i = 0; i < this.sels.length; i++){
                    ids.push(this.sels[i].id);
                }
                console.log(ids);
                var idsStr = ids.join();
                console.log(idsStr);
                this.$confirm('确认删除选中记录吗？', '提示', {
                    type: 'warning'
                }).then(() => {
                    this.listLoading = true;
                    this.$http.get("/product/brand/batchDelete",{
                        params:{
                            idsStr:idsStr
                        }
                    }).then((result) => {
                        //成功后的回调
                        let data = result.data;
                        if (data.success) { //删除成功
                            this.$message({
                                showClose: true,
                                type: 'success',
                                message: '删除成功!'
                            });
                            //重新加载员工
                            this.getProductTypes();
                            this.getBrands();
                        } else {
                            this.$message({
                                showClose: true,
                                type: 'error',
                                message: data.message
                            });
                        }
                    });
                }).catch(() => {

                });
            }
        },
        //$(function(){})
        mounted() {
            this.getBrands();
            this.getProductTypes();
        }
    }

</script>

<style scoped>

</style>