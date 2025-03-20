<template>
  <div class="elevator-simulator">
    <Building 
      :floors="floors"
      :current-floor="currentFloor"
      :pending-requests="pendingRequests"
      @call-elevator="handleCallElevator"
    />
    
    <Elevator
      :floors="floors"
      :current-floor="currentFloor"
      :elevator-position="elevatorPosition"
      :pending-floors="pendingRequests.map(r => r.floor)"
      :selected-floors="selectedFloors"
      :direction="direction"
      :floors-to-move="Math.abs(targetFloor - currentFloor)"
      @select-floor="handleSelectFloor"
    />
    
    <Logs :logs="formattedLogs" />
  </div>
</template>

<script>
import Building from './components/Building.vue'
import Elevator from './components/Elevator.vue'
import Logs from './components/logs.vue'

export default {
  components: {
    Building,
    Elevator,
    Logs
  },
  data() {
    return {
      floors: [1, 2, 3, 4],
      elevatorPosition: 0,
      currentFloor: 1,
      queue: [],
      pendingRequests: [],
      selectedFloors: [],
      isMoving: false,
      direction: null,
      logs: [],
      targetFloor: 1,
      idleTimeout: null
    }
  },
  computed: {
    formattedLogs() {
      return this.logs.map(log => ({
        timestamp: log.timestamp,
        message: log.message,
        type: this.getLogType(log.message)
      }))
    }
  },
  methods: {
    getLogType(message) {
      if (message.includes('chamado')) return 'call'
      if (message.includes('se movendo') || message.includes('chegou')) return 'action'
      if (message.includes('Parada')) return 'error'
      return 'info'
    },

    addLog(message) {
      this.logs.unshift({
        timestamp: new Date(),
        message
      })
      if (this.logs.length > 15) this.logs.pop()
    },

    handleCallElevator(floor, direction) {
      if (!this.pendingRequests.some(r => r.floor === floor && r.direction === direction)) {
        this.pendingRequests.push({ floor, direction })
        this.queue.push({ floor, direction })
        this.addLog(`🔼 Chamada no andar ${floor} (${direction === 'up' ? 'subir' : 'descer'})`)
        this.processQueue()
      }
    },

    handleSelectFloor(floor) {
      if (!this.selectedFloors.includes(floor)) {
        this.selectedFloors.push(floor)
        this.queue.push({ floor, direction: 'selected' })
        this.addLog(`▶️ Selecionado andar ${floor} no painel`)
        this.processQueue()
      }
    },

    processQueue() {
      if (this.isMoving || this.queue.length === 0) {
        this.resetIdleTimer()
        return
      }

      const nextRequest = this.queue[0]
      this.targetFloor = nextRequest.floor

      this.direction = this.targetFloor > this.currentFloor ? 'up' : 'down'
      this.isMoving = true
      this.resetIdleTimer()
      this.moveElevator(this.targetFloor)
    },

    async moveElevator(targetFloor) {
      const floorHeight = 100
      const floorsToMove = Math.abs(targetFloor - this.currentFloor)

      this.addLog(`🚠 Movendo para andar ${targetFloor} (${floorsToMove} andares)`)

      await this.animateElevator(targetFloor * floorHeight, floorsToMove * 5000)
      
      this.currentFloor = targetFloor
      this.isMoving = false
      this.cleanupFloorState(targetFloor)
      this.addLog(`✅ Chegou no andar ${targetFloor}`)

      await new Promise(resolve => setTimeout(resolve, 2000))

      const intermediate = this.findIntermediateRequest()
      if (intermediate) {
        this.addLog(`⏸ Parada intermediária no andar ${intermediate}`)
        this.moveElevator(intermediate)
      } else {
        this.queue.shift()
        this.processQueue()
      }
    },

    animateElevator(targetPosition, duration) {
      return new Promise(resolve => {
        this.elevatorPosition = targetPosition
        setTimeout(resolve, duration)
      })
    },

    findIntermediateRequest() {
      return this.queue.slice(1).find(request => {
        if (this.direction === 'up') {
          return request.floor > this.currentFloor && request.floor < this.queue[0].floor
        }
        return request.floor < this.currentFloor && request.floor > this.queue[0].floor
      })?.floor
    },

    cleanupFloorState(floor) {
      this.pendingRequests = this.pendingRequests.filter(r => r.floor !== floor)
      this.selectedFloors = this.selectedFloors.filter(f => f !== floor)
    },

    resetIdleTimer() {
      if (this.idleTimeout) clearTimeout(this.idleTimeout)
      if (this.currentFloor !== 1 && this.queue.length === 0 && !this.isMoving) {
        this.idleTimeout = setTimeout(() => {
          if (this.queue.length === 0 && !this.isMoving) {
            this.queue.push({ floor: 1, direction: 'idle' })
            this.addLog('⏲️ Retornando ao térreo por inatividade')
            this.processQueue()
          }
        }, 20000)
      }
    }
  },
  mounted() {
    this.resetIdleTimer()
  }
}
</script>

<style>
.elevator-simulator {
  display: grid;
  grid-template-columns: 120px 300px 1fr;
  grid-template-rows: 90vh;
  gap: 1rem;
  margin: 0;
  padding: 2rem;
  background: #f8f9fa;
  height: 100vh;
  width: 100vw;
  box-sizing: border-box;
  align-items: start;
}

.elevator-shaft {
  height: 100%;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  padding: 1rem;
  position: relative;
}

.floor {
  margin: 8px ;
  padding: 6px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.control-panel {
  position: absolute;
  bottom: 1rem;
  left: 50%;
  transform: translateX(-50%);
  width: 80%;
}
</style>