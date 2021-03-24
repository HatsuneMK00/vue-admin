<template>
  <div class="app-container">
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="Loading"
      border
      fit
      highlight-current-row
    >
      <el-table-column align="center" label="题目ID" width="95">
        <template slot-scope="scope">
          {{ scope.$index }}
        </template>
      </el-table-column>
      <el-table-column label="题目描述">
        <template slot-scope="scope">
          {{ scope.row.title }}
        </template>
      </el-table-column>
      <el-table-column label="题目答案">
        <template slot-scope="scope">
          {{ scope.row.title }}
        </template>
      </el-table-column>
      <el-table-column label="题目分值" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.pageviews }}
        </template>
      </el-table-column>
      <el-table-column label="题目标签" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.pageviews }}
        </template>
      </el-table-column>
      <el-table-column label="题型" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.pageviews }}
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="操作" width="200">
        <template slot-scope="scope">
          <el-button-group>
            <el-button type="primary" icon="el-icon-edit" @click="onEditClicked(scope.$index, scope.$index)" />
            <el-button type="danger" icon="el-icon-delete" @click="onDeleteClicked(scope.$index, scope.$index)" />
          </el-button-group>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog
      :visible="wordsDialogVisible"
      :title="wordsDialogTitle"
      width="50%"
      center
    >
      <el-form :model="form">
        <el-form-item label="题目描述" label-width="120px">
          <el-input v-model="form.questionDescription" autocomplete="off" />
        </el-form-item>
        <el-form-item label="题目回答" label-width="120px">
          <el-input v-model="form.questionAnswer" autocomplete="off" />
        </el-form-item>
        <el-form-item label="题目标签" label-width="120px">
          <el-input v-model="form.questionTag" autocomplete="off" />
        </el-form-item>
      </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="wordsDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="wordsDialogVisible = false">确 定</el-button>
        </span>
      </template>
    </el-dialog>

  </div>
</template>

<script>
import { getList } from '@/api/table'
import { deleteQuestionById } from '@/api/test/question'

export default {
  data() {
    return {
      list: null,
      listLoading: true,
      wordsDialogVisible: false,
      wordsDialogTitle: '',
      form: {
        questionDescription: '',
        questionAnswer: '',
        questionTag: ''
      }
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      this.listLoading = true
      getList().then(response => {
        this.list = response.data.items
        this.listLoading = false
      })
    },
    onDeleteClicked(question_id, question_index) {
      deleteQuestionById(question_id).then(response => {
        // if (response.data.result === 200) {
        // eslint-disable-next-line no-constant-condition
        if (true) {
          console.log('delete question success')
          this.list.splice(question_index, 1)
        } else {
          // console.log('删除失败')
        }
      })
    },
    onEditClicked(question_id, question_index) {
      this.wordsDialogTitle = '编辑题目'
      this.form.questionDescription = this.list[question_index].title
      this.form.questionAnswer = this.list[question_index].title
      this.form.questionTag = this.list[question_index].pageviews
      this.wordsDialogVisible = true
    }
  }
}
</script>
