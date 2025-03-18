<template>
  <div class="building">
    <h2 class="building-header">Andares</h2>
    <div 
      v-for="(floor, index) in reversedFloors"
      :key="floor"
      class="floor"
      :class="{ 'current-floor': currentFloor === floor }"
    >
      <div class="floor-info">
        <span class="floor-number">{{ floor }}</span>
        <div class="call-buttons">
          <button
            v-if="index !== 0"
            class="call-button up"
            :class="{ 'active-request': hasPendingRequest(floor, 'up') }"
            @click="callElevator(floor, 'up')"
          >
            ▲
          </button>
          <button
            v-if="index !== reversedFloors.length - 1"
            class="call-button down"
            :class="{ 'active-request': hasPendingRequest(floor, 'down') }"
            @click="callElevator(floor, 'down')"
          >
            ▼
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
    pendingRequests: Array
  },
  computed: {
    reversedFloors() {
      return [...this.floors].reverse();
    }
  },
  methods: {
    hasPendingRequest(floor, direction) {
      return this.pendingRequests.some(r => r.floor === floor && r.direction === direction);
    },
    callElevator(floor, direction) {
      this.$emit('call-elevator', floor, direction);
    }
  }
}
</script>

<style scoped>
.building {
  background: white;
  border-radius: 12px;
  padding: 1rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
  width: 100%;
  height: 50%;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  padding: 1rem;
  display: flex;
  flex-direction: column-reverse;
}

.building-header {
  text-align: center;
  font-size: 1.5rem;
  color: #2c3e50;
  font-weight: bold;
  margin-bottom: 1.5rem;
  padding: 0.5rem;
  border-bottom: 2px solid #f0f0f0;
}

.floor {
  padding: 1rem;
  border-bottom: 1px solid #eee;
  display: flex;
  flex-direction: column-reverse;
}

.floor-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.floor-number {
  font-size: 1.2rem;
  font-weight: 600;
  color: #333;
}

.call-buttons {
  display: flex;
  gap: 0.5rem;
}

.call-button {
  width: 32px;
  height: 32px;
  border: 2px solid #dee2e6;
  background: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s;
}

.call-button:hover {
  background: #f8f9fa;
}

.active-request {
  border-color: #dc3545;
  color: #dc3545;
  animation: pulse 1.5s infinite;
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); }
  100% { transform: scale(1); }
}

.building {
  display: flex;
  flex-direction: column;
}

.building > div:first-child {
  order: 1;
}

.building > .floor {
  order: 2;
}
</style>