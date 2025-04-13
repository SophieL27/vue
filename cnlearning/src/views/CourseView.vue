<script setup>
import { ref, reactive, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { Download, CaretRight, Edit, ArrowLeft, ArrowRight, SuccessFilled } from '@element-plus/icons-vue'

const router = useRouter()
const route = useRoute()

// 课程信息
const courseInfo = reactive({
  id: 1,
  title: 'TCP/IP协议详解',
  description: '从基础到深入，全面讲解TCP/IP协议栈',
  instructor: {
    name: '张教授',
    avatar: 'https://picsum.photos/100/100?random=1',
    description: '计算机网络专家，10年教学经验',
    answerTime: '每周一、三、五 19:00-21:00'
  }
})

// 当前选中的章节
const currentSection = ref(0)
const currentSubSection = ref(0)

// 播放速度
const playbackSpeed = ref(1.0)
const speedOptions = [0.5, 0.75, 1.0, 1.25, 1.5, 2.0]

// 章节列表
const sections = ref([
  {
    id: 1,
    title: '第一章：计算机网络概述',
    complete: true,
    subsections: [
      { id: 101, title: '1.1 网络基础概念', complete: true, duration: '15:20' },
      { id: 102, title: '1.2 网络体系结构', complete: true, duration: '20:45' },
      { id: 103, title: '1.3 标准化组织', complete: true, duration: '12:10' }
    ]
  },
  {
    id: 2,
    title: '第二章：TCP/IP协议族',
    complete: false,
    subsections: [
      { id: 201, title: '2.1 TCP/IP模型', complete: true, duration: '18:30' },
      { id: 202, title: '2.2 IP协议详解', complete: false, duration: '25:15' },
      { id: 203, title: '2.3 TCP协议详解', complete: false, duration: '28:40' },
      { id: 204, title: '2.4 UDP协议与应用', complete: false, duration: '16:20' }
    ]
  },
  {
    id: 3,
    title: '第三章：网络层技术',
    complete: false,
    subsections: [
      { id: 301, title: '3.1 IPv4与IPv6', complete: false, duration: '22:50' },
      { id: 302, title: '3.2 路由算法', complete: false, duration: '26:15' },
      { id: 303, title: '3.3 子网划分', complete: false, duration: '19:40' }
    ]
  },
  {
    id: 4,
    title: '第四章：网络安全',
    complete: false,
    subsections: [
      { id: 401, title: '4.1 加密技术基础', complete: false, duration: '24:10' },
      { id: 402, title: '4.2 网络攻击类型', complete: false, duration: '23:35' },
      { id: 403, title: '4.3 防火墙技术', complete: false, duration: '21:20' },
      { id: 404, title: '4.4 VPN技术', complete: false, duration: '18:45' }
    ]
  }
])

// 课件资料
const resources = ref([
  { id: 1, name: 'TCP/IP协议详解第一章.pdf', size: '2.5MB', type: 'pdf' },
  { id: 2, name: 'TCP协议状态转换图.png', size: '1.2MB', type: 'image' },
  { id: 3, name: '网络层实验指导.pptx', size: '5.8MB', type: 'ppt' },
  { id: 4, name: 'Wireshark抓包实验.zip', size: '10.5MB', type: 'zip' }
])

// 讨论区
const discussions = ref([
  { id: 1, user: '小明', content: 'TCP的三次握手过程是什么？', time: '2小时前', replies: 3 },
  { id: 2, user: '小红', content: 'IP地址分类具体是怎样的？', time: '5小时前', replies: 2 },
  { id: 3, user: '小张', content: '子网掩码的计算有什么简便方法？', time: '昨天', replies: 5 }
])

// 笔记
const notes = ref('')

// 切换章节
const selectSection = (sectionIndex, subsectionIndex) => {
  currentSection.value = sectionIndex
  currentSubSection.value = subsectionIndex
}

// 下一节
const nextSection = () => {
  const section = sections.value[currentSection.value]
  if (currentSubSection.value < section.subsections.length - 1) {
    currentSubSection.value++
  } else if (currentSection.value < sections.value.length - 1) {
    currentSection.value++
    currentSubSection.value = 0
  }
}

// 上一节
const prevSection = () => {
  if (currentSubSection.value > 0) {
    currentSubSection.value--
  } else if (currentSection.value > 0) {
    currentSection.value--
    currentSubSection.value = sections.value[currentSection.value].subsections.length - 1
  }
}

// 更改播放速度
const changeSpeed = (speed) => {
  playbackSpeed.value = speed
}

// 返回首页
const goHome = () => {
  router.push('/')
}
</script>

<template>
  <div class="course-container">
    <!-- 顶部导航 -->
    <div class="course-header">
      <div class="back-nav" @click="goHome">
        <el-icon><ArrowLeft /></el-icon>
        <span>返回首页</span>
      </div>
      <h1 class="course-title">{{ courseInfo.title }}</h1>
    </div>
    
    <div class="course-content">
      <!-- 左侧导航区 -->
      <div class="course-sidebar">
        <div class="sidebar-header">
          <h3>课程目录</h3>
        </div>
        
        <div class="chapter-list">
          <div 
            v-for="(section, sIndex) in sections" 
            :key="section.id" 
            class="chapter"
          >
            <div class="chapter-title">
              <span>{{ section.title }}</span>
              <el-icon v-if="section.complete"><SuccessFilled /></el-icon>
            </div>
            
            <div class="section-list">
              <div 
                v-for="(subsection, ssIndex) in section.subsections" 
                :key="subsection.id"
                :class="['section', currentSection === sIndex && currentSubSection === ssIndex ? 'active' : '']"
                @click="selectSection(sIndex, ssIndex)"
              >
                <div class="section-info">
                  <span class="section-title">{{ subsection.title }}</span>
                  <span class="section-duration">{{ subsection.duration }}</span>
                </div>
                <el-icon v-if="subsection.complete" class="complete-icon"><SuccessFilled /></el-icon>
              </div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 主内容区 -->
      <div class="main-content">
        <!-- 视频播放区 -->
        <div class="video-container">
          <div class="video-placeholder">
            <div class="play-button">
              <el-icon><CaretRight /></el-icon>
            </div>
            <div class="video-title">
              {{ sections[currentSection].subsections[currentSubSection].title }}
            </div>
          </div>
          
          <div class="video-controls">
            <div class="playback-control">
              <span>播放速度：</span>
              <el-select v-model="playbackSpeed" size="small" @change="changeSpeed">
                <el-option
                  v-for="speed in speedOptions"
                  :key="speed"
                  :label="`${speed}x`"
                  :value="speed"
                ></el-option>
              </el-select>
            </div>
            
            <div class="navigation-buttons">
              <el-button type="primary" size="small" @click="prevSection" :disabled="currentSection === 0 && currentSubSection === 0">
                <el-icon><ArrowLeft /></el-icon>上一节
              </el-button>
              <el-button type="primary" size="small" @click="nextSection" :disabled="currentSection === sections.length - 1 && currentSubSection === sections[currentSection].subsections.length - 1">
                下一节<el-icon><ArrowRight /></el-icon>
              </el-button>
            </div>
          </div>
        </div>
        
        <!-- 课件资料区 -->
        <div class="resource-section">
          <h3>课件资料</h3>
          <el-scrollbar height="180px">
            <div class="resource-list">
              <div v-for="resource in resources" :key="resource.id" class="resource-item">
                <div class="resource-info">
                  <div class="resource-icon" :class="resource.type"></div>
                  <div class="resource-name">{{ resource.name }}</div>
                  <div class="resource-size">{{ resource.size }}</div>
                </div>
                <el-button type="primary" size="small" circle>
                  <el-icon><Download /></el-icon>
                </el-button>
              </div>
            </div>
          </el-scrollbar>
        </div>
      </div>
      
      <!-- 右侧功能区 -->
      <div class="right-panel">
        <div class="instructor-info">
          <h3>讲师信息</h3>
          <div class="instructor-profile">
            <img :src="courseInfo.instructor.avatar" class="instructor-avatar" />
            <div class="instructor-details">
              <div class="instructor-name">{{ courseInfo.instructor.name }}</div>
              <div class="instructor-description">{{ courseInfo.instructor.description }}</div>
              <div class="answer-time">答疑时间：{{ courseInfo.instructor.answerTime }}</div>
            </div>
          </div>
        </div>
        
        <div class="discussion-section">
          <div class="section-header">
            <h3>讨论区</h3>
            <el-badge :value="discussions.length" :max="99" class="discussion-badge">
              <el-button type="primary" size="small">查看全部</el-button>
            </el-badge>
          </div>
          
          <el-scrollbar height="180px">
            <div class="discussion-list">
              <div v-for="discussion in discussions" :key="discussion.id" class="discussion-item">
                <div class="discussion-user">{{ discussion.user }}</div>
                <div class="discussion-content">{{ discussion.content }}</div>
                <div class="discussion-footer">
                  <span class="discussion-time">{{ discussion.time }}</span>
                  <span class="discussion-replies">回复({{ discussion.replies }})</span>
                </div>
              </div>
            </div>
          </el-scrollbar>
        </div>
        
        <div class="notes-section">
          <div class="section-header">
            <h3>笔记</h3>
            <el-button type="primary" size="small" circle>
              <el-icon><Edit /></el-icon>
            </el-button>
          </div>
          
          <el-input
            v-model="notes"
            type="textarea"
            placeholder="记录你的学习笔记..."
            :rows="8"
          ></el-input>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.course-container {
  width: 100%;
  min-height: 100vh;
  background-color: #F5F7FA;
  font-family: "思源黑体", "Source Han Sans", "Noto Sans CJK SC", sans-serif;
}

.course-header {
  display: flex;
  align-items: center;
  padding: 15px 30px;
  background-color: #fff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  position: sticky;
  top: 0;
  z-index: 10;
}

.back-nav {
  display: flex;
  align-items: center;
  cursor: pointer;
  margin-right: 20px;
  color: #2D8CF0;
  font-size: 14px;
}

.back-nav .el-icon {
  margin-right: 5px;
}

.course-title {
  margin: 0;
  font-size: 20px;
  font-weight: 600;
  color: #303133;
}

.course-content {
  display: flex;
  padding: 20px 30px;
  gap: 20px;
}

/* 左侧导航区 */
.course-sidebar {
  width: 280px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  overflow: hidden;
  flex-shrink: 0;
}

.sidebar-header {
  padding: 15px;
  border-bottom: 1px solid #EBEEF5;
}

.sidebar-header h3 {
  margin: 0;
  font-size: 16px;
  color: #303133;
}

.chapter-list {
  padding: 10px 0;
}

.chapter {
  margin-bottom: 10px;
}

.chapter-title {
  display: flex;
  justify-content: space-between;
  padding: 10px 15px;
  font-weight: 600;
  color: #303133;
  cursor: pointer;
  transition: background-color 0.3s;
}

.chapter-title:hover {
  background-color: #F5F7FA;
}

.section-list {
  padding-left: 15px;
}

.section {
  display: flex;
  justify-content: space-between;
  padding: 8px 15px;
  margin: 5px 0;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.3s;
  align-items: center;
}

.section:hover {
  background-color: #F5F7FA;
}

.section.active {
  background-color: #EBF5FF;
  border-left: 3px solid #2D8CF0;
}

.section-info {
  display: flex;
  flex-direction: column;
}

.section-title {
  font-size: 14px;
  color: #606266;
}

.section-duration {
  font-size: 12px;
  color: #909399;
  margin-top: 4px;
}

.complete-icon {
  color: #67C23A;
}

/* 主内容区 */
.main-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.video-container {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  overflow: hidden;
}

.video-placeholder {
  position: relative;
  width: 100%;
  height: 0;
  padding-bottom: 56.25%; /* 16:9 比例 */
  background-color: #000;
}

.play-button {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 60px;
  height: 60px;
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s;
}

.play-button .el-icon {
  font-size: 30px;
  color: #fff;
}

.play-button:hover {
  background-color: rgba(255, 255, 255, 0.3);
  transform: translate(-50%, -50%) scale(1.1);
}

.video-title {
  position: absolute;
  bottom: 20px;
  left: 20px;
  color: #fff;
  font-size: 16px;
  font-weight: 600;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.5);
}

.video-controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border-top: 1px solid #EBEEF5;
}

.playback-control {
  display: flex;
  align-items: center;
  gap: 10px;
}

.navigation-buttons {
  display: flex;
  gap: 10px;
}

.resource-section {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  padding: 15px;
}

.resource-section h3 {
  margin-top: 0;
  margin-bottom: 15px;
  font-size: 16px;
  color: #303133;
}

.resource-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.resource-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px;
  border-radius: 4px;
  transition: background-color 0.3s;
}

.resource-item:hover {
  background-color: #F5F7FA;
}

.resource-info {
  display: flex;
  align-items: center;
  gap: 10px;
}

.resource-icon {
  width: 24px;
  height: 24px;
  background-repeat: no-repeat;
  background-position: center;
  background-size: cover;
}

.resource-icon.pdf {
  background-color: #F56C6C;
  border-radius: 4px;
}

.resource-icon.image {
  background-color: #67C23A;
  border-radius: 4px;
}

.resource-icon.ppt {
  background-color: #E6A23C;
  border-radius: 4px;
}

.resource-icon.zip {
  background-color: #909399;
  border-radius: 4px;
}

.resource-name {
  font-size: 14px;
  color: #303133;
}

.resource-size {
  font-size: 12px;
  color: #909399;
  margin-left: 10px;
}

/* 右侧功能区 */
.right-panel {
  width: 300px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  flex-shrink: 0;
}

.instructor-info, .discussion-section, .notes-section {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  padding: 15px;
}

.instructor-info h3, .discussion-section h3, .notes-section h3 {
  margin-top: 0;
  margin-bottom: 15px;
  font-size: 16px;
  color: #303133;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.section-header h3 {
  margin: 0;
}

.instructor-profile {
  display: flex;
  gap: 15px;
}

.instructor-avatar {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  object-fit: cover;
}

.instructor-details {
  flex: 1;
}

.instructor-name {
  font-weight: 600;
  font-size: 16px;
  color: #303133;
  margin-bottom: 5px;
}

.instructor-description {
  font-size: 14px;
  color: #606266;
  margin-bottom: 5px;
}

.answer-time {
  font-size: 12px;
  color: #909399;
}

.discussion-list {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.discussion-item {
  padding: 10px;
  border-radius: 4px;
  background-color: #F5F7FA;
}

.discussion-user {
  font-weight: 600;
  font-size: 14px;
  color: #303133;
  margin-bottom: 5px;
}

.discussion-content {
  font-size: 14px;
  color: #606266;
  margin-bottom: 5px;
}

.discussion-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 12px;
  color: #909399;
}

.discussion-replies {
  cursor: pointer;
  color: #2D8CF0;
}

/* 动效 */
.section, .resource-item, .play-button, .el-button {
  transition: all 0.3s ease;
}

.el-button:hover {
  transform: scale(1.05);
}

@media (max-width: 1200px) {
  .course-content {
    flex-direction: column;
  }
  
  .course-sidebar, .main-content, .right-panel {
    width: 100%;
  }
}
</style> 