<template>
    <div>
    <el-button size="small" type="info" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>

    <el-table :data="customers">
        <el-table-column prop = "id" label="编号"></el-table-column>
        <el-table-column prop = "realname" label="姓名"></el-table-column>
        <el-table-column prop = "telephone" label="联系方式"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdataHandler">修改</a>
            </template>
        </el-table-column>
    </el-table>

    <el-dialog
  title="录入顾客信息"
  :visible.sync="visible"
  width="60%">
    ---{{form}}
  <el-form :model="form" label-width="80px">
    <el-form-item label="用户名">
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
    <el-button @click="closeModulHandler" size="small">取 消</el-button>
    <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
  </span>
</el-dialog>
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  
    //methods用于存放网页中需要调用的方法
    methods:{
      submitHandler(){
      let url = "http://localhost:6677/customer/saveOrUpdate"
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        //模态框关闭，
        this.closeModulHandler();
        //提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })
  },
      loadData(){
        let url = "http://localhost:6677/customer/findAll"
      request.get(url).then((response)=>{
        //将查询结果设置到customer中,this指向外部函数的this
        this.customers = response.data;
      })
      },
      toDeleteHandler(id){
         //先确认
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
        toUpdataHandler(){
          this.visible = true;
        },
        closeModulHandler(){
        this.visible = false;
        },
    toAddHandler(){
      this.visible = true;
    }

    },
    //data（）用于存放要向网页中显示的数据
    data(){
        return{
            visible:false,
       customers:[],
       form:{
         type : "customer"
       }
        }
    },
    created(){
      //this为当前vue实例
      //vue实例创建完毕
      this.loadData();
      }
    }
</script>
<style scoped>
</style>
