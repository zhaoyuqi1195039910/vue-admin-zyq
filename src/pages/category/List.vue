<template>
    <div>
        <h1>栏目管理</h1>
        <!--按钮-->
        <div>
            <el-button round size="small" type="primary" plain @click="toAddHandler">添加</el-button>
            <el-button round type="danger" plain size="small">批量删除</el-button>
        </div>
        <!-- /按钮结束 -->
        <!-- 表格 -->
        <el-table :data="category">
            <el-table-column width="50" prop="all" label="全选"><el-checkbox v-model="checked"></el-checkbox></el-table-column>
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num" label="序号"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格结束 -->
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
                <el-form-item :model="form" label="栏目名称">
                    <el-input v-model="form.name"/>
                </el-form-item>
                <el-form-item label="序号">
                    <el-input v-model="form.num"/>
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
            title:"添加栏目信息",
            visible:false,
            category:[],
            form:{
                type:"category"
            }
        }
    },
    created(){
        this.loadDate();
    },
     methods:{
         //提交
         submitHandler(){
             let url = "http://localhost:6677/category/saveOrUpdate";
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
        //重载产品数据
        loadDate(){
            //this -> vue实例，通过vue实例访问该实例中的数据
            //this.title/this.toAddHandler
            let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.category = response.data;
            })
        },
        toAddHandler(){
            this.title = "添加栏目信息";
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
            this.title = "修改栏目信息";
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