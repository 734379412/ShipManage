<template>
  <div>
    <div style="margin-bottom: 30px">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>装卸作业委托书管理</el-breadcrumb-item>
      </el-breadcrumb>
    </div>


    <div style="margin: 10px 0">
      <el-input style="width: 200px" placeholder="请输入船舶名" suffix-icon="el-icon-s-custom" v-model="shipName"></el-input>
            <el-input style="width: 200px" placeholder="请输入委托人名" suffix-icon="el-icon-s-cooperation" v-model="client" class="ml-5"></el-input>
      <el-select  v-model="type" placeholder="请选择申请书审批状态" class="ml-5" >
        <el-option
            v-for="item in uOptions"
            :key="item.uValue"
            :label="item.uLabel"
            :value="item.uValue">
        </el-option>
      </el-select>
      <el-button class="ml-5" type="primary" @click="load">搜索<i class="el-icon-search"></i></el-button>
      <el-button  type="warning" @click="reset">重置<i class="el-icon-refresh-left"></i></el-button>
    </div>
    <div style="margin: 10px 0">
      <el-button type="primary" @click="handleAdd" :style="{display: visibleAdd}">新增<i class="el-icon-circle-plus-outline"></i></el-button>
      <el-popconfirm
          class="ml-5"
          confirm-button-text='确定删除'
          cancel-button-text='我再想想'
          icon="el-icon-info"
          icon-color="red"
          title="你确定删除吗？"
          @confirm="delBatch"
      >
        <el-button type="danger" slot="reference"  :style="{display: visibleDelBth}" >批量删除<i class="el-icon-lollipop"></i></el-button>
      </el-popconfirm>
      <el-upload action="http://localhost:9090/power-attorney/import" :show-file-list="false" accept="xlsx" :on-success="handleExcelImportSuccess" style="display: inline-block">
        <el-button type="primary" class="ml-5" :style="{display: visibleImport}">导入<i class="el-icon-upload2"></i></el-button>
      </el-upload>
      <el-button type="primary" class="ml-5" @click="exp">导出<i class="el-icon-download"></i></el-button>
    </div>

    <el-table :data="tableData" border stripe header-cell-class-name="headerBg"  @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column prop="id" label="ID" width="50"></el-table-column>
      <el-table-column prop="client" label="委托人" width="70"></el-table-column>
      <el-table-column prop="shipName" label="船舶名" width="80"></el-table-column>
      <el-table-column prop="time" label="作业时间" width="145"></el-table-column>
      <el-table-column prop="place" label="作业地点"></el-table-column>
      <el-table-column prop="workType" label="作业类型" width="60"></el-table-column>
      <el-table-column prop="phone" label="委托人电话" width="140"></el-table-column>
      <el-table-column prop="goodsType" label="货物类型" width="60"></el-table-column>
      <el-table-column prop="goodsName" label="货物名称" width="60"></el-table-column>
      <el-table-column prop="type" label="委托书状态" ></el-table-column>
      <el-table-column prop="manageTime" label="处理时间"></el-table-column>
      <el-table-column prop="manager" label="处理人员"></el-table-column>
      <el-table-column prop="company" label="委托人公司"></el-table-column>
      <el-table-column  label="操作" align="center" width="180">
        <template slot-scope="scope">   <!--数据放在scope里 下面用scope传数据-->
          <el-button type="primary" @click="handleEdit(scope.row)" :style="{display: visibleEdit}">审批<i class="el-icon-edit"></i></el-button>
          <el-popconfirm
              class="ml-5"
              confirm-button-text='确定删除'
              cancel-button-text='我再想想'
              icon="el-icon-info"
              icon-color="red"
              title="您确定删除吗？"
              @confirm="handleDelete(scope.row.id)"
          >
            <el-button type="danger" slot="reference" :style="{display: visibleDel}" >删除<i class="el-icon-ice-cream-round"></i></el-button>
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

    <el-dialog title="新增作业委托书信息" :visible.sync="dialogFormVisible" width="30%">
      <el-form label-width="80px" size="small" :model="attorney" :rules="rules" ref="attorneyForm">
        <el-form-item label="委托人" prop="client">
          <el-input v-model="attorney.client" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="船舶名" prop="shipName">
          <el-input v-model="attorney.shipName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="作业类型" prop="workType">
          <el-select v-model="attorney.workType" placeholder="请选择" >
            <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
            </el-option>
          </el-select>
<!--          <el-input v-model="form.workType" autocomplete="off"></el-input>-->
        </el-form-item>
<!--        <el-form-item label="所属公司" >-->
<!--          <el-input  v-model="form.company" autocomplete="off"></el-input>-->
<!--        </el-form-item>-->
        <el-form-item label="货物类型" prop="goodsType">
          <el-input v-model="attorney.goodsType" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="货物名称" prop="goodsName">
          <el-input v-model="attorney.goodsName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="联系电话" prop="phone">
          <el-input v-model="attorney.phone" autocomplete="off"></el-input>
        </el-form-item>
        <div style="margin: 10px 0;text-align: right">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="save">确 定</el-button>
        </div>
      </el-form>

    </el-dialog>



    <el-dialog title="审批作业委托书信息" :visible.sync="EditFormVisible" width="30%">
      <el-form label-width="83px" size="small">
        <el-form-item label="委托人" >
          <el-input v-model="form.client" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="船舶名" >
          <el-input v-model="form.shipName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="作业时间" >
          <el-date-picker
              v-model="form.time"
              type="datetime"
              placeholder="选择日期时间">
          </el-date-picker>
<!--          <el-input v-model="form.time" autocomplete="off"></el-input>-->
        </el-form-item>
        <el-form-item label="作业地点" >
          <el-select  v-model="form.place" placeholder="请选择" >
            <el-option
                v-for="item in pOptions"
                :key="item.pValue"
                :label="item.pLabel"
                :value="item.pValue">
            </el-option>
          </el-select>
<!--          <el-input v-model="form.place" autocomplete="off"></el-input>-->
        </el-form-item>
        <el-form-item label="作业类型" >
          <el-select v-model="form.workType" placeholder="请选择">
            <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
            </el-option>
          </el-select>
          <!--          <el-input v-model="form.workType" autocomplete="off"></el-input>-->
        </el-form-item>
<!--        style="background-color: #FC4668"-->
        <el-form-item  label="审批类型" style="background-color: #ffdf4d" >
          <el-select  v-model="form.type" placeholder="请选择" >
            <el-option
                v-for="item in nOptions"
                :key="item.nValue"
                :label="item.nLabel"
                :value="item.nValue">
            </el-option>
          </el-select>
          <!--          <el-input v-model="form.workType" autocomplete="off"></el-input>-->
        </el-form-item>
                <el-form-item label="委托人所属公司" >
                  <el-input  v-model="form.company" autocomplete="off"></el-input>
                </el-form-item>
        <el-form-item label="货物类型" >
          <el-input v-model="form.goodsType" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="货物名称" >
          <el-input v-model="form.goodsName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="委托人电话" >
          <el-input v-model="form.phone" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="EditFormVisible= false">取 消</el-button>
        <el-button type="primary" @click="Edit">确 定</el-button>
      </div>
    </el-dialog>

  </div>
</template>

<script>
export default {
  name: "Attorney",
  data(){
    return{
      attorney:[],
      rules: {
        client: [
          { required: true, message: '请输入委托人姓名', trigger: 'blur' },
        ],
        shipName: [
          { required: true, message: '请输入船舶名', trigger: 'blur' },
        ],
        workType: [
          { required: true, message: '请选择作业类型', trigger: 'blur' },
        ],
        goodsType: [
          { required: true, message: '请输入货物类型', trigger: 'blur' },
        ],
        goodsName: [
          { required: true, message: '请输入货物名称', trigger: 'blur' },
        ],
        phone: [
          { required: true, message: '请输入委托人联系方式(电话)', trigger: 'blur' },
        ]
      },
      visibleDel:'none',
      visibleEdit:'none',
      visibleImport:'none',
      visibleDelBth:'none',
      visibleAdd:'none',
      tableData:[],
      getTime:'',
      total:0,
      pageNum:1,
      pageSize:10,
      shipName:"",
      client:"",
      type:"",
      form:{},
      dialogFormVisible:false,
      EditFormVisible:false,
      multipleSelection:[],
      headerBg:'headerBg',
      user:localStorage.getItem("user")?JSON.parse(localStorage.getItem("user")):{},
      options: [{
        value: '装货',
        label: '装货'
      }, {
        value: '卸货',
        label: '卸货'
      }],
      value:'',
      nOptions: [{
        nValue: '审批通过',
        nLabel: '审批通过'
      }, {
        nValue: '驳回',
        nLabel: '驳回'
      }],
      nValue:'',
      uOptions: [{
        uValue: '未办',
        uLabel: '未办'
      },{
        uValue: '审批通过',
        uLabel: '审批通过'
      }, {
        uValue: '驳回',
        uLabel: '驳回'
      } ],
      uValue:'',
      pOptions: [{
        pValue: '一号港口',
        pLabel: '一号港口'
      },{
        pValue: '二号港口',
        pLabel: '二号港口'
      }, {
        pValue: '三号港口',
        pLabel: '三号港口'
      }, {
        pValue: '四号港口',
        pLabel: '四号港口'
      }, {
        pValue: '五号港口',
        pLabel: '五号港口'
      }, {
        pValue: '六号港口',
        pLabel: '六号港口'
      }, {
        pValue: '七号港口',
        pLabel: '七号港口'
      }, {
        pValue: '八号港口',
        pLabel: '八号港口'
      }, {
        pValue: '九号港口',
        pLabel: '九号港口'
      }, {
        pValue: '十号港口',
        pLabel: '十号港口'
      }, {
        pValue: '十一号港口',
        pLabel: '十一号港口'
      } ],
      pValue:''
    }
  },
  created() {

    this.request.get("/user/username/"+this.user.username).then(res =>{
      if(res.code === '200'){
        this.user = res.data
        if(this.user.type ==='1') {
          this.visibleAdd=''
          this.visibleDelBth='none'
          this.visibleImport=''
          this.visibleEdit='none'
          this.visibleDel='none'
          this.load()
        }else{
          this.visibleAdd='none'
          this.visibleDelBth=''
          this.visibleImport='none'
          this.visibleEdit=''
          this.visibleDel=''
          this.load()
        }
      }
    })
    // this.reset()

  },
  methods:{
    reset(){
      this.shipName=""
      this.client=""
      this.type=""
      if(this.user.type==='2'){
        this.user.company=null
      }
      this.load()
    },

    save(){

      // this.form.company=this.user.company
      // this.request.post("/power-attorney",this.form).then(res =>{
      //   if(res){
      //     this.$message.success("保存成功")
      //     this.dialogFormVisible=false
      //     this.load()
      //   }else{
      //     this.$message.error("保存失败")
      //   }
      // });

      this.$refs['attorneyForm'].validate((valid)=>{
        if(valid){
          this.attorney.company=this.user.company
          this.request.post("/power-attorney",this.attorney).then(res =>{
            if(res){
              this.$message.success("保存成功")
              this.dialogFormVisible=false
              this.load()
            }else{
              this.$message.error("保存失败")
            }
          });
        }else{
          this.$message.error("请正确输入正确的相关信息")
          return false;
        }
      });

    },

    Edit(){
      this.getCurrentTime()
      this.form.manageTime=this.getTime
      this.form.manager=this.user.nickname
      this.request.post("/power-attorney",this.form).then(res =>{
        if(res){
          this.$message.success("审批成功")
          this.EditFormVisible=false
          this.load()
        }else{
          this.$message.error("审批失败")
        }
      })
    },

    handleDelete(id){
      this.request.delete("/power-attorney/"+id).then(res =>{
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
      this.request.get("/power-attorney/page",{
            params:{
              pageNum:this.pageNum,
              pageSize:this.pageSize,
              shipName:this.shipName,
              client:this.client,
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
      this.attorney={}
      this.attorney.client=this.user.username
      this.attorney.phone=this.user.phone
    },

    handleEdit(row){
      // this.form=row
      this.form= Object.assign({},row)
      this.EditFormVisible=true
      this.reset()
    },
    delBatch(){
      let ids=this.multipleSelection.map(v => v.id)  //[{},{},{}] =>[1,2,3] 对象数组变id数组
      this.request.post("/power-attorney/del/batch",ids).then(res =>{
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
      if(this.user.type === '2'){
        this.user.company=null
      }
      window.open("http://localhost:9090/power-attorney/export/"+this.user.company)
    },
    getCurrentTime() {
      //获取当前时间并打印
      let yy = new Date().getFullYear();
      let mm = new Date().getMonth()+1;
      let dd = new Date().getDate();
      let hh = new Date().getHours();
      let mf = new Date().getMinutes()<10 ? '0'+new Date().getMinutes() : new Date().getMinutes();
      let ss = new Date().getSeconds()<10 ? '0'+new Date().getSeconds() : new Date().getSeconds();
      this.getTime = yy+'/'+mm+'/'+dd+' '+hh+':'+mf+':'+ss;
    }
  }
}
</script>

<style>
.headerBg{
  background-color: #eee !important;
}
</style>