<template>
  <div class="app-container">
    <div style="margin: 0 auto; text-align: center; height: 80px">
      <span style="font-size: 40px"><b>配置试卷题目</b></span>
    </div>
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="Loading"
      border
      fit
      highlight-current-row
    >
      <el-table-column align="center" prop="created_at" label="勾选" width="80">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.selected" />
        </template>
      </el-table-column>
      <el-table-column align="center" label="ID" width="60">
        <template slot-scope="scope">
          {{ scope.row.quesId }}
        </template>
      </el-table-column>
      <el-table-column label="题型" width="95">
        <template slot-scope="scope">
          {{ scope.row.type }}
        </template>
      </el-table-column>
      <el-table-column label="题目">
        <template slot-scope="scope">
          {{ scope.row.descrip }}
        </template>
      </el-table-column>
      <el-table-column label="标签" width="95">
        <template slot-scope="scope">
          {{ scope.row.tag }}
        </template>
      </el-table-column>
      <el-table-column label="分数" width="50">
        <template slot-scope="scope">
          {{ scope.row.score }}
        </template>
      </el-table-column>
      <el-table-column label="答案" width="180" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.answer }}</span>
        </template>
      </el-table-column>
    </el-table>
    <div align="center" style="margin-top: 30px; margin-bottom: 20px">
      <el-button type="primary" @click="onSaveClicked">保存配置</el-button>
    </div>
  </div>
</template>

<script>
import {
  getQuestionList
} from '@/api/test/question'
import {
  getPaperQuestionsById,
  addQuestionToPaper
} from '@/api/test/paper'

function sleep(ms) {
  return new Promise(resolve =>
    setTimeout(resolve, ms)
  )
}

export default {
  data() {
    return {
      list: [],
      listLoading: false,
      paperId: -1
    }
  },
  created() {
    this.paperId = this.$route.query.paperId
    this.fetchData()
  },
  mounted() {
    if (window.history && window.history.pushState) {
      history.pushState(null, null, document.URL)
      window.addEventListener('popstate', this.back, false)
    }
  },
  destroyed() {
    window.removeEventListener('popstate', this.back, false)
  },
  methods: {
    fetchData() {
      this.listLoading = true
      getQuestionList().then(response => {
        const responseResult = response.data.responseMap.result
        for (let i = 0; i < responseResult.length; i++) {
          this.list.push({
            quesId: responseResult[i].quesId,
            type: responseResult[i].type,
            tag: responseResult[i].tag,
            descrip: responseResult[i].descrip,
            score: responseResult[i].score,
            answer: responseResult[i].answer,
            selected: false
          })
        }
        this.listLoading = false
      })
      sleep(200).then(() => {
        getPaperQuestionsById(this.paperId).then(response => {
          const responseResult = response.data.responseMap.questions
          for (let i = 0; i < responseResult.length; i++) {
            for (let j = 0; j < this.list.length; j++) {
              if (responseResult[i].quesId === this.list[j].quesId) {
                this.list[j].selected = true
              }
            }
          }
        })
      })
    },
    back() {
      this.$confirm('修改尚未保存，直接退出页面将不会作任何修改！是否确认退出？', '注意', {
        confirmButtonText: '保存并退出',
        cancelButtonText: '取消',
        distinguishCancelAndClose: true,
        center: true
      }).then(() => {
        const quesIds = []
        for (let i = 0; i < this.list.length; i++) {
          if (this.list[i].selected === true) {
            quesIds.push(this.list[i].quesId)
          }
        }
        addQuestionToPaper(this.paperId, quesIds).then(response => {
          this.$message({
            type: 'success',
            message: '保存成功!'
          })
          this.$router.push('/test_manage/paper_manage')
        })
      }).catch(() => {
        if (window.history && window.history.pushState) {
          history.pushState(null, null, document.URL)
          window.addEventListener('popstate', this.back, false)
        }
      })
    },
    onSaveClicked() {
      this.$confirm('确认保存试卷配置？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        const quesIds = []
        for (let i = 0; i < this.list.length; i++) {
          if (this.list[i].selected === true) {
            quesIds.push(this.list[i].quesId)
          }
        }
        addQuestionToPaper(this.paperId, quesIds).then(response => {
          this.$message({
            type: 'success',
            message: '保存成功!'
          })
          this.$router.push('/test_manage/paper_manage')
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消保存'
        })
      })
    }
  }
}
</script>
