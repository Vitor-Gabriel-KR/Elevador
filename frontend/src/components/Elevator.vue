<template>
  <div class="elevator-shaft">
    <p class="elevator-text">Elevador</p>
    <div 
      class="elevator" 
      :style="{ 
        bottom: elevatorPosition + 'px',
        'transition-duration': transitionDuration + 's'
      }"
    >
      <div class="elevator-header">
        <div class="floor-info">
          <span class="floor-label">Andar Atual</span>
          <span class="current-floor">{{ currentFloor }}</span>
        </div>
        <div class="direction-indicator">
          {{ directionSymbol }}
        </div>
      </div>

      <div class="control-panel-wrapper">
        <div class="control-panel">
          <button
            v-for="floor in reversedFloors"
            :key="floor"
            class="floor-button"
            :class="{ 
              'active-floor': pendingFloors.includes(floor),
              'selected-floor': selectedFloors.includes(floor)
            }"
            @click="selectFloor(floor)"
          >
            {{ floor }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    floors: Array,
    currentFloor: Number,
    elevatorPosition: Number,
    pendingFloors: Array,
    selectedFloors: Array,
    direction: String,
    floorsToMove: Number
  },
  computed: {
    reversedFloors() {
      return [...this.floors].reverse();
    },
    directionSymbol() {
      return this.direction === 'up' ? '▲' : this.direction === 'down' ? '▼' : '■';
    },
    transitionDuration() {
      return this.floorsToMove * 5;
    }
  },
  methods: {
    selectFloor(floor) {
      this.$emit('select-floor', floor);
    }
  }
}
</script>

<style scoped>
.elevator-shaft {
  position: relative;
  width: 200px;
  height: 72%;
  background: #f8f9fa;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
  overflow: hidden;
  margin-left: 20px;
  left: 30px;
}

.elevator {
  position: absolute;
  width: 180px;
  height: 240px;
  background: white;
  border-radius: 10px;
  left: 10px;
  transition: bottom cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  flex-direction: column;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.elevator-text{
  text-align: center;
  font-size: 1.4rem;
  color: #000000;
  margin-bottom: 1rem;
}

.elevator-header {
  padding: 1rem;
  background: linear-gradient(145deg, #f8f9fa, #e9ecef);
  border-radius: 8px 8px 0 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  min-height: 80px;
}

.floor-info {
  display: flex;
  flex-direction: column;
}

.floor-label {
  font-size: 0.75rem;
  color: #6c757d;
  margin-bottom: 0.25rem;
}

.current-floor {
  font-size: 1.8rem;
  font-weight: 700;
  color: #212529;
}

.direction-indicator {
  font-size: 1.8rem;
  color: #0d6efd;
  padding: 0.6rem;
  background: rgba(13, 110, 253, 0.1);
  border-radius: 8px;
  margin-left: 1rem;
}

.control-panel-wrapper {
  flex: 1;
  background: #f8f9fa;
  border-radius: 0 0 8px 8px;
  padding: 1rem;
  display: flex;
  align-items: center;
}

.control-panel {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 0.5rem;
  width: 100%;
}

.floor-button {
  padding: 0.7rem;
  border: 2px solid #dee2e6;
  background: white;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  font-size: 0.9rem;
  transition: all 0.2s;
  display: flex;
  justify-content: center;
  align-items: center;
}

.floor-button:hover {
  background: #f1f3f5;
  transform: translateY(-1px);
}

.active-floor {
  border-color: #dc3545;
  color: #dc3545;
  animation: pulse 1.5s infinite;
}

.selected-floor {
  border-color: #0d6efd;
  color: #0d6efd;
  background: rgba(13, 110, 253, 0.05);
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); }
  100% { transform: scale(1); }
}
</style>