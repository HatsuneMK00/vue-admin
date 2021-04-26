<template>
  <div class="app-container">
    <el-row>
      <el-col span="6">
        <el-button type="primary" style="margin-bottom: 10px;" @click="onCreateNewClicked">新建<i
          class="el-icon-plus el-icon--right"
        /></el-button>
      </el-col>
      <el-col span="6" offset="12">
        <el-radio-group v-model="radio1">
          <el-radio-button label="随机生成"></el-radio-button>
          <el-radio-button label="勾选生成"></el-radio-button>
        </el-radio-group>
      </el-col>
    </el-row>
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
          {{ scope.row.testOptionId }}
        </template>
      </el-table-column>
      <el-table-column label="考试名称">
        <template slot-scope="scope">
          {{ scope.row.testOptionName }}
        </template>
      </el-table-column>
<!--      <el-table-column label="选择题数目" width="110" align="center">-->
<!--        <template slot-scope="scope">-->
<!--          {{ scope.row.selectNum }}-->
<!--        </template>-->
<!--      </el-table-column>-->
<!--      <el-table-column label="判断题数目" width="110" align="center">-->
<!--        <template slot-scope="scope">-->
<!--          {{ scope.row.judgeNum }}-->
<!--        </template>-->
<!--      </el-table-column>-->
<!--      <el-table-column label="简答题数目" width="110" align="center">-->
<!--        <template slot-scope="scope">-->
<!--          {{ scope.row.qaNum }}-->
<!--        </template>-->
<!--      </el-table-column>-->
      <el-table-column label="开始时间" width="110" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.startDate }}</span>
        </template>
      </el-table-column>
      <el-table-column label="时长" width="110" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.duration }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="操作" width="200">
        <template slot-scope="scope">
          <el-button-group>
            <el-button type="primary" icon="el-icon-edit" @click="onEditClicked(scope.row.testOptionId, scope.$index)" />
            <el-button type="danger" icon="el-icon-delete" @click="onDeleteClicked(scope.row.testOptionId, scope.$index)" />
            <el-button type="warning" icon="el-icon-s-custom" @click="onPickClicked(scope.row.testOptionId, scope.$index)" />
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
        <el-form-item label="考试名称" label-width="120px">
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
          <el-button type="primary" @click="wordsDialogConfirmOnClicked">确 定</el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script>
import { getList, addPaper, changePaperById, deletePaperById, submitWordsDialogResult } from '@/api/test/exam'

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
        index: -1,
        paperId: -1,
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
        this.list = response.data.responseMap.result
        this.listLoading = false
      })
    },
    onCreateNewClicked() {
      this.wordsDialog.title = '新增考试'
      this.form.paperName = ''
      this.form.paperSelectNum = 0
      this.form.paperJudgeNum = 0
      this.form.paperQaNum = 0
      this.form.duration = 0
      this.wordsDialog.visible = true
      this.wordsDialog.changeMode = 'add'
    },
    onDeleteClicked(paper_id, paper_index) {
      deletePaperById({ testOptionId: paper_id }).then(response => {
        if (response.data.status === 200) {
          console.log('delete paper success')
          this.list.splice(paper_index, 1)
        } else {
          alert('删除失败')
        }
      })
    },
    onEditClicked(paper_id, paper_index) {
      this.wordsDialog.title = '编辑考试'
      this.form.paperId = paper_id
      this.form.index = paper_index
      this.form.paperName = this.list[paper_index].testOptionName
      this.form.paperSelectNum = this.list[paper_index].selectNum
      this.form.paperJudgeNum = this.list[paper_index].judgeNum
      this.form.paperQaNum = this.list[paper_index].qaNum
      this.form.duration = this.list[paper_index].duration
      this.wordsDialog.visible = true
      this.wordsDialog.changeMode = 'update'
    },

    onPickClicked(exam_id, exam_index) {
      this.$router.push({
        path: '/test_manage/exam_manage/pick_user',
        query: {
          exam_id: exam_id
        }
      })
    },

    wordsDialogConfirmOnClicked() {
      const params = {
        id: this.form.paperId,
        index: this.form.index,
        name: this.form.paperName,
        selectNum: this.form.paperSelectNum,
        judgeNum: this.form.paperJudgeNum,
        qaNum: this.form.paperQaNum,
        duration: this.form.duration,
        changeMode: this.wordsDialog.changeMode
      }
      submitWordsDialogResult(params).then(response => {
        const index = this.form.index
        const changeMode = this.wordsDialog.changeMode
        const temp = {
          testOptionName: this.form.paperName,
          selectNum: this.form.paperSelectNum,
          judgeNum: this.form.paperJudgeNum,
          qaNum: this.form.paperQaNum,
          duration: this.form.duration
        }
        if (changeMode === 'update') {
          if (index != null && index >= 0) {
            const id = this.form.paperId
            this.list[index].testOptionName = this.form.paperName
            this.list[index].selectNum = this.form.paperSelectNum
            this.list[index].judgeNum = this.form.paperJudgeNum
            this.list[index].qaNum = this.form.paperQaNum
            this.list[index].duration = this.form.duration
            changePaperById(id, temp).then(response => {
              console.log('Updated paper' + temp)
            })
          }
        } else if (changeMode === 'add') {
          console.log('3')
          addPaper(temp).then(response => {
            console.log('Create new paper' + temp)
            this.list.push({
              testOptionId: response.data.responseMap.result,
              testOptionName: this.form.paperName,
              selectNum: this.form.paperSelectNum,
              judgeNum: this.form.paperJudgeNum,
              qaNum: this.form.paperQaNum,
              duration: this.form.duration
            })
          })
        }
        this.wordsDialog.visible = false
      })
    }
  }
}
</script>
