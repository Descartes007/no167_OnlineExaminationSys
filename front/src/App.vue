<!--
 * 程序主入口
 *
 * @Author: ShanZhu
 * @Date: 2023-11-23
-->
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App',
  created() {
    var token = this.$cookies.get("rb_token");
    var role = this.$cookies.get("rb_role");
    if(token == null || token == "" || role == null || role == null) {
      this.$router.push({path: '/' }) //跳转到首页
    }
  }
}
</script>

<style>
ul {
  list-style: none;
}
a {
  text-decoration: none;
}
* {
  margin: 0;
  padding: 0;
}
#app {
  font-family: "Microsoft YaHei", "Helvetica", "Tahoma", "Geneva", "Arial", sans-serif;
  background-color: #eee;
}
</style>
