<template>
<!-- 员工管理 -->
    <div>
        <!--按钮-->
        <div>
            <el-button round size="small" type="primary" plain @click="toAddHandler">添加</el-button>
            <el-button round type="danger" plain size="small">批量删除</el-button>
        </div>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="employee">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="username" label="用户名"></el-table-column>
            <el-table-column fixed="left" prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column width="180" prop="telephone" label="手机号"></el-table-column>
            <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
            <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- /分页 -->
        <!-- 模态框 -->
        <el-dialog 
            :title="title"
            :visible.sync="visible"
            width="30%">
            测试：{{form}}
            <el-form label-width="80px">
                <el-form-item :model="form" label="用户名">
                    <el-input v-model="form.username"/>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input type="password" v-model="form.password"/>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="form.realname"/>
                </el-form-item>
                <el-form-item label="性别">
                    <el-radio-group v-model="form.gender">
                        <el-radio label="男">男</el-radio>
                        <el-radio label="女">女</el-radio>
                    </el-radio-group>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"/>
                </el-form-item>
                <el-form-item label="身份证号">
                    <el-input v-model="form.idCard"/>
                </el-form-item>
                <el-form-item label="银行卡号">
                    <el-input v-model="form.bankCard"/>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!-- /模态框 -->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employee:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadDate();
    },
    methods:{
        //提交
        submitHandler(){
            let url = "http://localhost:6677/waiter/saveOrUpdate";
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"post",
                //告诉后台我的请求体中存放的是查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据，将this.form转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModalHandler();
                this.loadDate();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        //重载员工数据
        loadDate(){
            //this -> vue实例，通过vue实例访问该实例中的数据
            //this.title/this.toAddHandler
            let url = "http://localhost:6677/waiter/findAll"
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.employee = response.data;
            })
        },
        toAddHandler(){
            this.title = "录入员工信息";
            this.visible = true;
        },
        toDeleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.$message({
                    type: 'success',
                    message: '删除成功!'+id
                });
            })
        },
        toUpdateHandler(){
            this.title = "修改员工信息";
            this.visible = true;
        },
        closeModalHandler(){
            this.visible = false;
        }
    },
}
</script>

<style scoped>

</style>