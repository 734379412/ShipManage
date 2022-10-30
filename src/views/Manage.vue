<template>
  <div>
    <el-container style="min-height: 100vh">
      <el-aside :width="sideWidth + 'px'" style="background-color: rgb(238, 241, 246);box-shadow: 2px 0 6px rgb(0 21 41 / 35%)">
        <Aside :isCollapse="isCollapse" :logoTextShow="logoTextShow"/>
      </el-aside>

      <el-container>
        <el-header style="border-bottom: 1px solid #ccc" >
          <Header :collapseBtnClass="collapseBtnClass" :collapse="collapse" :user="user"/>
        </el-header>

        <el-main>
<!--          表示当前页面的子路由会在<router-view/>里展示-->
          <router-view @refreshUser="getUser"/>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>

// import request from "@/utils/request";
import Aside from "@/components/Aside";
import Header from "@/components/Header";

export default {
  name: 'HomeView',
  data() {
    return {
      // tableData:[],
      // total:0,
      // pageNum:1,
      // pageSize:10,
      // username:"",
      // email:"",
      // address:"",
      // form:{},
      // dialogFormVisible:false,
      collapseBtnClass:'el-icon-s-fold',
      isCollapse:false,
      sideWidth:200,
      logoTextShow:true,
      user:localStorage.getItem("user")?JSON.parse(localStorage.getItem("user")):{}
      // multipleSelection:[],
      // headerBg:'headerBg'
    }
  },
  components:{
    Aside,
    Header
  },

  methods:{
    collapse(){ //点击收缩按钮触发
       this.isCollapse=!this.isCollapse
      if(this.isCollapse){ //收缩
        this.sideWidth=64
        this.collapseBtnClass='el-icon-s-unfold'
        this.logoTextShow=false
      }else{ //展开
        this.sideWidth=200
        this.collapseBtnClass='el-icon-s-fold'
        this.logoTextShow=true
      }
    },
    getUser() {
      this.request.get("user/username/" + this.user.username).then(res =>{
        this.user=res.data
      })
    }
  }
}
</script>
