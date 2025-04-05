<script setup>
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'
import { User, Lock, Connection } from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'

const router = useRouter()
const loading = ref(false)

const loginForm = reactive({
  username: '',
  password: '',
  role: '学生'
})

const rules = {
  username: [
    { required: true, message: '请输入用户名', trigger: 'blur' },
    { min: 3, max: 20, message: '长度应为3到20个字符', trigger: 'blur' }
  ],
  password: [
    { required: true, message: '请输入密码', trigger: 'blur' },
    { min: 6, max: 20, message: '长度应为6到20个字符', trigger: 'blur' }
  ]
}

const loginFormRef = ref(null)

const handleLogin = async () => {
  if (!loginFormRef.value) return
  
  await loginFormRef.value.validate((valid) => {
    if (valid) {
      loading.value = true
      // 模拟API请求
      setTimeout(() => {
        ElMessage.success('登录成功！')
        loading.value = false
        router.push('/')
      }, 1500)
      
      // 实际API请求代码
      // try {
      //   const response = await fetch('/login', {
      //     method: 'POST',
      //     headers: {
      //       'Content-Type': 'application/json'
      //     },
      //     body: JSON.stringify(loginForm)
      //   })
      //   const data = await response.json()
      //   if (data.code === 1) {
      //     localStorage.setItem('token', data.data.token)
      //     ElMessage.success(data.msg)
      //     router.push('/')
      //   } else {
      //     ElMessage.error(data.msg || '登录失败')
      //   }
      // } catch (error) {
      //   ElMessage.error('网络错误，请稍后重试')
      // } finally {
      //   loading.value = false
      // }
    }
  })
}

const goToRegister = () => {
  router.push('/register')
}
</script>

<template>
  <div class="login-container">
    <div class="login-box">
      <div class="login-header">
        <div class="logo-wrap" @click="router.push('/')">
          <el-icon class="logo-icon"><Connection /></el-icon>
          <h1>计算机网络学习平台</h1>
        </div>
        <h2>欢迎登录</h2>
      </div>
      
      <el-form 
        ref="loginFormRef" 
        :model="loginForm" 
        :rules="rules" 
        label-position="top"
        class="login-form"
      >
        <el-form-item label="用户名" prop="username">
          <el-input 
            v-model="loginForm.username" 
            placeholder="请输入用户名"
          >
            <template #prefix>
              <el-icon><User /></el-icon>
            </template>
          </el-input>
        </el-form-item>
        
        <el-form-item label="密码" prop="password">
          <el-input 
            v-model="loginForm.password" 
            type="password" 
            placeholder="请输入密码"
            show-password
          >
            <template #prefix>
              <el-icon><Lock /></el-icon>
            </template>
          </el-input>
        </el-form-item>
        
        <el-form-item label="角色">
          <el-radio-group v-model="loginForm.role">
            <el-radio label="学生">学生</el-radio>
            <el-radio label="教师">教师</el-radio>
            <el-radio label="管理员">管理员</el-radio>
          </el-radio-group>
        </el-form-item>
        
        <el-form-item>
          <el-button 
            type="primary" 
            :loading="loading" 
            @click="handleLogin" 
            class="login-button"
          >
            登录
          </el-button>
        </el-form-item>
        
        <div class="form-footer">
          <span>还没有账号？</span>
          <a @click="goToRegister">注册账号</a>
        </div>
      </el-form>
    </div>

    <div class="copyright">
      © 2025 计算机网络学习平台 版权所有
    </div>
  </div>
</template>

<style scoped>
.login-container {
  height: 100vh;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: url('https://picsum.photos/id/11/1920/1080') no-repeat center center;
  background-size: cover;
  position: relative;
  overflow: hidden;
}

.login-container::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(5px);
}

.login-box {
  width: 400px;
  padding: 30px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 8px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  position: relative;
  z-index: 1;
  max-width: 90%;
}

.login-header {
  text-align: center;
  margin-bottom: 30px;
}

.logo-wrap {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
  cursor: pointer;
}

.logo-icon {
  color: #409EFF;
  font-size: 32px;
  margin-right: 10px;
}

.login-header h1 {
  font-size: 20px;
  color: #409EFF;
  margin: 0;
}

.login-header h2 {
  font-size: 24px;
  color: #303133;
  margin: 10px 0;
}

.login-form {
  margin-top: 20px;
}

.login-button {
  width: 100%;
  font-size: 16px;
  padding: 12px 0;
  margin-top: 10px;
}

.form-footer {
  text-align: center;
  margin-top: 15px;
  font-size: 14px;
  color: #606266;
}

.form-footer a {
  color: #409EFF;
  text-decoration: none;
  cursor: pointer;
  margin-left: 5px;
}

.form-footer a:hover {
  text-decoration: underline;
}

.copyright {
  position: relative;
  z-index: 1;
  color: white;
  margin-top: 20px;
  font-size: 14px;
  text-align: center;
}

@media (max-width: 480px) {
  .login-box {
    width: 320px;
    padding: 20px;
  }
  
  .login-header h1 {
    font-size: 18px;
  }
  
  .login-header h2 {
    font-size: 20px;
  }
}
</style> 