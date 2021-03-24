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
      <el-table-column align="center" label="试卷ID" width="95">
        <template slot-scope="scope">
          {{ scope.$index }}
        </template>
      </el-table-column>
      <el-table-column label="试卷名称">
        <template slot-scope="scope">
          {{ scope.row.title }}
        </template>
      </el-table-column>
      <el-table-column label="选择题数目" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.pageviews }}
        </template>
      </el-table-column>
      <el-table-column label="判断题数目" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.pageviews }}
        </template>
      </el-table-column>
      <el-table-column label="简答题数目" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.pageviews }}
        </template>
      </el-table-column>
      <el-table-column label="时长" width="110" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.pageviews }}</span>
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
          <el-input v-model="form.paperName" autocomplete="off" />
        </el-form-item>
        <el-form-item label="选择题数目" label-width="120px">
          <el-input v-model="form.paperSelectNum" autocomplete="off" />
        </el-form-item>
        <el-form-item label="判断题数目" label-width="120px">
          <el-input v-model="form.paperJudgeNum" autocomplete="off" />
        </el-form-item>
        <el-form-item label="简答题数目" label-width="120px">
          <el-input v-model="form.paperQaNum" autocomplete="off" />
        </el-form-item>
        <el-form-item label="时长" label-width="120px">
          <el-input v-model="form.duration" autocomplete="off" />
        </el-form-item>
      </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="wordsDialog.visible = false">取 消</el-button>
          <el-button type="primary" @click="wordsDilogComfirmOnClick">确 定</el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script>
import { getList } from '@/api/table'
import { deletePaperById, submitWordsDialogResult } from '@/api/test/paper'

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
        paperName: '',
        paperSelectNum: 0,
        paperJudgeNum: 0,
        paperQaNum: 0,
        duration: 0
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
      this.wordsDialog.title = '新增试卷'
      this.form.paperName = ''
      this.form.paperSelectNum = 0
      this.form.paperJudgeNum = 0
      this.form.paperQaNum = 0
      this.form.duration = 0
      this.wordsDialog.visible = true
      this.wordsDialog.changeMode = 'add'
    },
    onDeleteClicked(paper_id, paper_index) {
      deletePaperById(paper_id).then(response => {
        // if (response.data.result === 200) {
        // eslint-disable-next-line no-constant-condition
        if (true) {
          console.log('delete paper success')
          this.list.splice(paper_index, 1)
        } else {
          // console.log('删除失败')
        }
      })
    },
    onEditClicked(paper_id, paper_index) {
      this.wordsDialog.title = '编辑试卷'
      this.form.paperName = this.list[paper_index].title
      this.form.paperSelectNum = this.list[paper_index].pageviews
      this.form.paperJudgeNum = this.list[paper_index].pageviews
      this.form.paperQaNum = this.list[paper_index].pageviews
      this.form.duration = this.list[paper_index].pageviews
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
