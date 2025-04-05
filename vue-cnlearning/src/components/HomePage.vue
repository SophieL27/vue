<template>
  <div class="home-page">
    <!-- 1. 顶部导航栏 -->
    <header class="header">
      <div class="logo-container">
        <span class="network-icon">&#9741;</span>
        <h1 class="logo-text">计算机网络学习平台</h1>
      </div>
      <div class="search-container">
        <div class="search-input-container">
          <input
            v-model="searchText"
            type="text"
            placeholder="搜索课程/章节"
            class="search-input"
          />
          <span class="search-icon">&#128269;</span>
        </div>
      </div>
      <div class="user-container">
        <button class="btn btn-login">登录</button>
        <button class="btn btn-register">注册</button>
      </div>
    </header>

    <!-- 2. Banner区 -->
    <div class="banner-container">
      <div class="carousel">
        <div class="carousel-item" v-for="(item, index) in bannerItems" :key="index" v-show="activeSlide === index">
          <div class="banner-content" :style="{ backgroundImage: `url(${item.imageUrl})` }">
            <div class="banner-text">
              <h2>{{ item.title }}</h2>
              <p>{{ item.description }}</p>
              <button class="btn btn-primary action-button">立即学习</button>
            </div>
          </div>
        </div>
        <div class="carousel-indicators">
          <span 
            v-for="(item, index) in bannerItems" 
            :key="index" 
            class="indicator" 
            :class="{ active: activeSlide === index }"
            @click="setActiveSlide(index)"
          ></span>
        </div>
      </div>
    </div>

    <!-- 3. 课程分类导航 -->
    <div class="category-nav">
      <div class="tabs">
        <div class="tab-header">
          <div 
            v-for="tab in tabs" 
            :key="tab.name" 
            class="tab-item" 
            :class="{ active: activeTab === tab.name }"
            @click="activeTab = tab.name"
          >
            {{ tab.label }}
          </div>
        </div>
        <div class="tab-content">
          <div v-if="activeTab === 'all'" class="tab-pane">
            <div class="course-container">
              <h2 class="section-title">推荐课程</h2>
              <!-- 4. 课程推荐卡片 -->
              <div class="course-list">
                <div class="row">
                  <div class="col" v-for="(course, index) in courses" :key="index">
                    <div class="course-card">
                      <img :src="course.imageUrl" class="course-image" />
                      <h3 class="course-title">{{ course.title }}</h3>
                      <div class="progress-container">
                        <div class="progress">
                          <div 
                            class="progress-bar" 
                            :style="{ width: course.progress + '%', backgroundColor: getProgressColor(course.progress) }"
                          ></div>
                        </div>
                        <span class="progress-text">已完成 {{ course.progress }}%</span>
                      </div>
                      <div class="course-footer">
                        <span class="teacher-name">讲师: {{ course.teacher }}</span>
                        <button class="btn btn-primary btn-sm continue-btn">继续学习</button>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div v-if="activeTab === 'inProgress'" class="tab-pane">
            进行中的课程列表
          </div>
          <div v-if="activeTab === 'completed'" class="tab-pane">
            已完成的课程列表
          </div>
          <div v-if="activeTab === 'labs'" class="tab-pane">
            测试专区课程列表
          </div>
        </div>
      </div>
    </div>

    <!-- 5. 底部功能区 -->
    <footer class="footer">
      <div class="footer-links">
        <a href="#" class="link">常见问题</a>
        <span class="divider">|</span>
        <a href="tel:15351347448" class="link">电话：15351347448</a>
        <span class="divider">|</span>
        <a href="mailto:sophieliu27@foxmail.com" class="link">邮箱：sophieliu27@foxmail.com</a>
      </div>
      <div class="copyright">
        © 2025 计算机网络学习平台 版权所有
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'

// 搜索文本
const searchText = ref('')

// 轮播图当前激活的索引
const activeSlide = ref(0)
let carouselInterval = null

// 自动轮播
const startCarousel = () => {
  carouselInterval = setInterval(() => {
    activeSlide.value = (activeSlide.value + 1) % bannerItems.length
  }, 5000)
}

const setActiveSlide = (index) => {
  activeSlide.value = index
  // 重置定时器
  clearInterval(carouselInterval)
  startCarousel()
}

onMounted(() => {
  startCarousel()
})

onBeforeUnmount(() => {
  clearInterval(carouselInterval)
})

// 当前激活的标签页
const activeTab = ref('all')

// 标签页数据
const tabs = [
  { label: '全部课程', name: 'all' },
  { label: '进行中', name: 'inProgress' },
  { label: '已学完', name: 'completed' },
  { label: '测试专区', name: 'labs' }
]

// 进度条颜色方法
const getProgressColor = (percentage) => {
  if (percentage < 30) {
    return '#909399' // 灰色
  } else if (percentage < 70) {
    return '#409EFF' // 蓝色
  } else {
    return '#67C23A' // 绿色
  }
}

// 轮播图数据
const bannerItems = [
  {
    title: 'TCP/IP协议精讲',
    description: '深入理解网络通信的核心协议',
    imageUrl: 'https://picsum.photos/id/1/1200/400' // 临时使用占位图，实际应替换为网络协议相关图片
  },
  {
    title: '网络安全实战',
    description: '学习保护网络系统的核心技能',
    imageUrl: 'https://picsum.photos/id/2/1200/400'
  },
  {
    title: '计算机网络基础',
    description: '从零开始学习计算机网络知识',
    imageUrl: 'https://picsum.photos/id/3/1200/400'
  }
]

// 课程数据
const courses = [
  {
    title: 'TCP/IP协议详解',
    imageUrl: 'https://picsum.photos/id/11/300/200',
    progress: 65,
    teacher: '张教授'
  },
  {
    title: '网络安全基础',
    imageUrl: 'https://picsum.photos/id/12/300/200',
    progress: 30,
    teacher: '李教授'
  },
  {
    title: '路由交换技术',
    imageUrl: 'https://picsum.photos/id/13/300/200',
    progress: 90,
    teacher: '王教授'
  },
  {
    title: 'SDN软件定义网络',
    imageUrl: 'https://picsum.photos/id/14/300/200',
    progress: 20,
    teacher: '刘教授'
  }
]
</script>

<style scoped>
.home-page {
  font-family: 'PingFang SC', 'Helvetica Neue', Arial, sans-serif;
  background-color: #f5f7fa;
  min-height: 100vh;
  width: 100vw;
  max-width: 100%;
  box-sizing: border-box;
  overflow-x: hidden;
  margin: 0;
  padding: 0;
}

/* 顶部导航栏样式 */
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px 40px;
  background-color: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  position: sticky;
  top: 0;
  z-index: 100;
  width: 100%;
  box-sizing: border-box;
}

.logo-container {
  display: flex;
  align-items: center;
}

.network-icon {
  font-size: 24px;
  color: #409eff;
  margin-right: 10px;
}

.logo-text {
  font-size: 20px;
  color: #303133;
  margin: 0;
}

.search-container {
  width: 40%;
}

.search-input-container {
  position: relative;
  width: 100%;
}

.search-input {
  width: 100%;
  padding: 10px 15px;
  border: 1px solid #dcdfe6;
  border-radius: 4px;
  font-size: 14px;
  transition: all 0.2s;
  padding-right: 35px;
}

.search-input:focus {
  border-color: #409eff;
  outline: none;
}

.search-icon {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  color: #909399;
}

.user-container {
  display: flex;
  gap: 10px;
}

.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.3s ease;
}

.btn-sm {
  padding: 8px 15px;
  font-size: 12px;
}

.btn-primary {
  background-color: #409eff;
  color: white;
}

.btn-primary:hover {
  background-color: #66b1ff;
}

.btn-login {
  border: 1px solid #409eff;
  background-color: transparent;
  color: #409eff;
}

.btn-login:hover {
  color: #66b1ff;
  border-color: #66b1ff;
}

.btn-register {
  background-color: #409eff;
  color: white;
}

.btn-register:hover {
  background-color: #66b1ff;
}

/* Banner区样式 */
.banner-container {
  margin: 0;
  width: 100vw;
  max-width: 100%;
}

.carousel {
  position: relative;
  height: 450px;
  overflow: hidden;
  width: 100vw;
  max-width: 100%;
}

.carousel-item {
  height: 100%;
  width: 100%;
}

.banner-content {
  height: 100%;
  background-size: cover;
  background-position: center;
  display: flex;
  align-items: center;
  padding-left: 100px;
  width: 100%;
}

.banner-text {
  background: rgba(255, 255, 255, 0.85);
  padding: 30px;
  border-radius: 8px;
  max-width: 450px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.banner-text h2 {
  margin-top: 0;
  color: #303133;
  font-size: 28px;
}

.banner-text p {
  color: #606266;
  margin-bottom: 20px;
}

.action-button {
  font-weight: bold;
}

.carousel-indicators {
  position: absolute;
  bottom: 20px;
  left: 0;
  right: 0;
  display: flex;
  justify-content: center;
  gap: 10px;
}

.indicator {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.5);
  cursor: pointer;
  transition: all 0.3s ease;
}

.indicator.active {
  background-color: #fff;
}

/* 课程分类导航样式 */
.category-nav {
  margin: 30px auto;
  width: 95%;
  max-width: 1600px;
  box-sizing: border-box;
}

.tabs {
  border: 1px solid #e4e7ed;
  border-radius: 4px;
  background-color: #fff;
}

.tab-header {
  display: flex;
  background-color: #ecf5ff;
  border-bottom: 1px solid #e4e7ed;
}

.tab-item {
  padding: 15px 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  border-right: 1px solid #e4e7ed;
  font-size: 14px;
}

.tab-item:hover {
  color: #409eff;
}

.tab-item.active {
  color: #409eff;
  border-bottom: 2px solid #409eff;
  margin-bottom: -1px;
}

.tab-content {
  padding: 20px;
  max-width: 100%;
}

.section-title {
  color: #303133;
  margin: 20px 0;
  font-weight: 500;
  font-size: 22px;
  padding-left: 10px;
  border-left: 4px solid #409eff;
}

/* 课程推荐卡片样式 */
.course-container {
  padding: 10px 0 20px;
  width: 100%;
  max-width: 100%;
}

.course-list {
  padding: 0 0 30px;
  width: 100%;
  max-width: 100%;
}

.row {
  display: flex;
  flex-wrap: wrap;
  margin: 0 -10px;
  width: 100%;
}

.col {
  flex: 0 0 20%;
  max-width: 20%;
  padding: 0 10px;
  box-sizing: border-box;
}

@media (max-width: 1600px) {
  .col {
    flex: 0 0 25%;
    max-width: 25%;
  }
}

@media (max-width: 1200px) {
  .col {
    flex: 0 0 33.33%;
    max-width: 33.33%;
  }
}

@media (max-width: 992px) {
  .col {
    flex: 0 0 50%;
    max-width: 50%;
  }
}

@media (max-width: 576px) {
  .col {
    flex: 0 0 100%;
    max-width: 100%;
  }
}

.course-card {
  margin-bottom: 25px;
  transition: all 0.3s ease;
  height: 100%;
  border-radius: 8px;
  overflow: hidden;
  background-color: #fff;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  padding-bottom: 15px;
}

.course-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.15);
}

.course-image {
  width: 100%;
  height: 180px;
  object-fit: cover;
  border-radius: 4px 4px 0 0;
}

.course-title {
  margin: 15px 0 10px;
  font-size: 18px;
  color: #303133;
  font-weight: 500;
  padding: 0 15px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.progress-container {
  margin: 15px;
}

.progress {
  height: 6px;
  background-color: #ebeef5;
  border-radius: 100px;
  overflow: hidden;
}

.progress-bar {
  height: 100%;
  border-radius: 100px;
}

.progress-text {
  display: block;
  font-size: 13px;
  color: #909399;
  margin-top: 8px;
}

.course-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 15px;
  padding: 0 15px;
}

.teacher-name {
  font-size: 14px;
  color: #606266;
}

.continue-btn:hover {
  background-color: #66b1ff;
  transform: scale(1.05);
}

/* 底部功能区样式 */
.footer {
  background-color: #fff;
  padding: 30px 0;
  text-align: center;
  border-top: 1px solid #e4e7ed;
  margin-top: 30px;
  width: 100vw;
  max-width: 100%;
}

.footer-links {
  display: flex;
  justify-content: center;
  margin-bottom: 15px;
}

.link {
  color: #409eff;
  text-decoration: none;
  margin: 0 10px;
}

.link:hover {
  color: #66b1ff;
}

.divider {
  color: #dcdfe6;
}

.copyright {
  font-size: 13px;
  color: #909399;
}

/* 响应式设计 */
@media (max-width: 992px) {
  .header {
    padding: 15px 20px;
  }
  
  .banner-content {
    padding-left: 30px;
  }
  
  .category-nav {
    margin: 20px;
  }
}

@media (max-width: 768px) {
  .logo-text {
    font-size: 16px;
  }
  
  .search-container {
    width: 50%;
  }
  
  .banner-text {
    max-width: 80%;
  }
  
  .banner-text h2 {
    font-size: 22px;
  }
}

@media (max-width: 576px) {
  .header {
    flex-direction: column;
    padding: 10px;
  }
  
  .logo-container {
    margin-bottom: 10px;
  }
  
  .search-container {
    width: 100%;
    margin: 10px 0;
  }
  
  .user-container {
    width: 100%;
    justify-content: center;
  }
  
  .banner-content {
    padding-left: 10px;
    justify-content: center;
  }
  
  .banner-text {
    max-width: 90%;
    padding: 15px;
  }
}
</style> 