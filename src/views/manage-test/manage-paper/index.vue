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
      :visible="wordsDialogVisible"
      :title="wordsDialogTitle"
      width="50%"
      center
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
          <el-button @click="wordsDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="wordsDialogVisible = false">确 定</el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script>
import { getList } from '@/api/table'
import { deletePaperById } from '@/api/test/paper'

export default {
  data() {
    return {
      list: null,
      listLoading: true,
      wordsDialogVisible: false,
      wordsDialogTitle: '',
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
      this.wordsDialogTitle = '编辑试卷'
      this.form.paperName = this.list[paper_index].title
      this.form.paperSelectNum = this.list[paper_index].pageviews
      this.form.paperJudgeNum = this.list[paper_index].pageviews
      this.form.paperQaNum = this.list[paper_index].pageviews
      this.form.duration = this.list[paper_index].pageviews
      this.wordsDialogVisible = true
    }
  }
}
</script>
