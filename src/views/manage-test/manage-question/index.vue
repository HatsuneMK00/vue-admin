<template>
  <div class="app-container">
    <el-button type="primary" style="margin-bottom: 10px;" @click="onCreateNewClicked">新建<i
      class="el-icon-plus el-icon--right"
    /></el-button>
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
      :visible="wordsDialog.visible"
      :title="wordsDialog.title"
      width="50%"
      center
      @close="wordsDialog.visible = false"
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
          <el-button @click="wordsDialog.visible = false">取 消</el-button>
          <el-button type="primary" @click="wordsDialogConfirmOnClicked">确 定</el-button>
        </span>
      </template>
    </el-dialog>

  </div>
</template>

<script>
import { getList } from '@/api/table'
import { deleteQuestionById, submitWordsDialogResult } from '@/api/test/question'

export default {
  data() {
    return {
      list: null,
      listLoading: true,
      wordsDialog: {
        visible: false,
        title: '',
        changeMode: 'add' // has two value: 'add' and 'update'
      },
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
    onCreateNewClicked() {
      this.wordsDialog.title = '新增题目'
      this.form.questionDescription = ''
      this.form.questionAnswer = ''
      this.form.questionTag = ''
      this.wordsDialog.visible = true
      this.wordsDialog.changeMode = 'add'

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
      this.wordsDialog.title = '编辑题目'
      this.form.questionDescription = this.list[question_index].title
      this.form.questionAnswer = this.list[question_index].title
      this.form.questionTag = this.list[question_index].pageviews
      this.wordsDialog.visible = true
      this.wordsDialog.changeMode = 'update'
    },
    wordsDialogConfirmOnClicked() {
      const params = {
        changeMode: this.wordsDialog.changeMode
      }
      submitWordsDialogResult(params).then(response => {
        this.wordsDialog.visible = false
      })
    }
  }
}
</script>
