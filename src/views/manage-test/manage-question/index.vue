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
          {{ scope.row.quesId }}
        </template>
      </el-table-column>
      <el-table-column label="题目描述">
        <template slot-scope="scope">
          {{ scope.row.descrip }}
        </template>
      </el-table-column>
      <el-table-column label="题目答案">
        <template slot-scope="scope">
          {{ scope.row.answer }}
        </template>
      </el-table-column>
      <el-table-column label="题目分值" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.score }}
        </template>
      </el-table-column>
      <el-table-column label="题目标签" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.tag }}
        </template>
      </el-table-column>
      <el-table-column label="题型" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.type }}
        </template>
      </el-table-column>
      <el-table-column label="题目图片" width="110" align="center">
        <template slot-scope="scope">
          <div class="demo-image__preview">
            <el-image
              style="width: 100px; height: 100px"
              :src="scope.row.image"
              :preview-src-list="scope.row.image">
            </el-image>
          </div>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="操作" width="200">
        <template slot-scope="scope">
          <el-button-group>
            <el-button type="primary" icon="el-icon-edit" @click="onEditClicked(scope.row.quesId, scope.$index)" />
            <el-button type="danger" icon="el-icon-delete" @click="onDeleteClicked(scope.row.quesId, scope.$index)" />
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
        <el-form-item label="题型" label-width="120px">
          <el-radio-group v-model="form.questionType" size="small">
            <el-radio-button label="选择题"></el-radio-button>
            <el-radio-button label="判断题"></el-radio-button>
            <el-radio-button label="问答题"></el-radio-button>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="分值" label-width="120px">
          <el-input v-model="form.questionScore" autocomplete="off" />
        </el-form-item>
        <el-form-item label="题目标签" label-width="120px">
          <el-input v-model="form.questionTag" autocomplete="off" />
        </el-form-item>
        <el-form-item label="试题图片" label-width="120px">
          <el-upload
            v-model="form.questionImage"
            class="upload-demo"
            drag
            action="https://jsonplaceholder.typicode.com/posts/"
            multiple>
            <i class="el-icon-upload"></i>
            <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
            <div class="el-upload__tip" slot="tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
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

import {
  addQuestion,
  changeQuestionById,
  deleteQuestionById,
  getQuestionList,
  submitWordsDialogResult
} from '@/api/test/question'

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
      mediaDialog: {
        visible: false,
        contentVisible: true,
        title: ''
      },
      form: {
        index: -1,
        questionId: -1,
        questionDescription: '',
        questionAnswer: '',
        questionTag: '',
        questionScore: 0,
        questionType: '选择题',
        questionImage: ''
      },
      selectValue: ''
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      this.listLoading = true
      getQuestionList().then(response => {
        this.list = response.data.responseMap.result
        this.listLoading = false
      })
    },
    onCreateNewClicked() {
      this.wordsDialog.title = '新增题目'
      this.form.questionDescription = ''
      this.form.questionAnswer = ''
      this.form.questionTag = ''
      this.form.questionType = ''
      this.form.questionImage = ''
      this.form.questionScore = 0
      this.wordsDialog.visible = true
      this.wordsDialog.changeMode = 'add'
    },
    onDeleteClicked(question_id, question_index) {
      deleteQuestionById({quesId: question_id}  ).then(response => {
        if (response.data.status === 200) {
          console.log('delete question success')
          this.list.splice(question_index, 1)
        } else {
          alert('删除失败')
        }
      })
    },
    onEditClicked(question_id, question_index) {
      this.wordsDialog.title = '编辑题目'
      this.form.questionId = question_id
      this.form.index = question_index
      this.form.questionDescription = this.list[question_index].descrip
      this.form.questionAnswer = this.list[question_index].answer
      this.form.questionTag = this.list[question_index].tag
      this.form.questionType = this.list[question_index].type
      this.form.questionScore = this.list[question_index].score
      this.form.questionImage = this.list[question_index].image
      this.wordsDialog.visible = true
      this.wordsDialog.changeMode = 'update'
    },
    wordsDialogConfirmOnClicked() {
      const params = {
        id: this.form.questionId,
        index: this.form.index,
        descrip: this.form.questionDescription,
        answer: this.form.questionAnswer,
        score: this.form.questionScore,
        type: this.form.questionType,
        tag: this.form.questionTag,
        image: this.form.questionImage,
        changeMode: this.wordsDialog.changeMode
      }
      console.log("1")
      submitWordsDialogResult(params).then(response => {
        console.log("2")
        const index = this.form.index
        const changeMode = this.wordsDialog.changeMode
        const id = response.data.quesId
        const temp = {
          descrip: this.form.questionDescription,
          answer: this.form.questionAnswer,
          score: this.form.questionScore,
          type: this.form.questionType,
          tag: this.form.questionTag,
          image: this.form.questionImage
        }
        if (changeMode === 'update') {
          if (index != null && index >= 0) {
            const id = this.form.questionId
            this.list[index].descrip = this.form.questionDescription
            this.list[index].answer = this.form.questionAnswer
            this.list[index].score = this.form.questionScore
            this.list[index].type = this.form.questionType
            this.list[index].tag = this.form.questionTag
            this.list[index].image = this.form.questionImage
            changeQuestionById(id, temp).then(response => {
              console.log('Updated user' + temp)
            })
          }
        } else if (changeMode === 'add') {
          console.log("3")
          addQuestion(temp).then(response => {
            console.log('Create new question' + temp)
          })
          this.list.push({
            quesId: id,
            descrip: this.form.questionDescription,
            answer: this.form.questionAnswer,
            type: this.form.questionType,
            score: this.form.questionScore,
            tag: this.form.questionTag,
            image: this.form.questionImage
          })
        }
        this.wordsDialog.visible = false
      })
    }
  }
}
</script>
