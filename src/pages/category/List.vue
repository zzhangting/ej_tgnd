<template>
    <div>
        <!-- 按钮 --->
        <el-button type="success" @click="toAddHandler" size="small">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- 按钮结束-->
        <!-- 表格-->
        <el-table :data="categories">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="姓名"></el-table-column>
            <el-table-column prop="num" label="数量"></el-table-column>
            <el-table-column prop="parentId" label="父属性"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                    <!--{{}}用来打印vue中的变量-->
                </template>
            </el-table-column>
        </el-table>
        <!--表格结束-->
        <!--分页开始 -->
        <!-- <el-pagination
            layout="prev, pager, next"
            :total="50">
        </el-pagination> -->
        <!-- 分页结束>
        <! -- 对话 -->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            <!-- ---{{form}}   --->
            <el-form :model="form" label-width="80px">
                <el-form-item label="密码">
                    <el-input type="password" v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="数量">
                    <el-input v-model="form.num"></el-input>
                </el-form-item>
                <el-form-item label="父属性">
                    <el-input v-model="form.parentId"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="closeModule">取 消</el-button>
                <el-button type="primary" @click="submitHandler" >确 定</el-button>
            </span>
        </el-dialog>
        <!--对话结束-->
    </div>
</template>
<script>
import request from '@/utils/request'//@表示src
import querystring from 'querystring'
//暴露接口
export default {
    //this为当前vue实例
    //用于存放要向网页中存放的数据
    created(){
        //文档加载完毕要执行的操作,vue实例创建完毕
        this.loadData();
    },
    data(){
        return{
            title:"录入栏目信息",
            visible:false,
            categories:[],
            form:{
                type:"category"
            }
        }
    },

    //用于存放网络中需要调用的方法
    methods:{
        loadData(){
            let url="http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                //将查询结果设置到customer中
                this.categories=response.data;
            })
        },
        submitHandler(){
            let url="http://localhost:6677/category/saveOrUpdate"
            //reques.post(url,this.form)
            request({
                url,
                method:"POST",
                henders:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //调用后台接口完成删除操作
               
                //请求结束,关闭模态框,提示消息
                //this.visible=false;
                this.closeModule();
                // 刷新
                this.loadData();
                // 提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toAddHandler(){
            this.form={
                type:"category" 
            }
            this.title="录入栏目信息"
            this.visible=true;
        },
        closeModule(){
            this.visible=false;
        },
        toUpdateHandler(row){
            //模态框的表单中显示出当前行的信息
            this.form=row;
            this.title="修改栏目信息";
            this.visible=true;
        },
        toDeleteHandler(id){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => { 
                let url="http://localhost:6677/category/deleteById?id="+id;
                request.get(url).then((response)=>{
                    //刷新数据
                    this.loadData();
                    //提示结果
                    this.$message({
                        type: 'success',
                        message: response.message
                    });
                })
                
            })
        }
    }
}
</script>
<style scoped>

</style>