<template>
  <div>
    <div style="margin-bottom: 30px">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>船舶管理</el-breadcrumb-item>
      </el-breadcrumb>
    </div>


    <div style="margin: 10px 0">
      <el-input style="width: 200px" placeholder="请输入船舶名" suffix-icon="el-icon-s-custom" v-model="shipname"></el-input>
<!--      <el-input style="width: 200px" placeholder="请输入船舶类型" suffix-icon="el-icon-ship" v-model="type" class="ml-5"></el-input>-->

      <el-select style="width: 200px" placeholder="请选择船舶类型" suffix-icon="el-icon-ship" v-model="type" class="ml-5">
        <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
        </el-option>
      </el-select>

<!--      <el-input style="width: 200px" placeholder="请输入所属公司" suffix-icon="el-icon-s-cooperation" v-model="company" class="ml-5"></el-input>-->
      <el-button class="ml-5" type="primary" @click="load">搜索<i class="el-icon-search"></i></el-button>
      <el-button  type="warning" @click="reset">重置<i class="el-icon-refresh-left"></i></el-button>
    </div>
    <div style="margin: 10px 0">
      <el-button type="primary" @click="handleAdd">新增<i class="el-icon-circle-plus-outline"></i></el-button>
      <el-popconfirm
          class="ml-5"
          confirm-button-text='确定删除'
          cancel-button-text='我再想想'
          icon="el-icon-info"
          icon-color="red"
          title="您确定删除吗？"
          @confirm="delBatch"
      >
        <el-button type="danger" slot="reference"  :style="{display: visible}" >批量删除<i class="el-icon-lollipop"></i></el-button>
      </el-popconfirm>
      <el-upload action="http://localhost:9090/ship/import" :show-file-list="false" accept="xlsx" :on-success="handleExcelImportSuccess" style="display: inline-block">
        <el-button type="primary" class="ml-5">导入<i class="el-icon-upload2"></i></el-button>
      </el-upload>
      <el-button type="primary" class="ml-5" @click="exp">导出<i class="el-icon-download"></i></el-button>
    </div>

    <el-table :data="tableData" border stripe header-cell-class-name="headerBg"  @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column prop="id" label="ID" width="80"></el-table-column>
      <el-table-column prop="shipname" label="船舶名" width="140"></el-table-column>
      <el-table-column prop="nickname" label="昵称" width="120"></el-table-column>
      <el-table-column prop="type" label="船舶类型"></el-table-column>
      <el-table-column prop="company" label="所属公司"></el-table-column>
      <el-table-column prop="length" label="船长(米)"></el-table-column>
      <el-table-column prop="width" label="船宽(米)"></el-table-column>
      <el-table-column prop="designedDraft" label="设计吃水(米)"></el-table-column>
      <el-table-column  label="操作" align="center">
        <template slot-scope="scope">   <!--数据放在scope里 下面用scope传数据-->
          <el-button type="primary" @click="handleEdit(scope.row)" :style="{display: visible}">编辑<i class="el-icon-edit"></i></el-button>
          <el-popconfirm
              class="ml-5"
              confirm-button-text='确定删除'
              cancel-button-text='我再想想'
              icon="el-icon-info"
              icon-color="red"
              title="您确定删除吗？"
              @confirm="handleDelete(scope.row.id)"
          >
            <el-button type="danger" slot="reference" :style="{display: visible}" >删除<i class="el-icon-ice-cream-round"></i></el-button>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <div style="padding: 10px 0">
      <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageNum"
          :page-sizes="[2, 5, 10, 20]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
      </el-pagination>
    </div>

<!--    <el-dialog title="船舶信息" :visible.sync="dialogFormVisible" width="30%">-->
<!--      <el-form label-width="70px" size="small" :model="ship" :rules="rules" ref="shipForm">-->
<!--        <el-form-item label="船名" >-->
<!--          <el-input v-model="form.shipname" autocomplete="off"></el-input>-->
<!--        </el-form-item>-->
<!--        <el-form-item label="昵称" >-->
<!--          <el-input v-model="form.nickname" autocomplete="off"></el-input>-->
<!--        </el-form-item>-->
<!--        <el-form-item label="船舶类型" >-->
<!--          <el-select v-model="form.type" placeholder="请选择">-->
<!--            <el-option-->
<!--                v-for="item in options"-->
<!--                :key="item.value"-->
<!--                :label="item.label"-->
<!--                :value="item.value">-->
<!--            </el-option>-->
<!--          </el-select>-->
<!--&lt;!&ndash;          <el-input v-model="form.type" autocomplete="off"></el-input>&ndash;&gt;-->
<!--        </el-form-item>-->
<!--        <el-form-item label="所属公司" >-->
<!--          <el-input  v-model="form.company" autocomplete="off"></el-input>-->
<!--        </el-form-item>-->
<!--        <el-form-item label="船长" >-->
<!--          <el-input v-model="form.length" autocomplete="off"></el-input>-->
<!--        </el-form-item>-->
<!--        <el-form-item label="船宽" >-->
<!--          <el-input v-model="form.width" autocomplete="off"></el-input>-->
<!--        </el-form-item>-->
<!--        <el-form-item label="设计吃水" >-->
<!--          <el-input v-model="form.designedDraft" autocomplete="off"></el-input>-->
<!--        </el-form-item>-->
<!--        <div style="margin: 10px 0;text-align: right">-->
<!--          <el-button @click="dialogFormVisible = false">取 消</el-button>-->
<!--          <el-button type="primary" @click="save">确 定</el-button>-->
<!--        </div>-->
<!--      </el-form>-->
<!--&lt;!&ndash;      <div slot="footer" class="dialog-footer">&ndash;&gt;-->

<!--    </el-dialog>-->

    <el-dialog title="船舶信息" :visible.sync="dialogFormVisible" width="30%">
      <el-form label-width="80px" size="small" :model="ship" :rules="rules" ref="shipForm">
        <el-form-item label="船名" prop="shipname">
          <el-input v-model="ship.shipname" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="昵称" prop="nickname">
          <el-input v-model="ship.nickname" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="船舶类型" prop="type">
          <el-select v-model="ship.type" placeholder="请选择">
            <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
            </el-option>
          </el-select>
          <!--          <el-input v-model="form.type" autocomplete="off"></el-input>-->
        </el-form-item >
        <el-form-item label="所属公司" prop="company">
          <el-input  v-model="ship.company" autocomplete="off"></el-input>
        </el-form-item >
        <el-form-item label="船长" prop="length">
          <el-input v-model="ship.length" autocomplete="off"></el-input>
        </el-form-item >
        <el-form-item label="船宽" prop="width">
          <el-input v-model="ship.width" autocomplete="off"></el-input>
        </el-form-item >
        <el-form-item label="设计吃水" prop="designedDraft">
          <el-input v-model="ship.designedDraft" autocomplete="off"></el-input>
        </el-form-item>
        <div style="margin: 10px 0;text-align: right">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="save">确 定</el-button>
        </div>
      </el-form>
      <!--      <div slot="footer" class="dialog-footer">-->

    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "Ship",
  data(){
    return{
      ship:{},
      rules: {
        shipname: [
          { required: true, message: '请输入船名', trigger: 'blur' },
        ],
        nickname: [
          { required: true, message: '请输入昵称', trigger: 'blur' },
        ],
        company: [
          { required: true, message: '请输入所属公司', trigger: 'blur' },
        ],
        type:[
          { required: true, message: '请选择船舶类型', trigger: 'blur' },
        ],
        length: [
          { required: true, message: '请输入船长', trigger: 'blur' },
        ],
        width: [
          { required: true, message: '请输入船宽', trigger: 'blur' },
        ],
        designedDraft: [
          { required: true, message: '请输入设计吃水', trigger: 'blur' },
        ]
      },
      visible:'none',
      tableData:[],
      total:0,
      pageNum:1,
      pageSize:10,
      shipname:"",
      type:"",
      // form:{},
      dialogFormVisible:false,
      multipleSelection:[],
      headerBg:'headerBg',
      user:localStorage.getItem("user")?JSON.parse(localStorage.getItem("user")):{},
      options: [{
        value: '客货船',
        label: '客货船'
      }, {
        value: '普通货船',
        label: '普通货船'
      }, {
        value: '集装箱船',
        label: '集装箱船'
      }, {
        value: '滚装船',
        label: '滚装船'
      }, {
        value: '载驳货船',
        label: '载驳货船'
      }, {
        value: '散货船',
        label: '散货船'
      }, {
        value: '油船',
        label: '油船'
      }, {
        value: '液化气体船',
        label: '液化气体船'
      }, {
        value: '兼用船',
        label: '兼用船'
      }, {
        value: '供应船',
        label: '供应船'
      },{
        value: '工程船',
        label: '工程船'
      },{
        value: '杂货船',
        label: '杂货船'
      },{
        value: '冷藏船',
        label: '冷藏船'
      },{
        value: '其他',
        label: '其他'
      }],
      value:''
    }
  },
  created() {

    this.request.get("/user/username/"+this.user.username).then(res =>{
      if(res.code === '200'){
        this.user = res.data
        if(this.user.type ==='1') {
          this.visible='none'
          this.load()
        }else{
          this.visible=''
          this.load()
        }
      }
    })
    // this.reset()

  },
  methods:{
    reset(){
      this.shipname=""
      this.type=""
      if(this.user.type === '2'){
        this.user.company=''
      }
      this.load()
    },

    save(){
      this.$refs['shipForm'].validate((valid)=>{
        if(valid){
          this.request.post("/ship",this.ship).then(res =>{
            if(res){
              this.$message.success("保存成功")
              this.dialogFormVisible=false
              this.load()
            }else{
              this.$message.error("保存失败")
            }
          });
        }else{
          this.$message.error("请正确输入相关信息")
          return false;
        }
      });
    },

    handleDelete(id){
      this.request.delete("/ship/"+id).then(res =>{
        if(res) {
          this.$message.success("删除成功")
          this.load()
        }else{
          this.$message.error("删除失败")
        }
      })
    },
    load(){
      this.request.get("/user/username/"+this.user.username).then(res =>{this.user = res.data});
      this.request.get("/ship/page",{
            params:{
              pageNum:this.pageNum,
              pageSize:this.pageSize,
              shipname:this.shipname,
              type:this.type,
              company:this.user.company
            }
          }
      ).then(res =>{
        console.log(res)

        this.tableData=res.records
        this.total=res.total
      })
    },
    handleSelectionChange(val){
      console.log(val)
      this.multipleSelection=val
    },

    handleSizeChange(pageSize){
      console.log(pageSize)
      this.pageSize=pageSize
      this.load()
    },

    handleCurrentChange(pageNum){
      console.log(pageNum)
      this.pageNum=pageNum
      this.load()
    },

    handleAdd(){
      this.dialogFormVisible=true
      this.ship={}
      this.ship.company=this.user.company
    },

    handleEdit(row){
      // this.form=row
      this.ship= Object.assign({},row)
      this.dialogFormVisible=true
      this.reset()
    },
    delBatch(){
      let ids=this.multipleSelection.map(v => v.id)  //[{},{},{}] =>[1,2,3] 对象数组变id数组
      this.request.post("/ship/del/batch",ids).then(res =>{
        if(res) {
          this.$message.success("批量删除成功")
          this.load()
        }else{
          this.$message.error("批量删除失败")
        }
      })
    },
    handleExcelImportSuccess(){

    },
    exp(){
      window.open("http://localhost:9090/ship/export")
    }
  }
}
</script>

<style>
.headerBg{
  background-color: #eee !important;
}
</style>