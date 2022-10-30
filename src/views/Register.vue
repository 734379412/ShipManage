<template>
 <div class="wrapper">
   <div style="margin: 200px auto; background-color: #fff;width: 350px;height:450px;padding: 20px;border-radius: 10px">
     <div style="margin: 20px 0;text-align: center;font-size: 24px"><b>注 册</b></div>
     <el-form :model="user" :rules="rules" ref="userForm">
       <el-form-item prop="username">
         <el-input placeholder="请输入账号" size="medium" style="margin: 5px 0" prefix-icon="el-icon-user" v-model="user.username"></el-input>
       </el-form-item>
       <el-form-item prop="nickname">
         <el-input placeholder="请输入昵称" size="medium" style="margin: 5px 0" prefix-icon="el-icon-user-solid" v-model="user.nickname"></el-input>
       </el-form-item>
       <el-form-item prop="password">
     <el-input placeholder="请输入密码" size="medium" style="margin: 5px 0" prefix-icon="el-icon-lock" show-password v-model="user.password"></el-input>
       </el-form-item>
       <el-form-item prop="confirmPassword">
         <el-input placeholder="请确认密码" size="medium" style="margin: 5px 0" prefix-icon="el-icon-lock" show-password v-model="user.confirmPassword"></el-input>
       </el-form-item>
       <el-form-item prop="company">
         <el-input placeholder="请输入所属公司" size="medium" style="margin: 5px 0" prefix-icon="el-icon-s-cooperation"  v-model="user.company"></el-input>
       </el-form-item>
     <div style="margin: 5px 0;text-align: right">
       <el-button type="primary" size="small" autocomplete="off" @click="register">创建</el-button>
       <el-button type="warning" size="small" autocomplete="off" @click="$router.push('/login')">返回登陆</el-button>
     </div>
     </el-form>
   </div>
 </div>
</template>

<script>
import user from "@/views/User";

export default {
  name: "Login",
  data(){
    return{
      user:{},
      nickname:
      user.nickname='新用户',
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 1, max: 50, message: '长度在 1 到 50 个字符', trigger: 'blur' }
        ],
        nickname: [
          { required: true, message: '请输入昵称', trigger: 'blur' },
          { min: 1, max: 50, message: '长度在 1 到 50 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 1, max: 50, message: '长度在 1 到 50 个字符', trigger: 'blur' }
        ],
        confirmPassword:[
          { required: true, message: '请确认密码', trigger: 'blur' },
          { min: 1, max: 50, message: '长度在 1 到 50 个字符', trigger: 'blur' }
        ],
        company:[
          { required: true, message: '请确认所属公司', trigger: 'blur' },
          { min: 1, max: 50, message: '长度在 1 到 50 个字符', trigger: 'blur' }
        ],
      }
    }
  },
  methods:{
    register(){
      this.$refs['userForm'].validate((valid) => {
        if (valid) {
          if(this.user.password !== this.user.confirmPassword){
            this.$message.error("两次输入的密码不一致")
            return false
          }
          this.request.post("/user/register",this.user).then(res =>{
            if(res.code==='200'){
              this.$router.push("/login")
              this.$message.success("注册成功")
            }else
            {
              this.$message.error(res.msg)
            }
          });
        } else {
          this.$message.error("请正确输入用户名或密码")
          return false;
        }
      });
    }
  }
}
</script>

<style>
   .wrapper{
     height: 100vh;
     background-image: linear-gradient(to bottom right,#FC4668,#3F5EFB);
     overflow:hidden;
   }
</style>