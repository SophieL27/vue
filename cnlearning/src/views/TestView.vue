<script setup>
import { ref, reactive, computed, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'
import { Check, Close, Warning, ArrowLeft, ArrowRight } from '@element-plus/icons-vue'

const router = useRouter()

// 测试信息
const testInfo = reactive({
  id: 1,
  title: 'TCP/IP协议测试',
  total: 20,
  time: 30, // 分钟
  passScore: 60
})

// 当前题目
const currentQuestion = ref(0)

// 用户答案
const userAnswers = reactive({})

// 是否已提交
const isSubmitted = ref(false)

// 计时器
const timer = ref(testInfo.time * 60)
let timerInterval = null

// 测试题目
const questions = ref([
  {
    id: 1,
    type: 'single', // 单选题
    question: 'TCP/IP协议中，TCP属于哪一层？',
    options: [
      { key: 'A', value: '应用层' },
      { key: 'B', value: '传输层' },
      { key: 'C', value: '网络层' },
      { key: 'D', value: '数据链路层' }
    ],
    answer: 'B',
    explanation: 'TCP协议属于传输层，提供可靠的、面向连接的数据传输服务。'
  },
  {
    id: 2,
    type: 'single',
    question: 'IP地址192.168.0.1属于哪一类地址？',
    options: [
      { key: 'A', value: 'A类地址' },
      { key: 'B', value: 'B类地址' },
      { key: 'C', value: 'C类地址' },
      { key: 'D', value: '保留地址' }
    ],
    answer: 'C',
    explanation: '192.168.0.1是一个C类私有IP地址，C类地址范围是192.0.0.0至223.255.255.255。'
  },
  {
    id: 3,
    type: 'multiple', // 多选题
    question: '以下哪些协议工作在传输层？（多选）',
    options: [
      { key: 'A', value: 'TCP' },
      { key: 'B', value: 'UDP' },
      { key: 'C', value: 'IP' },
      { key: 'D', value: 'HTTP' }
    ],
    answer: ['A', 'B'],
    explanation: 'TCP和UDP都是传输层协议，IP是网络层协议，HTTP是应用层协议。'
  },
  {
    id: 4,
    type: 'single',
    question: 'TCP协议使用三次握手建立连接，使用几次握手断开连接？',
    options: [
      { key: 'A', value: '两次握手' },
      { key: 'B', value: '三次握手' },
      { key: 'C', value: '四次握手' },
      { key: 'D', value: '一次握手' }
    ],
    answer: 'C',
    explanation: 'TCP断开连接使用四次握手（四次挥手）。'
  },
  {
    id: 5,
    type: 'single',
    question: '下图所示的网络拓扑结构是什么类型？',
    image: 'https://picsum.photos/400/200?random=1',
    options: [
      { key: 'A', value: '星型拓扑' },
      { key: 'B', value: '总线型拓扑' },
      { key: 'C', value: '环形拓扑' },
      { key: 'D', value: '网状拓扑' }
    ],
    answer: 'A',
    explanation: '图中展示的是一个星型拓扑结构，所有节点连接到中央节点。'
  },
  {
    id: 6,
    type: 'multiple',
    question: '以下关于子网掩码的说法，正确的有：（多选）',
    options: [
      { key: 'A', value: '子网掩码用于划分子网' },
      { key: 'B', value: '子网掩码全部由0和1组成' },
      { key: 'C', value: '子网掩码中的1必须连续' },
      { key: 'D', value: '192.168.1.0/24表示子网掩码是255.255.255.0' }
    ],
    answer: ['A', 'B', 'C', 'D'],
    explanation: '这些说法都是正确的。子网掩码用于划分子网，由0和1组成，1必须连续出现，/24表示前24位都是1，即255.255.255.0。'
  },
  {
    id: 7,
    type: 'single',
    question: '以下代码片段执行了什么操作？\n```\nping 192.168.1.1\n```',
    options: [
      { key: 'A', value: '检测与192.168.1.1的连接状态' },
      { key: 'B', value: '获取192.168.1.1的MAC地址' },
      { key: 'C', value: '获取192.168.1.1的路由信息' },
      { key: 'D', value: '建立与192.168.1.1的TCP连接' }
    ],
    answer: 'A',
    explanation: 'ping命令使用ICMP协议检测网络连接状态，测试与目标主机的连通性。'
  }
])

// 获取当前题目
const currentQuestionData = computed(() => {
  return questions.value[currentQuestion.value]
})

// 进度百分比
const progressPercentage = computed(() => {
  return Math.round(((currentQuestion.value + 1) / questions.value.length) * 100)
})

// 格式化时间
const formattedTime = computed(() => {
  const minutes = Math.floor(timer.value / 60)
  const seconds = timer.value % 60
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
})

// 得分
const score = computed(() => {
  if (!isSubmitted.value) return 0
  
  let correctCount = 0
  
  questions.value.forEach(question => {
    const userAnswer = userAnswers[question.id]
    if (!userAnswer) return
    
    if (question.type === 'single' && userAnswer === question.answer) {
      correctCount++
    } else if (question.type === 'multiple') {
      const isCorrect = Array.isArray(userAnswer) && 
                       Array.isArray(question.answer) && 
                       userAnswer.length === question.answer.length && 
                       userAnswer.every(ans => question.answer.includes(ans))
      if (isCorrect) correctCount++
    }
  })
  
  return Math.round((correctCount / questions.value.length) * 100)
})

// 是否通过测试
const isPassed = computed(() => {
  return score.value >= testInfo.passScore
})

// 下一题
const nextQuestion = () => {
  if (currentQuestion.value < questions.value.length - 1) {
    currentQuestion.value++
  }
}

// 上一题
const prevQuestion = () => {
  if (currentQuestion.value > 0) {
    currentQuestion.value--
  }
}

// 选择答案
const selectAnswer = (key) => {
  if (isSubmitted.value) return
  
  const question = currentQuestionData.value
  
  if (question.type === 'single') {
    userAnswers[question.id] = key
  } else if (question.type === 'multiple') {
    if (!userAnswers[question.id]) {
      userAnswers[question.id] = []
    }
    
    const index = userAnswers[question.id].indexOf(key)
    if (index === -1) {
      userAnswers[question.id].push(key)
    } else {
      userAnswers[question.id].splice(index, 1)
    }
  }
}

// 提交测试
const submitTest = () => {
  clearInterval(timerInterval)
  isSubmitted.value = true
}

// 检查是否选中
const isSelected = (key) => {
  const question = currentQuestionData.value
  const userAnswer = userAnswers[question.id]
  
  if (!userAnswer) return false
  
  if (question.type === 'single') {
    return userAnswer === key
  } else if (question.type === 'multiple') {
    return userAnswer.includes(key)
  }
  
  return false
}

// 检查是否正确
const isCorrect = (key) => {
  if (!isSubmitted.value) return null
  
  const question = currentQuestionData.value
  
  if (question.type === 'single') {
    return key === question.answer
  } else if (question.type === 'multiple') {
    return question.answer.includes(key)
  }
  
  return false
}

// 返回首页
const goHome = () => {
  router.push('/')
}

// 开始计时
const startTimer = () => {
  timerInterval = setInterval(() => {
    if (timer.value > 0) {
      timer.value--
    } else {
      submitTest()
    }
  }, 1000)
}

// 组件挂载时开始计时
onMounted(() => {
  startTimer()
})

// 组件销毁时清除计时器
onUnmounted(() => {
  clearInterval(timerInterval)
})
</script>

<template>
  <div class="test-container">
    <!-- 顶部导航 -->
    <div class="test-header">
      <div class="back-nav" @click="goHome">
        <el-icon><ArrowLeft /></el-icon>
        <span>返回首页</span>
      </div>
      <h1 class="test-title">{{ testInfo.title }}</h1>
      <div class="test-timer" :class="{ 'warning': timer < 300 }">
        <el-icon><Warning v-if="timer < 300" /></el-icon>
        {{ formattedTime }}
      </div>
    </div>
    
    <!-- 结果页 -->
    <div v-if="isSubmitted" class="result-page">
      <div class="result-card">
        <div class="result-header">
          <h2>测试结果</h2>
        </div>
        
        <div class="result-content">
          <div class="score-section">
            <div class="score-circle" :class="{ 'pass': isPassed, 'fail': !isPassed }">
              {{ score }}
            </div>
            <div class="score-text">
              {{ isPassed ? '恭喜您，测试通过！' : '很遗憾，测试未通过。' }}
            </div>
          </div>
          
          <div class="result-details">
            <div class="detail-item">
              <span class="detail-label">总题数</span>
              <span class="detail-value">{{ questions.length }}题</span>
            </div>
            <div class="detail-item">
              <span class="detail-label">答对题数</span>
              <span class="detail-value">{{ Math.round(score / 100 * questions.length) }}题</span>
            </div>
            <div class="detail-item">
              <span class="detail-label">用时</span>
              <span class="detail-value">{{ testInfo.time - Math.floor(timer / 60) }}分钟</span>
            </div>
            <div class="detail-item">
              <span class="detail-label">状态</span>
              <span class="detail-value" :class="{ 'pass-text': isPassed, 'fail-text': !isPassed }">
                {{ isPassed ? '通过' : '未通过' }}
              </span>
            </div>
          </div>
        </div>
        
        <div class="result-footer">
          <el-button type="primary" @click="goHome">返回首页</el-button>
          <el-button type="primary" plain>查看错题</el-button>
        </div>
      </div>
    </div>
    
    <!-- 测试内容 -->
    <div v-else class="test-content">
      <!-- 左侧题目区 -->
      <div class="question-area">
        <div class="question-content">
          <div class="question-header">
            <div class="question-number">
              <span class="number">{{ currentQuestion + 1 }}</span>
              <span class="type">{{ currentQuestionData.type === 'single' ? '单选题' : '多选题' }}</span>
            </div>
          </div>
          
          <div class="question-body">
            <div class="question-text">{{ currentQuestionData.question }}</div>
            
            <div class="question-image" v-if="currentQuestionData.image">
              <img :src="currentQuestionData.image" alt="问题图片" />
            </div>
            
            <div class="options-list">
              <div 
                v-for="option in currentQuestionData.options" 
                :key="option.key"
                class="option-item"
                :class="{
                  'selected': isSelected(option.key),
                  'correct': isCorrect(option.key) === true,
                  'wrong': isSelected(option.key) && isCorrect(option.key) === false
                }"
                @click="selectAnswer(option.key)"
              >
                <div class="option-marker">
                  <div v-if="currentQuestionData.type === 'single'" class="marker single"></div>
                  <div v-else class="marker multiple"></div>
                  <div v-if="isSubmitted && isCorrect(option.key)" class="marker-icon correct">
                    <el-icon><Check /></el-icon>
                  </div>
                  <div v-if="isSubmitted && isSelected(option.key) && !isCorrect(option.key)" class="marker-icon wrong">
                    <el-icon><Close /></el-icon>
                  </div>
                </div>
                <div class="option-key">{{ option.key }}</div>
                <div class="option-value">{{ option.value }}</div>
              </div>
            </div>
            
            <div class="explanation" v-if="isSubmitted">
              <div class="explanation-header">解析</div>
              <div class="explanation-content">{{ currentQuestionData.explanation }}</div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 右侧导航区 -->
      <div class="navigation-area">
        <div class="progress-section">
          <div class="progress-text">
            <span>题目进度</span>
            <span>{{ currentQuestion + 1 }}/{{ questions.length }}</span>
          </div>
          <el-progress :percentage="progressPercentage" :stroke-width="10" />
        </div>
        
        <div class="questions-grid">
          <div 
            v-for="(question, index) in questions" 
            :key="question.id"
            class="question-dot"
            :class="{
              'current': index === currentQuestion,
              'answered': userAnswers[question.id]
            }"
            @click="currentQuestion = index"
          >
            {{ index + 1 }}
          </div>
        </div>
        
        <div class="navigation-buttons">
          <el-button type="primary" @click="prevQuestion" :disabled="currentQuestion === 0">
            <el-icon><ArrowLeft /></el-icon>上一题
          </el-button>
          
          <el-button type="primary" @click="nextQuestion" :disabled="currentQuestion === questions.length - 1">
            下一题<el-icon><ArrowRight /></el-icon>
          </el-button>
        </div>
        
        <div class="submit-section">
          <el-button type="danger" @click="submitTest">提交试卷</el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.test-container {
  width: 100%;
  min-height: 100vh;
  background-color: #F5F7FA;
  font-family: "思源黑体", "Source Han Sans", "Noto Sans CJK SC", sans-serif;
}

.test-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
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
  color: #2D8CF0;
  font-size: 14px;
}

.back-nav .el-icon {
  margin-right: 5px;
}

.test-title {
  margin: 0;
  font-size: 20px;
  font-weight: 600;
  color: #303133;
}

.test-timer {
  font-size: 16px;
  font-weight: 600;
  color: #303133;
  display: flex;
  align-items: center;
  gap: 5px;
}

.test-timer.warning {
  color: #F56C6C;
}

.test-content {
  display: flex;
  padding: 20px 30px;
  gap: 20px;
}

/* 左侧题目区 */
.question-area {
  flex: 1;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  padding: 20px;
}

.question-header {
  margin-bottom: 20px;
}

.question-number {
  display: flex;
  align-items: center;
  gap: 10px;
}

.question-number .number {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
}

.question-number .type {
  font-size: 14px;
  color: #909399;
  background-color: #F5F7FA;
  padding: 2px 8px;
  border-radius: 4px;
}

.question-text {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
  margin-bottom: 20px;
  line-height: 1.5;
}

.question-image {
  margin-bottom: 20px;
}

.question-image img {
  max-width: 100%;
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.options-list {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.option-item {
  display: flex;
  align-items: center;
  padding: 12px 15px;
  border-radius: 4px;
  background-color: #F5F7FA;
  cursor: pointer;
  transition: all 0.3s;
  position: relative;
}

.option-item:hover {
  background-color: #EBF5FF;
}

.option-item.selected {
  background-color: #E6F1FC;
  border: 1px solid #2D8CF0;
}

.option-item.correct {
  background-color: #F0F9EB;
  border: 1px solid #67C23A;
}

.option-item.wrong {
  background-color: #FEF0F0;
  border: 1px solid #F56C6C;
}

.option-marker {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  margin-right: 10px;
  position: relative;
}

.marker {
  width: 18px;
  height: 18px;
  border-radius: 50%;
  border: 2px solid #DCDFE6;
  background-color: #fff;
}

.marker.multiple {
  border-radius: 4px;
}

.option-item.selected .marker {
  border-color: #2D8CF0;
}

.option-item.selected .marker.single {
  background-color: #2D8CF0;
}

.option-item.selected .marker.multiple {
  background-color: #2D8CF0;
  position: relative;
}

.option-item.selected .marker.multiple::after {
  content: '✓';
  color: white;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 12px;
}

.marker-icon {
  position: absolute;
  top: 0;
  right: -8px;
  width: 16px;
  height: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
}

.marker-icon.correct {
  background-color: #67C23A;
  color: white;
}

.marker-icon.wrong {
  background-color: #F56C6C;
  color: white;
}

.option-key {
  width: 24px;
  text-align: center;
  font-weight: 600;
  color: #303133;
  margin-right: 10px;
}

.option-value {
  flex: 1;
  font-size: 14px;
  color: #606266;
}

.explanation {
  margin-top: 20px;
  padding: 15px;
  background-color: #F5F7FA;
  border-radius: 4px;
  border-left: 3px solid #2D8CF0;
}

.explanation-header {
  font-weight: 600;
  color: #303133;
  margin-bottom: 8px;
}

.explanation-content {
  font-size: 14px;
  color: #606266;
  line-height: 1.5;
}

/* 右侧导航区 */
.navigation-area {
  width: 280px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  flex-shrink: 0;
}

.progress-section {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  padding: 15px;
}

.progress-text {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
  font-size: 14px;
  color: #606266;
}

.questions-grid {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  padding: 15px;
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.question-dot {
  width: 36px;
  height: 36px;
  border-radius: 4px;
  background-color: #F5F7FA;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s;
  font-size: 14px;
  color: #606266;
}

.question-dot:hover {
  background-color: #EBF5FF;
}

.question-dot.current {
  background-color: #2D8CF0;
  color: white;
}

.question-dot.answered {
  background-color: #67C23A;
  color: white;
}

.question-dot.current.answered {
  background-color: #2D8CF0;
  color: white;
  border: 2px solid #67C23A;
}

.navigation-buttons {
  display: flex;
  justify-content: space-between;
}

.submit-section {
  margin-top: auto;
}

.submit-section .el-button {
  width: 100%;
}

/* 结果页 */
.result-page {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 40px 30px;
}

.result-card {
  width: 100%;
  max-width: 600px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  overflow: hidden;
}

.result-header {
  padding: 20px;
  background-color: #2D8CF0;
  color: white;
  text-align: center;
}

.result-header h2 {
  margin: 0;
  font-size: 20px;
}

.result-content {
  padding: 30px;
}

.score-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 30px;
}

.score-circle {
  width: 120px;
  height: 120px;
  border-radius: 50%;
  background-color: #F5F7FA;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 36px;
  font-weight: 700;
  color: #303133;
  margin-bottom: 15px;
}

.score-circle.pass {
  background-color: #F0F9EB;
  color: #67C23A;
  border: 4px solid #67C23A;
}

.score-circle.fail {
  background-color: #FEF0F0;
  color: #F56C6C;
  border: 4px solid #F56C6C;
}

.score-text {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
}

.result-details {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}

.detail-item {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.detail-label {
  font-size: 14px;
  color: #909399;
}

.detail-value {
  font-size: 16px;
  font-weight: 600;
  color: #303133;
}

.pass-text {
  color: #67C23A;
}

.fail-text {
  color: #F56C6C;
}

.result-footer {
  padding: 20px;
  display: flex;
  justify-content: center;
  gap: 20px;
  border-top: 1px solid #EBEEF5;
}

/* 动效 */
.option-item, .question-dot, .el-button {
  transition: all 0.3s ease;
}

.el-button:hover {
  transform: scale(1.05);
}

/* 响应式布局 */
@media (max-width: 768px) {
  .test-content {
    flex-direction: column;
  }
  
  .navigation-area {
    width: 100%;
    flex-direction: row;
    flex-wrap: wrap;
  }
  
  .progress-section, .questions-grid {
    flex: 1;
    min-width: 200px;
  }
  
  .navigation-buttons, .submit-section {
    width: 100%;
  }
}
</style> 