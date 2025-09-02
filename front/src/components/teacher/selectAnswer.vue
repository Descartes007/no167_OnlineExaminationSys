<!--
 * 查询所有题库
 *
 * @Author: ShanZhu
 * @Date: 2023-11-23
-->
<template>
  <div class="exam">
    <el-form :inline="true" class="demo-form-inline">
      <el-form-item label="试卷名称">
        <el-input v-model="subject" placeholder="试卷名称"></el-input>
      </el-form-item>
      <el-form-item label="章节">
        <el-input v-model="section" placeholder="章节"></el-input>
      </el-form-item>
      <el-form-item label="题目内容">
        <el-input v-model="question" placeholder="题目内容"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="getAnswerInfo">查询</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="pagination.records" border :row-class-name="tableRowClassName">
      <el-table-column fixed="left" prop="subject" label="试卷名称" width="180"></el-table-column>
      <el-table-column prop="question" label="题目信息" width="490"></el-table-column>
      <el-table-column prop="section" label="所属章节" width="200"></el-table-column>
      <el-table-column prop="type" label="题目类型" width="200"></el-table-column>
      <el-table-column prop="score" label="试题分数" width="150"></el-table-column>
      <el-table-column prop="level" label="难度等级" width="133"></el-table-column>
      <el-table-column fixed="right" label="操作" width="240">
        <template slot-scope="scope">
          <el-button @click="toEdit(scope.row.type, scope.row.questionId)" type="primary" size="small">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagination.current"
      :page-sizes="[6, 10]"
      :page-size="pagination.size"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pagination.total"
      class="page"
    ></el-pagination>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pagination: {
        //分页后的考试信息
        current: 1, //当前页
        total: null, //记录条数
        size: 6 //每页条数

      },
      section: "",
      subject: "",
      question: "",
    };
  },
  created() {
    this.getAnswerInfo();
  },
  methods: {
    toEdit(type, id) {
      // 前往编辑题目
      this.$router.push({path:'/editAnswerChildren',query: {type: type, questionId: id}})
    },
    getAnswerInfo(size, current) {
      //分页查询所有试卷信息
      if(typeof size === 'number' && !isNaN(size)) {
        this.pagination.size = size;
      }
      if(typeof current === 'number' && !isNaN(current)) {
        this.pagination.current = current;
      } else {
        this.pagination.current = 1;
      }
      var subject = this.subject;
      if(this.subject.trim() == "") {
        subject = "@";
      }
      var section = this.section;
      if(this.section.trim() == "") {
        section = "@";
      }
      var question = this.question;
      if(this.question.trim() == "") {
        question = "@";
      }
      this.$axios(
        `/api/answers/${this.pagination.current}/${this.pagination.size}/${subject}/${section}/${question}`
      )
        .then(res => {
          this.pagination = res.data.data;
        })
        .catch(error => {});
    },
    //改变当前记录条数
    handleSizeChange(val) {
      this.getAnswerInfo(val);
    },
    //改变当前页码，重新发送请求
    handleCurrentChange(val) {
      this.getAnswerInfo(undefined, val);
    },
    tableRowClassName({ row, rowIndex }) {
      if (rowIndex % 2 == 0) {
        return "warning-row";
      } else {
        return "success-row";
      }
    }
  }
};
</script>
<style lang="less" scoped>
.exam {
  padding: 0px 40px;
  .page {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .edit {
    margin-left: 20px;
  }
  .el-table tr {
    background-color: #DD5862 !important;
  }
}
  .el-table .warning-row {
    background: #000 !important;

  }

  .el-table .success-row {
    background: #DD5862;
  }

</style>
