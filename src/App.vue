<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const fridgeData = ref({
  temperature: '4.5',
  humidity: '65',
  doorStatus: '已关',
  networkType: 'Wi-Fi (静态IP)',
  lastUpdate: '刚刚',
  imageUrl: 'https://images.unsplash.com/photo-1583337130417-3346a1be7dee?q=80&w=1000&auto=format&fit=crop'
})

const fetchFridgeStatus = async () => {
  try {
    const now = new Date()
    fridgeData.value.lastUpdate = now.toLocaleTimeString()
    fridgeData.value.temperature = (4 + Math.random()).toFixed(1)
    fridgeData.value.humidity = Math.floor(60 + Math.random() * 10)
  } catch (error) {
    console.error('获取状态失败:', error)
  }
}

let pollingTimer = null

onMounted(() => {
  fetchFridgeStatus()
  pollingTimer = setInterval(fetchFridgeStatus, 5000)
})

onUnmounted(() => {
  if (pollingTimer) {
    clearInterval(pollingTimer)
  }
})
</script>

<template>
  <div class="dashboard-wrapper">
    <div class="dashboard-container">

      <div class="header-section">
        <h1 class="title">FridgeMind AI <span class="badge">终端</span></h1>
        <p class="subtitle">
          Powered by Purple Pi OH RK3566 <span class="dot-divider">•</span> 最后同步: <span class="highlight">{{ fridgeData.lastUpdate }}</span>
        </p>
      </div>

      <el-row :gutter="24" class="status-cards">
        <el-col :span="6">
          <div class="glass-card hover-lift">
            <div class="card-icon temp-icon">🌡️</div>
            <div class="card-content">
              <div class="card-title">内部温度 (I2C)</div>
              <div class="data-value temp-text">{{ fridgeData.temperature }}<span class="unit">℃</span></div>
            </div>
          </div>
        </el-col>

        <el-col :span="6">
          <div class="glass-card hover-lift">
            <div class="card-icon hum-icon">💧</div>
            <div class="card-content">
              <div class="card-title">内部湿度 (I2C)</div>
              <div class="data-value hum-text">{{ fridgeData.humidity }}<span class="unit">%</span></div>
            </div>
          </div>
        </el-col>

        <el-col :span="6">
          <div class="glass-card hover-lift">
            <div class="card-icon door-icon">🚪</div>
            <div class="card-content">
              <div class="card-title">门磁中断状态</div>
              <div class="data-value" :class="fridgeData.doorStatus === '已开' ? 'door-open' : 'door-closed'">
                {{ fridgeData.doorStatus }}
              </div>
            </div>
          </div>
        </el-col>

        <el-col :span="6">
          <div class="glass-card hover-lift">
            <div class="card-icon net-icon">
              <div class="pulse-dot"></div>
            </div>
            <div class="card-content">
              <div class="card-title">局域网连接</div>
              <div class="data-value net-text">{{ fridgeData.networkType }}</div>
            </div>
          </div>
        </el-col>
      </el-row>

      <div class="glass-card image-section hover-glow">
        <div class="image-header">
          <div class="header-left">
            <span class="camera-icon">📸</span>
            <span class="header-title">全景食材存照</span>
          </div>
          <div class="header-tags">
            <span class="custom-tag tag-green">关门自动抓拍</span>
            <span class="custom-tag tag-orange">NPU 畸变校正</span>
            <span class="custom-tag tag-blue">720P 压缩回传</span>
          </div>
        </div>

        <div class="image-container">
          <el-image
            :src="fridgeData.imageUrl"
            fit="cover"
            class="fridge-image"
            :preview-src-list="[fridgeData.imageUrl]">
            <template #error>
              <div class="image-slot">
                <i class="el-icon-picture-outline"></i>
                <p>等待硬件端从深度休眠中唤醒并上传图像...</p>
              </div>
            </template>
          </el-image>
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
/* 1. 梦幻柔和背景：保持白色基调，辅以极淡的蓝、粉、青色散光 */
.dashboard-wrapper {
  min-height: 100vh;
  background-color: #f8fafc;
  background-image:
    radial-gradient(at 0% 0%, rgba(224, 242, 254, 0.6) 0px, transparent 50%),
    radial-gradient(at 100% 0%, rgba(254, 226, 226, 0.5) 0px, transparent 50%),
    radial-gradient(at 100% 100%, rgba(220, 252, 231, 0.6) 0px, transparent 50%);
  padding: 40px 20px;
  font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  color: #334155;
}

.dashboard-container {
  max-width: 1100px;
  margin: 0 auto;
}

/* 2. 头部排版优化 */
.header-section {
  text-align: center;
  margin-bottom: 40px;
}
.title {
  font-size: 36px;
  font-weight: 800;
  letter-spacing: -0.5px;
  color: #0f172a;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
}
.badge {
  font-size: 14px;
  font-weight: 600;
  background: linear-gradient(135deg, #3b82f6, #8b5cf6);
  color: white;
  padding: 4px 12px;
  border-radius: 20px;
  letter-spacing: 1px;
}
.subtitle {
  font-size: 15px;
  color: #64748b;
  font-weight: 500;
}
.dot-divider {
  margin: 0 8px;
  color: #cbd5e1;
}
.highlight {
  color: #3b82f6;
  font-weight: 700;
}

/* 3. 核心液态玻璃卡片 (Glassmorphism) */
.glass-card {
  background: rgba(255, 255, 255, 0.65);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.8);
  border-radius: 24px;
  box-shadow: 0 10px 30px rgba(15, 23, 42, 0.04), inset 0 1px 0 rgba(255, 255, 255, 1);
  padding: 24px;
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  display: flex;
  align-items: center;
  gap: 16px;
}

/* 4. 极致交互动画 */
.hover-lift:hover {
  transform: translateY(-8px) scale(1.02);
  box-shadow: 0 20px 40px rgba(15, 23, 42, 0.08), inset 0 1px 0 rgba(255, 255, 255, 1);
  background: rgba(255, 255, 255, 0.85);
}
.hover-glow:hover {
  box-shadow: 0 20px 50px rgba(59, 130, 246, 0.1), inset 0 1px 0 rgba(255, 255, 255, 1);
}

/* 5. 卡片内部元素设计 */
.card-icon {
  font-size: 32px;
  width: 56px;
  height: 56px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 16px;
  background: white;
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
}
.card-content {
  flex: 1;
}
.card-title {
  font-size: 13px;
  color: #64748b;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 4px;
}
.data-value {
  font-size: 28px;
  font-weight: 800;
  letter-spacing: -0.5px;
}
.unit {
  font-size: 16px;
  font-weight: 600;
  color: #94a3b8;
  margin-left: 2px;
}

/* 色彩点缀，多彩但不艳丽 */
.temp-text { color: #f97316; }
.hum-text { color: #0ea5e9; }
.door-closed { color: #10b981; }
.door-open { color: #ef4444; }
.net-text { color: #8b5cf6; font-size: 20px;}

/* 动态网络呼吸圆点 */
.pulse-dot {
  width: 14px;
  height: 14px;
  background-color: #10b981;
  border-radius: 50%;
  box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.7);
  animation: pulse 2s infinite cubic-bezier(0.66, 0, 0, 1);
}
@keyframes pulse {
  to { box-shadow: 0 0 0 12px rgba(16, 185, 129, 0); }
}

/* 6. 图像区专属样式 */
.image-section {
  margin-top: 30px;
  flex-direction: column;
  align-items: stretch;
  padding: 30px;
}
.image-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
}
.header-left {
  display: flex;
  align-items: center;
  gap: 10px;
}
.camera-icon { font-size: 24px; }
.header-title {
  font-size: 20px;
  font-weight: 700;
  color: #0f172a;
}

/* 优雅的自定义标签 */
.header-tags {
  display: flex;
  gap: 10px;
}
.custom-tag {
  padding: 6px 14px;
  border-radius: 12px;
  font-size: 13px;
  font-weight: 600;
  backdrop-filter: blur(10px);
}
.tag-green { background: rgba(16, 185, 129, 0.1); color: #059669; border: 1px solid rgba(16, 185, 129, 0.2); }
.tag-orange { background: rgba(245, 158, 11, 0.1); color: #d97706; border: 1px solid rgba(245, 158, 11, 0.2); }
.tag-blue { background: rgba(59, 130, 246, 0.1); color: #2563eb; border: 1px solid rgba(59, 130, 246, 0.2); }

/* 图片容器优化 */
.image-container {
  width: 100%;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(0,0,0,0.08);
  position: relative;
}
.fridge-image {
  width: 100%;
  height: 500px;
  object-fit: cover;
  transition: transform 0.6s cubic-bezier(0.25, 0.46, 0.45, 0.94);
}
.image-container:hover .fridge-image {
  transform: scale(1.03); /* 鼠标悬停时极度丝滑的微距放大 */
}
.image-slot {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 500px;
  background: #f1f5f9;
  color: #64748b;
  font-size: 15px;
  font-weight: 500;
}
</style>