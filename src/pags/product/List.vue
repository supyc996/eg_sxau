<template>
  <div>
    <h2>产品管理</h2>
    <!-- 按钮 -->
    <el-button type="primary" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="products">
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="name" label="产品名称"></el-table-column>
        <el-table-column prop="price" label="价格"></el-table-column>
        <el-table-column prop="status" label="描述"></el-table-column>
        <el-table-column prop="categoryId" label="所属产品"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
            <a href="" @click.prevent="toDeleteHandler(slot.row.id)"><i class="el-icon-delete"></i></a>
            <a href="" @click.prevent="toUpdateHandler"><i class="el-icon-edit"></i></a>
            <a href="" @click.prevent="toUpdateHandler">详情</a>
            </template>
        </el-table-column>
    </el-table>
    <!-- /表格结束 -->
     <!-- 分页开始 -->
  
    <el-pagination
            layout="prev, pager, next"
            :total="50">
        </el-pagination>
    <!-- 模态框 -->
    <el-dialog
      title="详情信息"
      :visible.sync="visible"
      width="60%">
      ---{{form}}
      <el-form :model="form" label-width="80px">
        <el-form-item label="编号">
          <el-input v-model="form.id"></el-input>
        </el-form-item>
        <el-form-item label="产品名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="价格">
          <el-input v-model="form.price"></el-input>
        </el-form-item>
        <el-form-item label="描述">
          <el-input v-model="form.status"></el-input>
        </el-form-item>
         <el-form-item label="所属产品">
          <el-input v-model="form.categoryId"></el-input>
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
      let url = "http://localhost:6677/product/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到customers中，this指向外部函数的this
        this.products = response.data;
      })
    },
    submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/product/saveOrUpdate";
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
       // alert(id);
       //调用后台接口，传数据
       let url="http://localhost:6677/product/deleteById?id="+id;
       request.get(url).then((response)=>{
         //删除结束后，刷新
         this.loadData();
         //提示结果
          this.$message({
            type: 'success',
            message: response.message
          });
        })
        
      })     
    },
    toUpdateHandler(){
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.visible = true;
    }
  },
  
  // 用于存放要向网页中显示的数据
  created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    },
    data(){
        return{
            title:"录入信息",
            visible:false,
            products:[],
            form:{
                type:"product"
            }

        }
    },
  methods:{
        //提交
        submitHandler(){
            let url="http://localhost:6677/product/saveOrUpdate";
            //前端向后台发送请求，完成数据的保存数据
            request({
                url,
                method:"post",
                //告诉后台我的请求体中放的是查询字符串
                handers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据，将this.form转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModuleHandler();
                this.getData();
                this.$message({
                    message:response.message
                })
            })

        },
        //重载员工数据
        loadData(){
            //this ->vue 实例，通过vue实例访问该实例中的数据，methods
            //this.title/this.toAddHandler
            let url="http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
                //将查询结果设置到product中
                this.products=response.data;
            })
        },
        toAddHandler(){
            this.title="录入产品信息";
            this.visible=true;
        },
        closeModule(){
            this.visible=false;
        },
        toDeleteHandler(id){
            this.$confirm('此操作将永久删除该产品, 是否继续?', '提示', {
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
        toUpdateHandler(){
            this.title="修改产品信息";
            this.visible=true;
        }
  
  }
}
    </script>

  <style scoped>
 
</style>