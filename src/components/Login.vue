<template>
  <div class="login">
    <el-form ref="form" :model="form" label-width="80px">
      <img src="../assets/images/313263.jpg" alt>
      <el-form-item label="用户">
        <el-input v-model="form.name"></el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input v-model="form.name"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">登陆</el-button>
        <el-button>重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        name: "",
        password: ""
      }
    };
  }
};
</script>

<style lang='less'>
.login {
  height: 100%;
  background-color: #2d434c;
  overflow: hidden;

  .el-form {
    width: 500px;
    background-color: #fff;
    margin: 200px auto;
    padding: 75px 40px 15px;
    border-radius: 20px;
    position: relative;

    img {
      width: 100px;
      height: 100px;
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      top: -50px;
      border-radius: 50%;
      border: 10px solid #fff;
    }

    .el-button + .el-button {
      margin-left: 80px;
    }
  }
}
</style>
