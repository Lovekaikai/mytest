<template>
  <section class="h100"  @click="hideRight;showRight = false">
    <h2 class="main-title">
      危险源管理
    </h2>
    <div class="condition" style="display:flex;align-items:center;">
      <span style="margin: 0 5px;white-space: nowrap">分类 </span>
      <el-select v-model="classify" placeholder="请选择分类" style="min-width:130px;width:130px;" @change="changeClassify">
        <el-option label="全部" value="0"></el-option>
        <el-option v-for="item in classifies" :key="item.value" :label="item.label" :value="item.value"></el-option>
      </el-select>
      <span style="margin: 0 5px;white-space: nowrap">危险源名称 </span>
      <el-select v-model="dangerName" placeholder="请选择危险源名称"  @change="changeDanger">
        <el-option v-for="item in dangers" :key="item.value" :label="item.label" :value="item.value"></el-option>
      </el-select>
      <div style="flex:1"></div>
      <div class="btn-area" style="display:flex;">
        <el-button type="primary" size="small" style="margin-left: 10px;font-size:13px;" @click="dialogInfo.show=true">新建</el-button>
      </div>
    </div>
    <!-- end cond -->
    <div class="table">
      <template>
        <el-table :data="info.data" border style="width: 100%">
          <el-table-column type="selection" width="40">
          </el-table-column>
          <el-table-column prop="danger_type_name" label="分类">
          </el-table-column>
          <el-table-column prop="danger_place" label="地点">
          </el-table-column>
          <el-table-column prop="danger_name" label="危险源名称">
          </el-table-column>
          <el-table-column prop="danger_id" label="危险源代码">
          </el-table-column>
          <el-table-column prop="remark" label="备注">
          </el-table-column>
          <el-table-column label="操作">
            <template slot-scope="scope">
              <el-dropdown trigger="hover" :show-timeout="0" @click.stop>
                <span class="el-dropdown-link blue" style="">
                  操作
                  <i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu slot="dropdown">
                  <el-dropdown-item @click.native.stop="lookDetail(scope.row)">查看详情</el-dropdown-item>
                  <el-dropdown-item @click.native.stop="modifyDanger(scope.row)">修改</el-dropdown-item>
                  <el-dropdown-item>绑定表单</el-dropdown-item>
                  <el-dropdown-item>二维码</el-dropdown-item>
                  <el-dropdown-item @click.native="delDanger(scope.row)">删除</el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </template>
          </el-table-column>
        </el-table>
      </template>
    </div>
    <!-- end table -->
    <Pagi :page="page" :pageSize="pageSize" :count="info.count" @pageChange="pageChange" />
    <!-- end page -->
    <el-dialog :title="dialogInfo.title" width="450px" :visible.sync="dialogInfo.show" id="build-edit">
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>分类</el-col>
        <el-col :span="18">
          <template>
            <el-select v-model="danger_type_id" placeholder="请选择">
              <el-option
                v-for="item in newClassifiecs"
                :key="item.value"
                :label="item.label"
                :value="item.value">
                <span style="float: left">{{ item.label }}</span>
                <div style="float: right; color: #409EFF; font-size: 13px">
                  <span @click.stop="editor(item)">编辑</span>&nbsp;<span style="color:#F56C6C" @click.stop="del(item.value)">删除</span>
                </div>
              </el-option>
            </el-select>
            <span style="color:#409EFF;cursor:pointer" @click="newClass">新建分类</span>
          </template>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>名称</el-col>
        <el-col :span="18">
          <el-input type="text" placeholder="请输入名称" v-model="danger_name" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>地点</el-col>
        <el-col :span="18">
          <el-input type="text" v-model="danger_place" placeholder="请输入地点" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>危险源代码</el-col>
        <el-col :span="18">
          <el-input type="text"  v-model="danger_code" placeholder="请输入源代码" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
  <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>巡查领导</el-col>
        <el-col :span="18">
               <div style="margin-bottom: 10px;">
              <template v-for="(person, index) in editInfo.member">
                <User style="margin: 0 5px 5px 0;" :key="person.id" :item="person" type="user" @removeTag="removeTag(index)" />
              </template>
          </div>
          <div style="margin-bottom: 15px;">
            <el-button type="primary" size="mini" @click="showDept = true">添加人员</el-button>
          </div>
        </el-col>
      </el-row>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>备注</el-col>
        <el-col :span="18">
          <el-input type="textarea" v-model="remark_text" placeholder="请输入备注" style="min-width:250px;width:250px;">
          </el-input>
        </el-col>
      </el-row>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogInfo.show = false">取 消</el-button>
        <el-button type="primary" v-if="flag">确 定</el-button>
        <el-button type="primary" v-if="!flag" @click="newDangerTask">确 定</el-button>
      </span>
    </el-dialog>
    <!-- end dialong -->
    <el-dialog
      :title="dialogClass.title"
      :visible.sync="dialogClass.show"
      width="40%">
      <el-input v-model="editorInfo.label" style="width:80%"  placeholder="请输入内容"></el-input>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogClass.show = false">取 消</el-button>
        <el-button type="primary" @click="editorClass" v-if="classflag">确 定</el-button>
        <el-button type="primary" @click="addClass" v-if="!classflag">确 定</el-button>
      </span>
    </el-dialog>
        <Right :show="showRight" @hideRight="hideRight" title="危险源详情">
      <template slot="content">
            <div class="item">
            <el-row :gutter="20">
              <el-col style="text-align: right;color:#9b9b9b;" :span="8">分类</el-col>
              <el-col style="color:#4a4a4a;" :span="16">
                {{infoDetail.danger_type_name}}
              </el-col>
            </el-row>
           <el-row :gutter="20">
              <el-col style="text-align: right;color:#9b9b9b;" :span="8">地点</el-col>
              <el-col style="color:#4a4a4a;" :span="16">
                {{infoDetail.danger_place}}
              </el-col>
            </el-row>
             <el-row :gutter="20">
              <el-col style="text-align: right;color:#9b9b9b;" :span="8">危险源名称</el-col>
              <el-col style="color:#4a4a4a;" :span="16">
                {{infoDetail.danger_name}}
              </el-col>
            </el-row>
             <el-row :gutter="20">
              <el-col style="text-align: right;color:#9b9b9b;" :span="8">危险源代码</el-col>
              <el-col style="color:#4a4a4a;" :span="16">
                      {{infoDetail.danger_id}}
              </el-col>
            </el-row>
             <el-row :gutter="20">
              <el-col style="text-align: right;color:#9b9b9b;" :span="8">巡检领导</el-col>
              <el-col style="color:#4a4a4a;" :span="16">
                还没填
              </el-col>
            </el-row>
                <el-row :gutter="20">
              <el-col style="text-align: right;color:#9b9b9b;" :span="8">备注</el-col>
              <el-col style="color:#4a4a4a;" :span="16">
                {{infoDetail.remark}}
              </el-col>
            </el-row>
          </div>
      </template>
      <template slot="btn">
        <el-button type="primary" >绑定表单</el-button>
        <el-button type="primary" @click.native.stop="modify(detail)">修改</el-button>
        <el-button type="danger" @click.native.stop="delDanger(infoDetail)">删除</el-button>
      </template>
    </Right>
        <Dept model="user" :visible="showDept" :selected="{user: editInfo.member}"  @confirm="confirm" @updateVisible="updateVisible" />
  </section>
</template>
<script>
import Pagi from '@/components/pagi/'
import common from '@/common/http'
import Dept from '@/pages/dept/'
import User from '@/pages/dept/user'
import Right from '@/components/right/'
export default {
  data () {
    return {
      remark_text:'',
      danger_code:'',
      danger_place:'',
      classify: '',
      danger_type_id:'',
      newClassifiecs:[],
      classifies: [],
      dangerName: '',
      danger_name:'',
      dangers: [],
      info: {
        data: [
       
        ],
        count: 20
      },
      page: 1,
      pageSize: 10,
      dialogInfo: {
        title: '新建危险源',
        show: false
      },
      dialogClass:{
        title:"新建分类",
        show:false
      },
      classflag:0,
      flag:0,
      editorInfo:{
        label:'',
        value:''
      },
      editInfo:{
        member:[]
      },
      infoDetail:{},
      showDept:false,
      showRight:false
    }
  },
  components: {
    Pagi,
    Dept,
    User,
    Right
  },
  methods: {
    hideRight () {
      this.showRight = false
    },
    updateVisible (val) {
      this.showDept = val
    },
    confirm (choosen) {
      this.editInfo.member = choosen.user
    },
    removeTag (index) {
      this.editInfo.member.splice(index, 1)
    },
    newClass(){
      this.classflag=0
      this.dialogClass.show=true
    },
    addClass(){
      let data={
        danger_type_name:this.editorInfo.label,
        danger_type_id:0
      }
      common.req("/index.php?model=jlsafe&m=danger&a=index&cmd=102",data,(res)=>{
          if (res.data.errcode === '0') {
              this.$message({
                message: '分类新建成功',
                type: 'success',
                duration: 1000
              })
              this.dialogClass.show=false
              this.getType()
          }else{
                this.$message({
                message: '分类新建失败',
                type: 'error',
                duration: 1000
              })
            this.dialogClass.show=false                                                                                  
          }
      })
    },
    editorClass(){
        let data={
          danger_type_name:this.editorInfo.label,
          danger_type_id:this.editorInfo.value
        }
       common.req("/index.php?model=jlsafe&m=danger&a=index&cmd=102",data,(res)=>{
        if (res.data.errcode === '0') {
            this.$message({
                message: '分类修改成功',
                type: 'success',
                duration: 1000
          })
           this.dialogClass.show=false
           this.getType()
        }else{
           this.$message({
                message: '分类新建失败',
                type: 'error',
                duration: 1000
              })
          this.dialogClass.show=false
        }
       })
    },
    editor(item){
      this.classflag = 1
      this.editorInfo = item
      this.dialogClass={
        title:"修改分类",
        show:true
      }
    },
    del(id){
    this.$confirm('你确定要删除此分类吗？, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning',
          center: true
        }).then(() => {
    let data={
        danger_type_id:id
      }
      console.log()
        common.req("/index.php?model=jlsafe&m=danger&a=index&cmd=103",data,(res)=>{
          if (res.data.errcode === '0') {
              this.$message({
                type: 'success',
                message: '删除成功!'
              });
              this.getType()
            }else{
               this.$message({
                type: 'error',
                message: '删除失败!'
              });
            }
          })

        })
    },
    newDangerTask(){
      this.flag=0
      if(common.trim(this.danger_type_id).length===0){
        this.$message({
            type: 'warning',
            message: '分类内容不能为空!'
        });
        return false
      }
      if(common.trim(this.danger_name).length===0){
         this.$message({
            type: 'warning',
            message: '危险源不能为空!'
        });
      return false
      }
      if(common.trim(this.danger_place).length===0){
         this.$message({
            type: 'warning',
            message: '地点不能为空!'
        });
        return false
      }
      console.log(this.danger_code)
      if(common.trim(this.danger_code).length===0){
         this.$message({
            type: 'warning',
            message: '危险源代码不能为空!'
        });
      return false
      }
      if(this.editInfo.member.length===0){
        this.$message({
            type: 'warning',
            message: '巡查领导不能为空!'
        });
        return false
      }
      if(common.trim(this.remark_text).length===0){
         this.$message({
            type: 'warning',
            message: '备注内容不能为空!'
        });
             return false
      }
      let memberArr = []
      for (let j = 0; j < this.editInfo.member.length; j++) {
        memberArr.push(this.editInfo.member[j].id)
      }
      let data={
        danger_type_id:this.danger_type_id,
        danger_name:this.danger_name,
        danger_place:this.danger_place,
        danger_id:this.danger_code,
        patrol_lead:memberArr,
        remark:this.remark_text
      }
      common.req("/index.php?model=jlsafe&m=danger&a=index&cmd=106",data,(res)=>{
         if (res.data.errcode === '0') {
          this.$message({
            type: 'success',
            message: '新建危险源成功!'
            });
             this.dialogInfo.show=false
             this.getTableList()
             this.not_empty()
         }else{
            this.$message({
            type: 'error',
            message: '新建危险源失败'
            });
          this.dialogInfo.show=false
         }
      })
    },
    modifyDanger(row){
      // this.danger_type_id=row.danger_type_id
      // this.danger_name=this.danger_name
      // this.danger_place=this.danger_place
      // this.danger_code=this.danger_code
      // this.editInfo={}
      // this.remark=this.remark_text
      // common.req("/index.php?model=jlsafe&m=danger&a=index&cmd=106",data,()=>{
      //   if (res.data.errcode === '0') {
      //     this.$message({
      //       type: 'success',
      //       message: '修改危险源成功!'
      //       });
      //        this.dialogInfo.show=false
      //        this.getTableList()
      //        this.not_empty()
      //    }else{
      //       this.$message({
      //       type: 'error',
      //       message: '修改危险源失败'
      //       });
      //     this.dialogInfo.show=false
      //    }
      // })
    },
    delDanger(row){
      this.$confirm('你确定要删除此危险源？, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning',
          center: true
        }).then(() => {
        let data={
        danger_id:row.danger_id
      }
      common.req("/index.php?model=jlsafe&m=danger&a=index&cmd=109",data,(res)=>{
          if (res.data.errcode === '0') {
              this.$message({
                type: 'success',
                message: '删除成功!'
              });
              this.getTableList()
            }else{
               this.$message({
                type: 'error',
                message: '删除失败!'
              });
            }
          })

        })
    },
    lookDetail(data){
      this.infoDetail=data
      console.log(data)
      this.showRight = true
    },
    pageChange (page) {
      this.page = page
      this.getTableList()
    },
    getType () {
      let that = this
      common.req(
        '/index.php?model=jlsafe&m=danger&a=index&cmd=101', {},
        res => {
          if (res.data.errcode === '0') {
            let data = res.data.data
            let arr = []
            for (let index in data) {
              let obj = {
                label: data[index].danger_type_name,
                value: data[index].danger_type_id
              }
              arr.push(obj)
            }
            that.classifies = arr
            that.newClassifiecs = arr
          }
        }
      )
    },
    getTableList(){
      let data={
        danger_type_id:this.classify,
        danger_id:this.dangerName,
        page:this.page,
        page_size:10
      }
      common.req("/index.php?model=jlsafe&m=danger&a=index&cmd=105",data,(res)=>{
        if (res.data.errcode === '0') {
          this.info.data=res.data.data
        }
      })
    },
    not_empty(){
      this.danger_type_id = ''
      this.danger_name = ''
      this.danger_place = ''
      this.danger_code = ''
      this.editInfo = {
        member:[]
      }
      this.remark_text = ''
    },
    changeDanger(val){
      this.getTableList()
    },
    changeClassify (val) {
      if (val === '0') {
        this.dangerName = ''
        this.dangers = []
        this.getTableList()
      } else {
        this.getTableList()
        this.dangerList(val)
      }
    },
    dangerList (val) {
      let that = this
      let data = {
        danger_type_id: val,
        danger_id: 0,
        page: 1,
        page_size: 100000
      }
      common.req(
        '/index.php?model=jlsafe&m=danger&a=index&cmd=105',
        data,
        res => {
          if (res.data.errcode === '0') {
            let data = res.data.data
            let arr = []
            for (let index in data) {
              let obj = {
                label: data[index].danger_name,
                value: data[index].danger_id
              }
              arr.push(obj)
            }
            that.dangers = arr
          }
        }
      )
    },
  },
  watch: {

  },
  created () {
    this.getType()
    this.getTableList()
  }
}
</script>
<style lang="less" scoped>
.condition {
  font-size: 14px;
  margin-bottom: 10px;
}
.el-input {
  width: 150px;
}
.table {
  margin: 25px 0;
}
.el-row {
  margin: 10px 0;
}
.lh28 {
  line-height: 28px;
}
pre {
  color: inherit;
  // line-height: 18px;
  font-family: inherit;
}
</style>
<style lang="less">
#edit {
  .el-dialog__body {
    padding: 10px 25px;
  }
  .el-input__inner {
    padding: 0 2px;
    font-size: 14px;
  }
}
</style>
