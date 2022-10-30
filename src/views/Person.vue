<template>
  <div>
    <div style="margin-bottom: 30px">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>个人信息</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
  <el-card style="width: 500px; padding: 20px">
    <el-form label-width="70px" size="small">
      <el-form-item label="用户名" >
        <el-input disabled v-model="form.username" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="昵称" >
        <el-input v-model="form.nickname" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="邮箱" >
        <el-input v-model="form.email" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="所属公司" >
        <el-input v-model="form.company" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="电话" >
        <el-input v-model="form.phone" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="地址" >
        <el-input type="textarea" v-model="form.address" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="save">确 定</el-button>
      </el-form-item>
    </el-form>
  </el-card>
  </div>
</template>

<script>
export default {
  name: "Person",
  data(){
    return{
      form:{},
      user:localStorage.getItem("user")?JSON.parse(localStorage.getItem("user")):{}
    }
  },
  created() {
    this.request.get("/user/username/"+this.user.username).then(res =>{
      if(res.code === '200'){
        this.form = res.data
      }
    })
  },
  methods:{
    save(){
      this.request.post("/user",this.form).then(res =>{
        if(res){
          this.$message.success("保存成功")
          this.$emit("refreshUser")



        }else{
          this.$message.error("保存失败")
        }
      })
    },
  }
}
</script>

<style scoped>

</style>