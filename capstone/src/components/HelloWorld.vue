<template>
  <div class="page-container">
    <!-- Hero Section -->
    <div class="hero-wrapper">
      <div class="hero-content">
        <div class="text-section">
          <h1>GPS 위성신호를 활용한 우주기상 탐사(sensing)</h1>
          <p class="subtitle">지도교수 방유진교수님<br /></p>
          <div class="cta-buttons">
            <button class="btn primary" @click="showCalendar = !showCalendar">Start Exploring</button>
            <a href="#team-section" class="btn secondary" @click.prevent="toggleTeam">
              {{ isTeamVisible ? 'Close' : 'Team Member' }}
            </a>
          </div>
          <transition name="fade">
            <div v-if="showCalendar" class="calendar-ui">
              <label for="date-picker">날짜 선택</label>
              <input id="date-picker" type="date" @change="selectDate" class="calendar-picker" />
            </div>
          </transition>
        </div>
        <div class="visual-effect">
          <div class="glow-circle"></div>
        </div>
      </div>
    </div>

    <!-- Graph Section -->
    <div v-if="selectedDate" class="graph-section">
      <h2 class="graph-title">{{ selectedDate }} TEC Graphs</h2>
      <div class="hour-slider">
        <label for="hourRange">시간: {{ selectedHour }}시</label>
        <input id="hourRange" type="range" min="0" max="22" step="2" v-model="selectedHour" />
      </div>

      <div class="graph-container">
        <div class="graph-box" v-for="(title, index) in graphTitles" :key="index">
          <h3>{{ title }}</h3>
          <div class="az-info">
            <p>Az: {{ getAzValue(index) }}</p>
          </div>
          <div class="tec-graph">
            <div class="bar" v-for="(value, i) in generateDummyGraphData(index)" :key="i"
              :style="{ height: `${value}px` }"></div>
          </div>
        </div>
      </div>
    </div>

    <!-- Team Member Section -->
    <div id="team-section" class="team-section" v-show="isTeamVisible">
      <h2 class="team-title">Team Members</h2>
      <div class="team-grid">
        <div class="member-card" v-for="member in members" :key="member.name">
          <h3>{{ member.name }}</h3>
          <p v-html="member.task"></p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, nextTick } from 'vue'

const isTeamVisible = ref(false)
const showCalendar = ref(false)
const selectedDate = ref('')
const selectedHour = ref(0)
const graphTitles = ['Init_Az의 TEC', 'Opt_Az의 TEC', 'ML_Az의 TEC', 'GIMs의 TEC']

// 추후 API 데이터 입력 가능하도록 Az 값 구조화
const azValues = ref([
  [210, 220, 230, 240, 250, 260, 270, 280, 290, 300, 310, 320], // Init_Az
  [215, 225, 235, 245, 255, 265, 275, 285, 295, 305, 315, 325], // Opt_Az
  [220, 230, 240, 250, 260, 270, 280, 290, 300, 310, 320, 330], // ML_Az
])

const toggleTeam = () => {
  isTeamVisible.value = !isTeamVisible.value
  if (isTeamVisible.value) {
    nextTick(() => {
      const el = document.getElementById('team-section')
      if (el) el.scrollIntoView({ behavior: 'smooth' })
    })
  }
}

const selectDate = (e) => {
  const date = e.target.value
  if (!date) return
  selectedDate.value = date
  nextTick(() => {
    const graph = document.querySelector('.graph-section')
    if (graph) graph.scrollIntoView({ behavior: 'smooth' })
  })
}

const getAzValue = (graphIndex) => {
  const hourIndex = selectedHour.value / 2
  return azValues.value[graphIndex]?.[hourIndex] || 'N/A'
}

const generateDummyGraphData = (graphIndex) => {
  return Array.from({ length: 20 }, (_, i) => 40 + Math.abs(Math.sin((i + selectedHour.value + graphIndex)) * 50))
}

const members = [
  { name: '장창수', task: '맡은 일<br />맡은 일' },
  { name: '배서영', task: 'AZ파라미터 수집<br />GPS 데이터 수집<br />SSN+F10.7 데이터 수집<br />XGboost 모델 설계<br />TEC 그래프 시각화<br />프론트엔드 구축' },
  { name: '권지원', task: 'RF 머신러닝 모델 설계<br />Init Az 넬더 미드 기법으로 최적화' },
  { name: '박지연', task: '맡은 일<br />밑은 일' },
  { name: '김예진', task: '맡은 일<br />맡은 일' },
]
</script>


<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Outfit:wght@400;600;700&display=swap');

html {
  scroll-behavior: smooth;
}

html, body, #app {
  margin: 0;
  padding: 0;
  width: 100vw;
  height: 100vh;
  min-height: 100vh;
  box-sizing: border-box;
  overflow-x: hidden;
  font-family: 'Outfit', sans-serif;
  background: #0b0d1a;
  scroll-behavior: smooth;
}

.page-container {
  width: 100%;
  min-height: 100vh;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

.hero-wrapper {
  width: 100vw;
  height: 100vh;
  background: radial-gradient(circle at 20% 40%, rgba(70, 60, 130, 0.6), transparent 70%),
              radial-gradient(circle at 80% 70%, rgba(0, 150, 255, 0.4), transparent 80%),
              linear-gradient(135deg, #0d0d1a, #111223, #090a13);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
  margin: 0;
  overflow: hidden;
  color: #ffffff;
  position: relative;
}

.hero-content {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  width: 90%;
  max-width: 1200px;
  gap: 2.5rem;
  padding: 2rem;
  z-index: 2;
}

.text-section {
  flex: 1;
  max-width: 600px;
}

.text-section h1 {
  font-size: 3.5rem;
  font-weight: 700;
  letter-spacing: -0.02em;
  margin-bottom: 1.2rem;
  line-height: 1.2;
  color: #f0f0f0;
}

.subtitle {
  font-size: 1.2rem;
  line-height: 1.7;
  color: #cfcfe9;
  margin-bottom: 2.5rem;
}

.cta-buttons {
  display: flex;
  gap: 1rem;
}

.btn {
  padding: 0.85rem 1.6rem;
  border-radius: 14px;
  font-weight: 600;
  font-size: 1rem;
  text-decoration: none;
  transition: all 0.3s ease-in-out;
  position: relative;
  cursor: pointer;
}

.btn.primary {
  background: rgba(31, 92, 255, 0.1);
  border: 1px solid #1f5cff;
  color: #ffffff;
  box-shadow: 0 0 12px rgba(31, 92, 255, 0.25);
}

.btn.primary:hover {
  background: rgba(31, 92, 255, 0.25);
  box-shadow: 0 0 16px rgba(31, 92, 255, 0.5);
}

.btn.secondary {
  border: 1px solid #888bff;
  color: #cfd0ff;
  background: transparent;
}

.btn.secondary:hover {
  background: rgba(255, 255, 255, 0.05);
  border-color: #cfd0ff;
}

.calendar-picker {
  margin-top: 1rem;
  margin-left : 1rem;
  padding: 0.5rem;
  font-size: 1rem;
  border-radius: 6px;
  border: none;
  background: #1d2035;
  color: #fff;
}

.visual-effect {
  flex: 1;
  max-width: 500px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.glow-circle {
  width: 260px;
  height: 260px;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(173,216,230,0.4) 0%, transparent 70%);
  animation: pulse 4s infinite;
  filter: blur(12px);
  position: absolute;
  top: 0;
  left: 0;
}

@keyframes pulse {
  0%, 100% {
    transform: scale(1);
    opacity: 0.4;
  }
  50% {
    transform: scale(1.15);
    opacity: 0.7;
  }
}

@media (max-width: 768px) {
  .hero-content {
    flex-direction: column;
    text-align: center;
  }

  .text-section h1 {
    font-size: 2.4rem;
  }

  .visual-effect {
    margin-top: 2rem;
  }
}

.graph-section {
  padding: 3rem 2rem;
  background: #0d0d1a;
  color: #fff;
}

.graph-title {
  font-size: 1.8rem;
  text-align: center;
  margin-bottom: 2rem;
}

.graph-container {
  display: grid;
  grid-template-columns: 1fr;
  gap: 2rem;
  max-width: 1200px;
  margin: 0 auto;
}

.graph-box h3 {
  font-size: 1.2rem;
  margin-bottom: 0.5rem;
}

.graph-scroll-wrapper {
  overflow-x: auto;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.05);
  padding: 1rem 0.5rem;
}
.graph-scroll {
  display: flex;
  width: max-content;
  gap: 12px;
  padding-left: 8px;
}
.graph-bar {
  width: 40px;
  border-radius: 4px;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  transition: all 0.3s ease;
  position: relative;
}
.bar-label {
  font-size: 0.85rem;
  position: absolute;
  bottom: -20px;
  color: #ddd;
}
.graph-placeholder {
  display: inline-block;
  width: 1000px;
  height: 100px;
  background: #222c44;
  border: 1px dashed #888;
  color: #aaa;
  font-size: 1rem;
  line-height: 100px;
  text-align: center;
}

.team-section {
  padding: 5rem 2rem 6rem;
  text-align: center;
  background: linear-gradient(to bottom, #0b0d1a, #111223);
  border-top: 1px solid rgba(255, 255, 255, 0.05);
}

.team-title {
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 2rem;
  color: #e0e4ff;
}

.team-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 2rem;
  justify-items: center;
}

.member-card {
  background: linear-gradient(145deg, rgba(31, 92, 255, 0.05), rgba(255, 255, 255, 0.02));
  border: 1px solid rgba(120, 150, 255, 0.25);
  border-radius: 16px;
  padding: 1.5rem;
  width: 100%;
  max-width: 260px;
  box-shadow: 0 0 10px rgba(120, 150, 255, 0.15);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  color: #dce0ff;
}

.member-card h3 {
  margin-bottom: 0.8rem;
  font-size: 1.3rem;
  font-weight: 600;
  color: #ffffff;
}

.member-card p {
  font-size: 0.95rem;
  line-height: 1.5;
  color: #c3c8ff;
}

.member-card:hover {
  transform: translateY(-6px);
  box-shadow: 0 0 18px rgba(100, 180, 255, 0.3);
}

.hour-slider {
  max-width: 600px;
  margin: 0 auto 2rem;
  color: #eee;
  text-align: center;
}
input[type="range"] {
  width: 100%;
  margin-top: 0.5rem;
}
.tec-graph {
  display: flex;
  gap: 6px;
  height: 140px;
  padding: 0.5rem;
  align-items: flex-end;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 8px;
}
.bar {
  width: 12px;
  background: #4f7bff;
  border-radius: 4px;
  transition: height 0.3s;
}
.az-info {
  font-size: 0.9rem;
  color: #ccc;
  margin-bottom: 0.5rem;
}
</style>
