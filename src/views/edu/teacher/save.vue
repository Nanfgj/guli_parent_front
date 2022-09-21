<template>
  <div class="app-container">

    <el-form label-width="120px">
      <el-form-item label="讲师名称">
        <el-input v-model="teacher.name"/>
      </el-form-item>

      <el-form-item label="讲师排序">
        <el-input-number v-model="teacher.sort" controls-position="right" :min="0"/>
      </el-form-item>

      <el-form-item label="讲师头衔">
        <el-select v-model="teacher.level" clearable placeholder="请选择">
          <!--
          数据类型一定要和取出的json中的一致，否则没法回填
          因此，这里value使用动态绑定的值，保证其数据类型是number
          -->
          <el-option :value="1" label="高级讲师"/>
          <el-option :value="2" label="首席讲师"/>
        </el-select>
      </el-form-item>

      <el-form-item label="讲师资历">
        <el-input v-model="teacher.career"/>
      </el-form-item>

      <el-form-item label="讲师简介">
        <el-input v-model="teacher.intro" :rows="10" type="textarea"/>
      </el-form-item>

      <!-- 讲师头像：TODO -->
      <el-form-item>
        <el-button :disabled="saveBtnDisabled" type="primary" @click="saveOrUpdate">保存</el-button>
      </el-form-item>
    </el-form>

  </div>
</template>

<script>

//引用teacher.js文件
import teacherApi from '@/api/edu/teacher'

export default {
  data() {
    return {
      teacher: {
        name: '',
        sort: 0,
        level: 1,
        career: '',
        intro: '',
        avatar: ''
      },
      saveBtnDisabled: false // 保存按钮是否禁用,
    }
  },
  created() { //页面渲染前执行
    //判断是添加还是修改
    this.init()

  },
  watch: { //监听
    $route(to, from){ //路由发生变化，方法就会执行
      this.init()
    }
  },
  methods: {
    init(){
      //判断路径有id值，做修改
      if (this.$route.params && this.$route.params.id){
        //从路径获取id值
        const id = this.$route.params.id
        //调用根据id查询的方法
        this.getInfo(id)
      }else { //路径里没有id，做添加
        //清空表单
        this.teacher = {}
      }
    },

    //保存和修改
    saveOrUpdate() {
      this.saveBtnDisabled = true

      //判断是修改还是添加
      //根据teacher中是否有id值判断
      if (this.teacher.id){
        this.updateTeacher()  //有id值，修改
      }else {
        this.saveTeacher()    //没有id值，添加
      }
    },

    // 保存
    saveTeacher() {
      //引用保存讲师
      teacherApi.addTeacher(this.teacher)
        .then(response =>{ //保存成功

          //提示信息
          this.$message({
            type: 'success',
            message: '添加成功!'
          });
          //添加成功后，跳转到讲师列表 ---- 路由跳转（重要）
          this.$router.push({path:'/teacher/table'})

        })
    },

    //修改讲师方法
    updateTeacher(){
      teacherApi.updateTeacher(this.teacher)
        .then(response =>{
          //提示信息
          this.$message({
            type: 'success',
            message: '修改成功!'
          });
          //添加成功后，跳转到讲师列表 ---- 路由跳转（重要）
          this.$router.push({path:'/teacher/table'})
        })
    },

    //根据讲师id查询
    getInfo(id){
      teacherApi.getTeacherInfo(id)
        .then(response =>{
          //将后端查询的讲师信息赋值给 teacher
          this.teacher = response.data.teacher
        })
    }
  }
}
</script>
