<template>
  <div>
    <el-table
        :data="tableData"
        border
        stripe
        style="width: 100%">
      <el-table-column
          fixed
          prop="cno"
          label="课程号"
          width="150">
      </el-table-column>
      <el-table-column
          prop="cname"
          label="课程名"
          width="150">
      </el-table-column>
      <el-table-column
          prop="ccredit"
          label="学分"
          width="150">
      </el-table-column>
      <el-table-column
          label="操作"
          width="100">
        <template slot-scope="scope">
          <el-popconfirm
              confirm-button-text='选择'
              cancel-button-text='取消'
              icon="el-icon-info"
              icon-color="red"
              title="确定开设？"
              @confirm="offer(scope.row)"
          >
            <el-button slot="reference" type="text" size="small">开设</el-button>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
        background
        layout="prev, pager, next"
        :total="total"
        :page-size="pageSize"
        @current-change="changePage"
    >
    </el-pagination>
  </div>
</template>

<script>
export default {

  methods: {
    // select(row) {
    //   console.log(row)
    // },
    // deleteTeacher(row) {
    //   const that = this
    //   axios.get('/course/deleteById/' + row.cno).then(function (resp) {
    //     if (resp.data === true) {
    //       that.$message({
    //         showClose: true,
    //         message: '删除成功',
    //         type: 'success'
    //       });
    //       window.location.reload()
    //     }
    //     else {
    //       that.$message({
    //         showClose: true,
    //         message: '删除出错，请查询数据库连接',
    //         type: 'error'
    //       });
    //     }
    //   })
    // },
    offer(row) {
      const that = this
      const tno = sessionStorage.getItem("tno")
      const cno = row.cno
      const term = this.ruleForm.term
      const capacity = this.ruleForm.capacity
      const ct = {
        tno: tno,
        cno: cno,
        term: term,
        capacity: capacity
      }
      if ((this.ruleForm.term === '' || this.ruleForm.term === null) || (!Number.isInteger(this.ruleForm.capacity) || this.ruleForm.capacity === '' || this.ruleForm.capacity === null))//检查是否键入学期和容量
      {
        that.$message.error('学期或者选课容量输入错误')
      } else {
        axios.post('/ct/add/', ct).then(function (resp) {
          if (resp.data.code === 200) {
            that.$message({
              showClose: true,
              message: resp.data.msg,
              type: 'success'
            });
            window.location.reload()
          } else {
            that.$message({
              showClose: true,
              message: resp.data.msg,
              type: 'error'
            });
          }
      })
      }


    },
    changePage(page) {
      page = page - 1
      const that = this
      let start = page * that.pageSize, end = that.pageSize * (page + 1)
      let length = that.tmpList.length
      let ans = (end < length) ? end : length
      that.tableData = that.tmpList.slice(start, ans)
    },
    // editor(row) {
    //   this.$router.push({
    //     path: '/editorCourse',
    //     query: {
    //       cno: row.cno
    //     }
    //   })
    // }
  },
  created() {
    console.log(this.type)
  },
  data() {
    return {
      tableData: null,
      pageSize: 10,
      total: null,
      tmpList: null,
      type: sessionStorage.getItem("type"),
    }
  },
  props: {
    ruleForm: Object,
    isActive: Boolean
  },
  watch: {
    ruleForm: {
      handler(newRuleForm, oldRuleForm) {
        console.log("组件监听 form")
        console.log(newRuleForm)
        const that = this
        that.tmpList = null
        that.total = null
        that.tableData = null
        axios.post("/course/findBySearch", newRuleForm).then(function (resp) {
          console.log("查询结果:");
          console.log(resp)
          that.tmpList = resp.data.data
          that.total = resp.data.length
          let start = 0, end = that.pageSize
          let length = that.tmpList.length
          let ans = (end < length) ? end : length
          that.tableData = that.tmpList.slice(start, ans)
        })
      },
      deep: true,
      immediate: true
    }
  },
}
</script>