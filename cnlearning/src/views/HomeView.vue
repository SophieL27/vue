<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { Search, Connection } from '@element-plus/icons-vue'

const router = useRouter()

// 轮播图数据
const banners = ref([
  {
    id: 1,
    title: 'TCP/IP协议精讲',
    image: 'https://picsum.photos/800/300?random=1',
    description: '深入学习TCP/IP协议族，掌握网络通信的核心技术'
  },
  {
    id: 2,
    title: '网络安全实战',
    image: 'https://picsum.photos/800/300?random=2',
    description: '通过实际案例学习网络安全知识，提高防护能力'
  },
  {
    id: 3,
    title: '计算机网络基础',
    image: 'https://picsum.photos/800/300?random=3',
    description: '系统学习计算机网络的基本概念和架构'
  }
])

// 课程分类
const activeTab = ref('all')

// 推荐课程
const courses = ref([
  {
    id: 1,
    title: 'TCP/IP协议详解',
    cover: 'https://picsum.photos/300/200?random=1',
    progress: 45,
    teacher: '张教授',
    description: '从基础到深入，全面讲解TCP/IP协议栈'
  },
  {
    id: 2,
    title: '网络安全与防护',
    cover: 'https://picsum.photos/300/200?random=2',
    progress: 28,
    teacher: '李教授',
    description: '学习网络攻防技术，掌握安全防护措施'
  },
  {
    id: 3,
    title: '路由与交换技术',
    cover: 'https://picsum.photos/300/200?random=3',
    progress: 65,
    teacher: '王教授',
    description: '深入理解路由协议与交换技术'
  },
  {
    id: 4,
    title: '网络编程实践',
    cover: 'https://picsum.photos/300/200?random=4',
    progress: 12,
    teacher: '刘教授',
    description: '学习Socket编程，构建网络应用'
  },
  {
    id: 5,
    title: '无线网络技术',
    cover: 'https://picsum.photos/300/200?random=5',
    progress: 89,
    teacher: '赵教授',
    description: 'Wi-Fi、蓝牙、5G等无线网络技术详解'
  },
  {
    id: 6,
    title: '网络架构设计',
    cover: 'https://picsum.photos/300/200?random=6',
    progress: 35,
    teacher: '陈教授',
    description: '企业网络架构设计与实施'
  }
])

// 跳转到登录页面
const goToLogin = () => {
  router.push('/login')
}

// 跳转到注册页面
const goToRegister = () => {
  router.push('/register')
}
</script>

<template>
  <div class="home-container">
    <!-- 顶部导航栏 -->
    <div class="header">
      <div class="logo">
        <el-icon><Connection /></el-icon>
        <span>计算机网络学习平台</span>
      </div>
      <div class="search">
        <el-input placeholder="搜索课程/章节" clearable>
          <template #prefix>
            <el-icon><Search /></el-icon>
          </template>
        </el-input>
      </div>
      <div class="user-actions">
        <el-button @click="goToLogin" type="primary" plain>登录</el-button>
        <el-button @click="goToRegister" type="primary">注册</el-button>
      </div>
    </div>

    <!-- Banner区 -->
    <div class="banner">
      <el-carousel height="300px">
        <el-carousel-item v-for="banner in banners" :key="banner.id">
          <div class="banner-content" :style="{ backgroundImage: `url(${banner.image})` }">
            <div class="banner-info">
              <h2>{{ banner.title }}</h2>
              <p>{{ banner.description }}</p>
              <el-button type="primary" size="large">立即学习</el-button>
            </div>
          </div>
        </el-carousel-item>
      </el-carousel>
    </div>

    <!-- 课程分类导航 -->
    <div class="course-nav">
      <el-tabs v-model="activeTab">
        <el-tab-pane label="全部课程" name="all"></el-tab-pane>
        <el-tab-pane label="进行中" name="inProgress"></el-tab-pane>
        <el-tab-pane label="已学完" name="completed"></el-tab-pane>
        <el-tab-pane label="测试专区" name="test"></el-tab-pane>
      </el-tabs>
    </div>

    <!-- 课程推荐卡片 -->
    <div class="course-container">
      <el-row :gutter="30">
        <el-col :xs="24" :sm="12" :md="8" :lg="8" :xl="6" v-for="course in courses" :key="course.id" class="course-col">
          <el-card class="course-card" shadow="hover">
            <img :src="course.cover" class="course-image" />
            <div class="course-info">
              <h3>{{ course.title }}</h3>
              <div class="progress-section">
                <span>学习进度：{{ course.progress }}%</span>
                <el-progress :percentage="course.progress" :stroke-width="8" />
              </div>
              <div class="teacher">讲师：{{ course.teacher }}</div>
              <el-button type="primary" class="continue-btn">继续学习</el-button>
            </div>
          </el-card>
        </el-col>
      </el-row>
    </div>

    <!-- 底部功能区 -->
    <div class="footer">
      <div class="footer-content">
        <div class="footer-section">
          <h3>常见问题</h3>
          <ul>
            <li>如何开始学习？</li>
            <li>如何查看学习进度？</li>
            <li>如何获取证书？</li>
          </ul>
        </div>
        <div class="footer-section">
          <h3>联系方式</h3>
          <p>电话：15351347448</p>
          <p>邮箱：sophieliu27@foxmail.com</p>
        </div>
      </div>
      <div class="copyright">
        © 2025 计算机网络学习平台 版权所有
      </div>
    </div>
  </div>
</template>

<style scoped>
.home-container {
  font-family: Arial, sans-serif;
  color: #333;
  width: 100%;
  min-height: 100vh;
  overflow-x: hidden;
}

/* 顶部导航栏样式 */
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 15px 40px;
  background-color: #fff;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  width: 100%;
  position: sticky;
  top: 0;
  z-index: 1000;
}

.logo {
  display: flex;
  align-items: center;
  font-size: 22px;
  font-weight: bold;
  color: #409EFF;
}

.logo i {
  margin-right: 8px;
  font-size: 24px;
}

.search {
  width: 40%;
  max-width: 500px;
}

.user-actions {
  display: flex;
  gap: 10px;
}

/* Banner区样式 */
.banner {
  width: 100%;
}

.banner-content {
  height: 300px;
  background-size: cover;
  background-position: center;
  display: flex;
  align-items: center;
  position: relative;
}

.banner-content::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.4);
}

.banner-info {
  position: relative;
  color: white;
  padding: 0 50px;
  max-width: 600px;
}

.banner-info h2 {
  font-size: 32px;
  margin-bottom: 10px;
}

.banner-info p {
  font-size: 16px;
  margin-bottom: 20px;
}

/* 课程分类导航样式 */
.course-nav {
  margin: 30px 0;
  padding: 0 40px;
}

/* 课程推荐卡片样式 */
.course-container {
  padding: 0 40px;
  margin-bottom: 40px;
  width: 100%;
}

.course-col {
  margin-bottom: 30px;
}

.course-card {
  transition: transform 0.3s, box-shadow 0.3s;
  height: 100%;
}

.course-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
}

.course-image {
  width: 100%;
  height: 150px;
  object-fit: cover;
}

.course-info {
  padding: 15px;
}

.course-info h3 {
  font-size: 18px;
  margin-bottom: 12px;
}

.progress-section {
  margin-bottom: 10px;
}

.teacher {
  color: #606266;
  margin-bottom: 15px;
  font-size: 14px;
}

.continue-btn {
  width: 100%;
}

/* 底部功能区样式 */
.footer {
  background-color: #f5f7fa;
  padding: 40px 40px 20px;
  margin-top: 30px;
  width: 100%;
}

.footer-content {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  max-width: 1400px;
  margin: 0 auto;
}

.footer-section {
  flex: 1;
  min-width: 250px;
  margin-bottom: 20px;
}

.footer-section h3 {
  font-size: 18px;
  margin-bottom: 15px;
  color: #409EFF;
}

.footer-section ul {
  list-style: none;
  padding: 0;
}

.footer-section li, .footer-section p {
  margin-bottom: 8px;
  color: #606266;
}

.copyright {
  text-align: center;
  margin-top: 30px;
  padding-top: 15px;
  border-top: 1px solid #e4e7ed;
  color: #909399;
  font-size: 14px;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .header {
    flex-direction: column;
    padding: 15px 20px;
  }
  
  .logo {
    margin-bottom: 15px;
  }
  
  .search {
    width: 100%;
    margin: 10px 0;
  }
  
  .banner-info h2 {
    font-size: 24px;
  }
  
  .banner-info p {
    font-size: 14px;
  }
  
  .course-container, .course-nav {
    padding: 0 20px;
  }
}
</style>
