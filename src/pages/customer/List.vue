<template>
    <div>
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="customers">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <!-- //获取当前行数据 -->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格 -->

        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- /分页结束 -->

        <!-- 模态框 -->
        <el-dialog :title="title" :visible.sync="visible" width="50%">
            ---{{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="用户名">
                    <!-- v modell -->
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input type="password" v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="真实姓名">
                    <el-input v-model="form.realname"></el-input>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
            </el-form>

            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>

    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中要调用的方法(事件处理函数)
    methods:{
        loadData(){
            //Vue实例创建完毕
            let url = "http://localhost:6677/customer/findAll"
            request.get(url).then((response)=>{
                //将查询结果设置到customers
                this.customers = response.data;
            })
        },
        submitHandler(){
            //this.form 对象---字符串-->后台
            //通过request与后台进行交互，并且要写带参数
            let url = "http://localhost:6677/customer/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求结束,模态框关闭
                this.closeModalHandler();
                //刷新
                this.loadData();
                //接收消息
                this.$message({
                    type:"success",
                    message:response.message
                })

            })
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            //将form变为初始值
            this.form = {
                type:"customer"
            }
            this.visible=true;
        },
        toUpdataHandler(a){
            this.title="修改顾客信息"
            this.visible=true;
            this.form=a;
        },
        toDeleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                let url = "http://localhost:6677/customer/deleteById?id="+id;
                request.get(url).then((response)=>{
                    //刷新数据
                    this.loadData();
                    //提示结果
                    this.$message({
                        type: 'success',
                        message: response.message
                    });
                })
                
            });
        }
        
    },
    //用于存放要在网页中显示的数据
    data(){
        return{
            title:"录入顾客信息",
            visible:true,
            customers:[],
            form:{
                
            }
        }
    },
    created(){
        this.loadData();
    }
}
</script>

<style scoped>

</style>
