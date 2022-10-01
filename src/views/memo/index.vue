<template>
  <d2-container>
    <template slot="Header">备忘录</template>
    <el-button type="success" @click="addItemDialogVisible = true" round>新增条目</el-button>
    <el-table
      readonly="true"
      :data="memoData"
      border
      height=100%
      style="width: 100%"
      :cell-style="cellStyle">
      <el-table-column
        prop="date"
        label="日期"
        width="180"
        align="center">
      </el-table-column>
      <el-table-column
        prop="name"
        label="备忘录名称"
        width="180"
        align="center">
      </el-table-column>
      <el-table-column
        prop="description"
        label="描述"
        align="center">
      </el-table-column>
      <el-table-column
        prop="state"
        label="状态"
        align="center">
      </el-table-column>
      <el-table-column
        fixed="right"
        prop="operate"
        label="操作"
        align="center">
        <template slot-scope="scope">
          <el-button type="success" icon="el-icon-check" @click="memoDone(scope.$index,scope.row)" circle></el-button>
          <el-button type="info" icon="el-icon-edit" circle></el-button>
          <el-button type="danger" icon="el-icon-delete" @click="memoDelete(scope.$index,scope.row)" circle></el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog
      title="新增条目"
      :visible.sync="addItemDialogVisible"
      width="30%"
      center>
      <el-form ref="form" :model="form" :rules="rules" label-width="100px">
        <el-form-item label="活动名称" prop="name">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="活动描述" prop="description">
          <el-input v-model="form.description"></el-input>
        </el-form-item>
        <el-form-item label="活动时间" prop="date" required>
          <el-date-picker
            v-model="form.date"
            type="datetime"
            placeholder="选择日期时间">
          </el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button @click="addItemDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="onSubmit('form')">确 定</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </d2-container>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      addItemDialogVisible: false,
      rules: {
        name: [
          { required: true, message: '请输入活动名称', trigger: 'blur' },
          { min: 1, max: 10, message: '长度在 1 到 10 个字符', trigger: 'blur' }
        ],
        date: [
          { type: 'date', required: true, message: '请选择活动时间', trigger: 'change' }
        ],
        description: [
          { required: true, message: '请输入活动描述', trigger: 'blur' },
          { min: 1, max: 20, message: '长度在 1 到 20 个字符', trigger: 'blur' }
        ]
      },
      form: {
        name: '',
        description: '',
        date: ''
      },
      memoData: []
    }
  },
  methods: {
    onSubmit (formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          const dateTmp = new Date(this.form.date)
          this.memoData.push({
            date: dateTmp.toLocaleDateString() + ' ' + dateTmp.toLocaleTimeString(),
            name: this.form.name,
            description: this.form.description,
            state: '未完成'
          })
          this.addItemDialogVisible = false
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    memoDone (index, row) {
      console.log(index)
      this.memoData[index].state = '已完成'
    },
    memoDelete (index, row) {
      this.memoData.splice(index, 1)
    },
    cellStyle ({ row, column, rowIndex, columnIndex }) {
      // 状态列字体颜色
      if (row.state === '已完成' && columnIndex === 3) {
        return 'color: #0CB618'
      } else if (row.state === '未完成' && columnIndex === 3) {
        return 'color: #EA1B29'
      } else {
        return 'color: #1a1a1b'
      }
    }
  }
}
</script>

<style scoped>

</style>
