<template>
      <div>
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small">批量删除</el-button>
    <el-table :data="address">
    <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
    <el-table-column fixed="left" prop="Sname" label="省"></el-table-column>
    <el-table-column prop="city" label="市"></el-table-column>
    <el-table-column prop="area" label="区"></el-table-column>
    <el-table-column prop="address"  label="地址"></el-table-column>
    <el-table-column prop="tel"  label="电话"></el-table-column>
    <el-table-column prop="customer_id"  label="顾客编号"></el-table-column>
    <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
        <a href=""  @click.prevent="toUpdateHandler(slot.row)">修改</a>
        <a href=""  @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
        
        </template>
    </el-table-column>
</el-table>
<!--/表格-->
<!--分页-->
<el-pagination layout="prev, pager, next" :total="50"></el-pagination>
<!--/分页-->
<!--模态框-->
<el-dialog
      title="录入地址信息"
      :visible.sync="visible"
      width="60%">
    测试：{{form}}
      <el-form :model="form" label-width="80px">
          <el-form-item label="编号">
              <el-input v-model="form.id"/>
          </el-form-item>
           <el-form-item label="省">
              <el-input  v-model="form.Sname"/>
          </el-form-item>
           <el-form-item label="市">
               <el-input v-model="form.city"/>
          </el-form-item>
           <el-form-item label="区">
              <el-input v-model="form.area"/>
          </el-form-item>
           <el-form-item label="地址">
              <el-input v-model="form.address"/>
          </el-form-item>
          <el-form-item label="电话">
              <el-input v-model="form.tel"/>
          </el-form-item>
          <el-form-item label="用户编号">
              <el-input v-model="form.customer_id"/>
          </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
<!--/模态框-->
      </div>

    </template>
    
    <script>
    export default {
        data(){
           return{
            title:"录入地址信息",
            visible:false,
            addresses:[],
            form:{
                type:"address"
            }
        }
    },
    created(){
        this.loadDate()
    },
    methods:{
        submitHandler(){
            let url="http://134.175.154.93:6677/address/saveOrUpdate "
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,method:"post",
                //告诉给后台我的请求体中放的查询字符串
                headers:{
                    "content-type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
      //请求结束，模态框关闭
      this.closeModalHandler();
      //自动刷新
      this.loadData();
      this.$message({
        type:"success",
        message:response.message
      })
            })
        },
        loadDate(){
            //this=>指向vue实例，通过vue实例访问该实例中数据
            let url="http://134.175.154.93:6677/address/findAll"
            request.get(url).then(response=>{
                this.addresses=response.data;
            })
        },
        toDeleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message({
          type: 'success',
          message: '删除成功!'
        });
      })
      



      
        },
        toUpdateHandler(row){
            this.form-=row;
            this.title="编辑地址信息";
            this.visible=true;
        },
        
        toAddHandler(){
            this.title="录入地址信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        }
    }
    }
    </script>
    
    <style>
    
    </style>

