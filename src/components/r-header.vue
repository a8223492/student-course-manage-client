<template>
  <div>
    <el-button style="color: #ffffff; font-size: 18px" type="text" @click="out()">
      登出
    </el-button>
    <el-divider direction="vertical" style="color: #333333"></el-divider>
    <i class="el-icon-paperclip" style="margin-right: 18px"></i>
    <span>{{ term }}</span>
    <el-divider direction="vertical"></el-divider>
    <i class="el-icon-user" style="margin-right: 18px"></i>
    <span>{{ name }}</span>

  </div>
</template>

<script>
export default {
  name: "r-header",
  data() {
    return {
      name: null,
      currentTerm: null
    }
  },
  created() {
    this.name = sessionStorage.getItem("sname")
    this.term = sessionStorage.getItem("term")
  },
  methods: {
    out() {
      sessionStorage.clear();
      this.$router.push('/')
    }
  }
}
</script>

<style scoped>

</style>