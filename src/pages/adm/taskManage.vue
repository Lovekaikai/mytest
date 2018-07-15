<template>
  <section class="h100">
    <h2 class="main-title">
      任务管理
    </h2>
    <div class="condition" style="display:flex;align-items:center;">
      <span style="margin: 0 5px;white-space: nowrap">分类 </span>
      <el-select v-model="classify" placeholder="请选择分类">
        <el-option v-for="item in classifies" :key="item.value" :label="item.label" :value="item.value"></el-option>
      </el-select>
      <span style="margin: 0 5px;white-space: nowrap">危险源名称 </span>
      <el-select v-model="dangerName" placeholder="请选择危险源名称">
        <el-option v-for="item in dangers" :key="item.value" :label="item.label" :value="item.value"></el-option>
      </el-select>
      <span style="margin: 0 5px;white-space: nowrap">岗位角色 </span>
      <el-select v-model="role" placeholder="请选择岗位角色">
        <el-option v-for="item in roles" :key="item.value" :label="item.label" :value="item.value"></el-option>
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
          <el-table-column prop="danger_name" label="巡查人(岗位角色)">
          </el-table-column>
          <el-table-column prop="danger_code" label="巡逻时间点">
          </el-table-column>
          <el-table-column prop="danger_code" label="每时间点巡查次数">
          </el-table-column>
          <el-table-column prop="danger_code" label="报警消息接收人">
          </el-table-column>
          <el-table-column label="操作">
            <template slot-scope="scope">
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
    <el-dialog :title="dialogInfo.title" width="600px" :visible.sync="dialogInfo.show" id="build-edit">
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
          <el-input type="text" placeholder="请选择危险源" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>巡查岗位角色</el-col>
        <el-col :span="18">
          <el-input type="text" placeholder="请选择岗位角色源" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>班次配置</el-col>
        <el-col :span="18">
          <el-button>添加时间点</el-button>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>每时间点巡逻次数</el-col>
        <el-col :span="18">
          <el-input type="text" placeholder="" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>报警接收人</el-col>
        <el-col :span="18">
          <el-input type="text" placeholder="" style="min-width:250px;width:250px;"></el-input>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6" style="text-align: right;line-height:40px;">
          <span class="red">* </span>过滤时间段</el-col>
        <el-col :span="18">
          <el-checkbox-group v-model="editData.checkList">
            <el-checkbox label="法定节假日"></el-checkbox>
            <el-checkbox label="周末"></el-checkbox>
          </el-checkbox-group>
        </el-col>
      </el-row>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogInfo.show = false">取 消</el-button>
        <el-button type="primary">确 定</el-button>
      </span>
    </el-dialog>
    <!-- end dialong -->
  </section>
</template>
<script>
import Pagi from '@/components/pagi/'

export default {
  data () {
    return {
      classify: '',
      classifies: [{ label: '分类1', value: 1 }, { label: '分类2', value: 2 }, { label: '分类3', value: 3 }],
      dangerName: '',
      dangers: [{ label: '危险源1', value: 1 }, { label: '危险源2', value: 2 }, { label: '危险源3', value: 3 }],
      role: '',
      roles: [{ label: '角色1', value: 1 }, { label: '角色2', value: 2 }, { label: '角色3', value: 3 }],
      info: {
        data: [
          { classify: '分类1', place: '地点1', danger_name: '危险源1', danger_code: '1', remark: '备注1' },
          { classify: '分类1', place: '地点1', danger_name: '危险源1', danger_code: '1', remark: '备注1' },
          { classify: '分类1', place: '地点1', danger_name: '危险源1', danger_code: '1', remark: '备注1' },
          { classify: '分类1', place: '地点1', danger_name: '危险源1', danger_code: '1', remark: '备注1' },
          { classify: '分类1', place: '地点1', danger_name: '危险源1', danger_code: '1', remark: '备注1' }
        ],
        count: 20
      },
      page: 1,
      pageSize: 10,
      dialogInfo: {
        title: '新建巡检任务',
        show: false
      },
      editData: {
        checkList: []
      }
    }
  },
  components: {
    Pagi
  },
  methods: {
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
.el-row .el-checkbox-group {
  height: 40px;
  display: flex;
  align-items: center;
}
</style>
