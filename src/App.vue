<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import axios from 'axios'

// 响应式数据源
const fridgeData = ref({
  temperature: '4.5',
  humidity: '65',
  doorStatus: '已关',
  lastUpdate: '刚刚',
  imageUrl: 'https://images.unsplash.com/photo-1583337130417-3346a1be7dee?q=80&w=1000&auto=format&fit=crop'
})

// 定义一个获取后端数据的函数
const fetchFridgeStatus = async () => {
  try {
    // 【预留接口】等后端的 FastAPI 写好后，把下面这行注释解开，换成真实的服务器 IP
    // const response = await axios.get('http://192.168.X.X:8000/api/status')
    // fridgeData.value = response.data

    // 【当前演示阶段】我们用代码模拟温度和湿度的微小波动，让你看到页面是“活”的
    const now = new Date()
    fridgeData.value.lastUpdate = now.toLocaleTimeString()
    // 模拟温度在 4.0 ~ 5.0 之间波动
    fridgeData.value.temperature = (4 + Math.random()).toFixed(1)
    // 模拟湿度在 60 ~ 70 之间波动
    fridgeData.value.humidity = Math.floor(60 + Math.random() * 10)

    console.log('数据已更新！')
  } catch (error) {
    console.error('获取冰箱状态失败:', error)
  }
}

// 设置一个定时器变量
let pollingTimer = null

// 当页面加载完成时 (onMounted)，开始定时轮询
onMounted(() => {
  // 刚进页面先拉取一次
  fetchFridgeStatus()
  // 随后每隔 5 秒钟拉取一次最新数据（5000 毫秒）
  pollingTimer = setInterval(fetchFridgeStatus, 5000)
})

// 当页面关闭时 (onUnmounted)，销毁定时器，防止内存泄漏
onUnmounted(() => {
  if (pollingTimer) {
    clearInterval(pollingTimer)
  }
})
</script>

<template>
  <div class="dashboard-container">
    <h1 class="title">FridgeMind AI —— 灵犀冰箱终端</h1>
    <p class="subtitle">状态最后更新: {{ fridgeData.lastUpdate }}</p>

    <el-row :gutter="20" class="status-cards">
      <el-col :span="8">
        <el-card shadow="hover" class="card-item">
          <h3>🌡️ 实时温度</h3>
          <div class="data-value">{{ fridgeData.temperature }} <span class="unit">℃</span></div>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card shadow="hover" class="card-item">
          <h3>💧 实时湿度</h3>
          <div class="data-value">{{ fridgeData.humidity }} <span class="unit">%</span></div>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card shadow="hover" class="card-item">
          <h3>🚪 箱门状态</h3>
          <div class="data-value" :class="{'door-open': fridgeData.doorStatus === '已开'}">
            {{ fridgeData.doorStatus }}
          </div>
        </el-card>
      </el-col>
    </el-row>

    <el-card shadow="always" class="image-card">
      <template #header>
        <div class="card-header">
          <span>📸 最新内部影像 (本地 NPU 畸变校正处理)</span>
        </div>
      </template>
      <div class="image-container">
        <el-image
          :src="fridgeData.imageUrl"
          fit="cover"
          class="fridge-image"
          :preview-src-list="[fridgeData.imageUrl]">
          <template #error>
            <div class="image-slot">等待硬件端摄像头上传图像...</div>
          </template>
        </el-image>
      </div>
    </el-card>
  </div>
</template>

<style scoped>
.dashboard-container {
  max-width: 1000px;
  margin: 0 auto;
  padding: 20px;
  font-family: sans-serif;
}
.title {
  text-align: center;
  color: #303133;
}
.subtitle {
  text-align: center;
  color: #909399;
  margin-bottom: 30px;
}
.status-cards {
  margin-bottom: 30px;
}
.card-item {
  text-align: center;
  border-radius: 12px;
}
.data-value {
  font-size: 36px;
  font-weight: bold;
  color: #409EFF;
  margin-top: 10px;
}
.unit {
  font-size: 18px;
  color: #606266;
}
.door-open {
  color: #F56C6C;
}
.image-card {
  border-radius: 12px;
}
.image-container {
  display: flex;
  justify-content: center;
  background-color: #f5f7fa;
  border-radius: 8px;
  overflow: hidden;
}
.fridge-image {
  width: 100%;
  max-height: 500px;
  border-radius: 8px;
}
</style>