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
      <el-table-column align="center" label="考试ID" width="95">
        <template slot-scope="scope">
          {{ scope.$index }}
        </template>
      </el-table-column>
      <el-table-column label="考试名称">
        <template slot-scope="scope">
          {{ scope.row.title }}
        </template>
      </el-table-column>
      <el-table-column label="开始时间" width="110" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.pageviews }}</span>
        </template>
      </el-table-column>
      <el-table-column label="时长" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.pageviews }}
        </template>
      </el-table-column>
      <el-table-column label="总分" width="110" align="center">
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
        <el-form-item label="试卷名称" label-width="120px">
          <el-input v-model="form.examName" autocomplete="off" />
        </el-form-item>
        <el-form-item label="开始时间" label-width="120px">
          <el-input v-model="form.startTime" autocomplete="off" />
        </el-form-item>
        <el-form-item label="时长" label-width="120px">
          <el-input v-model="form.duration" autocomplete="off" />
        </el-form-item>
        <el-form-item label="总分" label-width="120px">
          <el-input v-model="form.totalScore" autocomplete="off" />
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
import { deleteExamById, submitWordsDialogResult } from '@/api/test/exam'

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
        examName: '',
        startTime: null,
        duration: 0,
        totalScore: 0
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
      this.wordsDialog.title = '新增考试'
      this.form.examName = ''
      this.form.startTime = ''
      this.form.duration = ''
      this.form.totalScore = ''
      this.wordsDialog.visible = true
      this.wordsDialog.changeMode = 'add'

      // var parsedobj = JSON.parse(JSON.stringify(this.form))

    },
    onDeleteClicked(exam_id, exam_index) {
      deleteExamById(exam_id).then(response => {
        // if (response.data.result === 200) {
        // eslint-disable-next-line no-constant-condition
        if (true) {
          console.log('delete exam success')
          this.list.splice(exam_index, 1)
        } else {
          // console.log('删除失败')
        }
      })
    },
    onEditClicked(exam_id, exam_index) {
      this.wordsDialog.title = '编辑考试'
      this.form.examName = this.list[exam_index].title
      this.form.startTime = this.list[exam_index].pageviews
      this.form.duration = this.list[exam_index].pageviews
      this.form.totalScore = this.list[exam_index].pageviews
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
