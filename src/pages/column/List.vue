<template>
    <div>
        <h2>栏目管理</h2>
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="columns">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num" label="序号"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler">删除</a>
                    <a href="" @click.prevent="toUpdataHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- /分页结束 -->
        <!-- 模态框 -->
        <el-dialog :title="title" :visible.sync="visible" width="50%">
            {{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="栏目名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="序号">
                    <el-input v-model="form.num"></el-input>
                </el-form-item>
                
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModelHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
        loadData(){
            let url ="http://localhost:6677/category/findAll";
            request.get(url).then((response)=>{
                this.columns = response.data;
            })
        },
        submitHandler(){
            let url = "http://localhost:6677/category/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModelHandler();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
            
        },
        toAddHandler(){
            this.visible=true;
        },
        closeModelHandler(){
            this.visible=false;
        },
        toUpdataHandler(){
            this.title="修改栏目信息"
            this.visible=true;
        },
        toDeleteHandler(){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.$message({
                    type: 'success',
                    message: '删除成功!'
                })
            })
        }
    },
    data(){
        return{
            title:"录入栏目信息",
            visible:true,
            columns:[],
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