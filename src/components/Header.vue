<template>
<div style="font-size: 12px;line-height: 60px;display: flex">
  <div style="flex: 1;font-size: 20px">
    <span :class="collapseBtnClass" style="cursor: pointer" @click="collapse"></span>
  </div>
  <el-dropdown style="width: 70px;cursor: pointer">
    <span>{{user.nickname}}</span><i class="el-icon-arrow-down" style="margin-left: 5px"></i>
    <el-dropdown-menu slot="dropdown">
      <el-dropdown-item>
        <router-link to="/person">个人信息</router-link>
      </el-dropdown-item>
      <el-dropdown-item>
<!--        <router-link to="/login" style="text-decoration: none" @click="logout">退出</router-link>-->
        <span style="text-decoration: none" @click="logout">退出</span>
      </el-dropdown-item>
    </el-dropdown-menu>
  </el-dropdown>
</div>
</template>

<script>
export default {
  name: "Header",
  props:{
    collapseBtnClass: String,
    collapse: Function,
    user:Object
  },
  data(){
    return{
      // user:localStorage.getItem("user")?JSON.parse(localStorage.getItem("user")):{}
    }
  },
  methods:{
    logout(){
      this.$router.push("/login")
      localStorage.removeItem("user")
      this.$message.success("退出成功")
    }
  }
}
</script>

<style scoped>

</style>