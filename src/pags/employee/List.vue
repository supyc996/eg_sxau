<template>
  <div>
    <!-- 按钮 -->
    <el-button type="primary" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格开始 -->
    <el-table :data="employees">
      <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
      <el-table-column fixed="left" prop="username" label="用户名"></el-table-column>
      <el-table-column prop="password"  label="密码"></el-table-column>
      <el-table-column prop="gender" label="性别"></el-table-column>
      <el-table-column width="150" prop="telephone" label="电话"></el-table-column>
      <el-table-column width="250" prop="idCard" label="身份证号"></el-table-column>
      <el-table-column width="250" prop="bankCard" label="银行卡号"></el-table-column>
      <el-table-column fixed="right" label="操作">
        <!-- 获取当前行信息 -->
        <template v-slot="slot">   
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)"><i class="el-icon-delete"></i></a>
          <a href="" @click.prevent="toUpdateHandler"><i class="el-icon-edit"></i></a>
          <a href="" @click.prevent="toUpdateHandler">详情</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- 表格结束 -->
    <!-- 分页开始 -->
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
          title="录入职工信息"
          :visible.sync="visible"
          width="50%">
          <!-- {{form}}  -->
          <el-form :model="form" label-width="80px">
            <el-form-item label="用户名">
              <el-input v-model="form.username"></el-input>
            </el-form-item>
            <el-form-item label="密码">
              <el-input type="password" v-model="form.password"></el-input>
            </el-form-item>
            <el-form-item label="性别">
              <el-radio-group v-model="form.gender">
                <el-radio :label="男" checked="true">男</el-radio>
                <el-radio :label="女">女</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="电话">
              <el-input v-model="form.telephone"></el-input>
            </el-form-item>
            <el-form-item label="身份证号">
              <el-input v-model="form.idCard"></el-input>
            </el-form-item>
            <el-form-item label="银行卡号">
              <el-input v-model="form.bankCard"></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button size="small" typ @click="closeModalHandler">取 消</el-button>
            <el-button size="small" type="primary" @click="closeModalHandler">确 定</el-button>
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
      let url = "http://localhost:6677/waiter/findAll"
      request.get(url).then((response)=>{
        this.employees = response.data;
      })
    },
    submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/waiter/saveOrUpdate";
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
        this.$message({
          type: 'success',
          message: '删除成功!'
        });
      })
      
    },
    toUpdateHandler(row){
      this.form = row;
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
  data(){
      return{
      visible:false,
      employees:[],
      form:{
        type:"waiter"
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