<template>
    <div>
        <!-- 按钮 --->
        <el-button type="success" @click="toAddHandler" size="small">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- 按钮结束-->
        <!-- 表格-->
        <el-table :data="orders.list">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column width="200" prop="orderTime" label="订单时间"></el-table-column>
            <el-table-column prop="total" label="总计"></el-table-column>
            <el-table-column prop="status" label="状态"></el-table-column>
            <el-table-column prop="customerId" label="顾客ID"></el-table-column>
            <el-table-column prop="waiterId" label="员工ID"></el-table-column>
            <el-table-column prop="addressId" label="地址ID"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                    <!--{{}}用来打印vue中的变量-->
                </template>
            </el-table-column>
        </el-table>
        <!--/表格结束-->
        <!--分页开始 -->
        <el-pagination
            layout="prev, pager, next"
            @current-change="pageChangeHandler"
            :total="orders.total" >
        </el-pagination>
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
            visible:false,
            orders:{},
            form:{
                type:"order"
            },
            params:{
                page:0,
                pageSize:10
            }
        }
    },
    
    //用于存放网络中需要调用的方法
    methods:{
        pageChangeHandler(page){
            this.params.page=page-1;
            this.loadData();
        },
        loadData(){
            let url="http://localhost:6677/order/queryPage"
            request({
                url,
                method:"post",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
            data:querystring.stringify(this.params)
            }).then((response)=>{
                this.orders=response.data;
            })
        },
        submitHandler(){
            let url="http://localhost:6677/order/save"
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
        toUpdateHandler(row){
            //模态框的表单中显示出当前行的信息
            this.form=row;
            this.visible=true;
        },
        toAddHandler(){
            this.form={
                type:"order"
            }
            this.visible=true;
        },
        toDeleteHandler(id){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => { 
                let url="http://localhost:6677/order/deleteById?id="+id;
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
        },
    }
}
</script>
<style scoped>

</style>