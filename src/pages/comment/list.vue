<template>
  <div>
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="comments">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="content" label="内容"></el-table-column>
      <el-table-column prop="comment_time" label="评论时间"></el-table-column>
      <el-table-column prop="order_id" label="订单编号"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="slot">
          <a href="" class="el-icon-edit" @click.prevent="toUpdateHandler(slot.row)"></a>
        <a href="" class="el-icon-delete"  @click.prevent="toDeleteHandler(slot.row.id)"></a>
        </template>
      </el-table-column> 
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      title="填写评论"
      :visible.sync="visible"
      width="60%">
        ---{{form}}
      <el-form :model="form" label-width="80px">
        <el-form-item label="编号">
          <el-input v-model="form.id"></el-input>
        </el-form-item>
        <el-form-item label="内容">
          <el-input  v-model="form.content"></el-input>
        </el-form-item>
        <el-form-item label="评论时间">
          <el-input v-model="form.comment_time"></el-input>
        </el-form-item>
        <el-form-item label="订单编号">
          <el-input v-model="form.order_id"></el-input>
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
  // 用于存放网页中需要调用的方法
  methods:{
    loadData(){
      let url = "http://localhost:6677/comment/findAll"
      request.get(url).then((response)=>{
        this.comments = response.data;
      })
    },
    submitHandler(){
      let url = "http://localhost:6677/comment/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })

    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let url="http://localhost:6677/comment/deleteById?id"+id;
        request.get(url).then((response)=>{
          //刷新数据
          this.loadData();
          //提示结果
        this.$message({
                  type:"success",
                  message:response.message
        });
        })
        this.$message({
          type: 'success',
          message: '删除成功!'
        });
      
      
    })
    },
    toUpdateHandler(row){
      this.form=row;
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.form={type:"comment"}
      this.visible = true;
    }
    },
  // 用于存放要向网页中显示的数据
  data(){
    return {
      visible:false,
      comments:[],
      form:{
        type:"comment"
      }
    }
  },
  created(){
    // this为当前vue实例对象
    // vue实例创建完毕 
    this.loadData()

  }
}
</script>

<style scoped>
 
</style>
