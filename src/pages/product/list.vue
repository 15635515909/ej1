<template>
    <div>
<!--按钮-->
<div>
    <el-button size="small" type="primary" @click="toAddHandler">添加</el-button>
    <el-button size="small" type="danger">批量删除</el-button>
</div>
<!--/按钮-->
<!--表格-->
<el-table :data="products">
    <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
    <el-table-column fixed="left" prop="name" label="产品名称"></el-table-column>
    <el-table-column prop="price" label="价格"></el-table-column>
    <el-table-column prop="description" label="描述"></el-table-column>
    <el-table-column prop="categoryId"  label="所属产品"></el-table-column>
    <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
        <a href="" class="el-icon-edit" @click.prevent="toUpdateHandler(slot.row)"></a>
        <a href="" class="el-icon-delete"  @click.prevent="toDeleteHandler(slot.row.id)"></a>
        <a href="" @click.prevent="toDetailHandler">详情</a>
        </template>
    </el-table-column>
</el-table>
<!--/表格-->
<!--分页-->
<el-pagination layout="prev, pager, next" :total="50"></el-pagination>
<!--/分页-->
<!--模态框-->
 <el-dialog
      title="录入产品信息"
      :visible.sync="visible"
      width="60%">
      测试：{{form}}
      <el-form :model="form" label-width="80px">
          <el-form-item label="编号">
              <el-input v-model="form.id"/>
          </el-form-item>
           <el-form-item label="产品名称">
              <el-input  v-model="form.name"/>
          </el-form-item>
           <el-form-item label="价格">
               <el-input v-model="form.price"/>
          </el-form-item>
           <el-form-item label="描述">
              <el-input v-model="form.description"/>
          </el-form-item>
           <el-form-item label="所属产品">
              <el-input v-model="form.categoryId"/>
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
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            title:"录入产品信息",
            visible:false,
            products:[],
            form:{
                type:"product"
            }
        }
    },
    created(){
        this.loadDate()
    },
    methods:{
        submitHandler(){
            let url="http://localhost:6677/product/saveOrUpdate"
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
            let url="http://localhost:6677/product/findAll"
            request.get(url).then(response=>{
                this.products=response.data;
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
            this.title="编辑产品信息";
            this.visible=true;
        },
        toAddHandler(){
            this.title="录入产品信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        }
    }
}
</script>

<style scoped>

</style>
