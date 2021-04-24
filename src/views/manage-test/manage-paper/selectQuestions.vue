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
import { getQuestionList } from '@/api/test/question'

export default {
  data() {
    return {
      list: [],
      listLoading: false
    }
  },
  created() {
    this.fetchData()
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
    },
    onSaveClicked() {
      this.$confirm('确认保存试卷配置？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message({
          type: 'success',
          message: '保存成功!'
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
