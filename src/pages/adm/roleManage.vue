<template>
  <section @click="hideRight" class="h100">
    <h2 class="main-title">
      岗位角色管理
    </h2>
    <div class="condition" style="display:flex;align-items:center;">
      <span style="margin: 0 5px;white-space: nowrap">分类 </span>
      <el-select v-model="classify" placeholder="请选择分类" style="min-width:130px;width:130px;">
        <el-option v-for="item in classifies" :key="item.value" :label="item.label" :value="item.value"></el-option>
      </el-select>
      <span style="margin: 0 5px;white-space: nowrap">危险源名称 </span>
      <el-select v-model="dangerName" placeholder="请选择危险源名称">
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
          <el-table-column prop="classify" label="分类">
          </el-table-column>
          <el-table-column prop="place" label="危险源名称">
          </el-table-column>
          <el-table-column prop="danger_name" label="岗位角色">
          </el-table-column>
          <el-table-column prop="danger_code" label="人员">
          </el-table-column>
          <el-table-column label="操作">
            <template slot-scope="scope">
              <el-button type="text" @click.stop="openRight(scope.row)">查看详情</el-button>
              <el-button type="text">修改</el-button>
              <el-button type="text">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </template>
    </div>
    <!-- end table -->
    <Pagi :page="page" :pageSize="pageSize" :count="info.count" @pageChange="pageChange" />
    <!-- end page -->
    <el-dialog :title="dialogInfo.title" width="500px" :visible.sync="dialogInfo.show" id="build-edit">
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>分类</el-col>
        <el-col :span="18">
          <el-select v-model="classify" placeholder="请选择分类" style="min-width:150px;width:150px;">
            <el-option v-for="item in classifies" :key="item.value" :label="item.label" :value="item.value"></el-option>
          </el-select>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>危险源</el-col>
        <el-col :span="18">
          <el-input type="text" placeholder="请输入危险源" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>岗位角色名称</el-col>
        <el-col :span="18">
          <el-input type="text" placeholder="请输入地点" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>人员</el-col>
        <el-col :span="18">
          <el-input type="text" placeholder="请输入源代码" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogInfo.show = false">取 消</el-button>
        <el-button type="primary">确 定</el-button>
      </span>
    </el-dialog>
    <!-- end dialong -->
    <Right :show="showRight" @hideRight="hideRight" title="任务详情">
      <template slot="content">
        <template v-for="(item, index) in viewDict">
          <div class="item" :key="index">
            <el-row :gutter="20">
              <el-col style="text-align: right;color:#9b9b9b;" :span="8">{{item.name}}</el-col>
              <el-col style="color:#4a4a4a;" :span="16">
                <div v-if="!item.format" v-html="$common.wrap(detail[item.key])"></div>
              </el-col>
            </el-row>
          </div>
        </template>
      </template>
      <template slot="btn">
        <el-button type="primary">修改</el-button>
        <el-button type="danger">删除</el-button>
      </template>
    </Right>
    <!-- end right -->
  </section>
</template>
<script>
import Pagi from '@/components/pagi/'
import Right from '@/components/right/'

export default {
  data () {
    return {
      classify: '',
      classifies: [{ label: '分类1', value: 1 }, { label: '分类2', value: 2 }, { label: '分类3', value: 3 }],
      dangerName: '',
      dangers: [{ label: '危险源1', value: 1 }, { label: '危险源2', value: 2 }, { label: '危险源3', value: 3 }],
      info: {
        data: [
          { classify: '分类1', danger_name: '危险源名称', role: '岗位角色', people: '人员1' },
          { classify: '分类1', danger_name: '危险源名称', role: '岗位角色', people: '人员1' },
          { classify: '分类1', danger_name: '危险源名称', role: '岗位角色', people: '人员1' },
          { classify: '分类1', danger_name: '危险源名称', role: '岗位角色', people: '人员1' },
          { classify: '分类1', danger_name: '危险源名称', role: '岗位角色', people: '人员1' }
        ],
        count: 20
      },
      page: 1,
      pageSize: 10,
      dialogInfo: {
        title: '新建岗位角色',
        show: false
      },
      viewDict: [
        { key: 'classify', name: '分类' },
        { key: 'danger_name', name: '危险源名称' },
        { key: 'role', name: '岗位角色' },
        { key: 'people', name: '人员' }
      ],
      detail: '',
      showRight: false
    }
  },
  components: {
    Pagi,
    Right
  },
  methods: {
    openRight (data) {
      this.detail = data
      this.showRight = true
    },
    hideRight () {
      this.showRight = false
    },
    pageChange (page) {
      this.page = page
    }
  },
  watch: {

  },
  created () {
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
