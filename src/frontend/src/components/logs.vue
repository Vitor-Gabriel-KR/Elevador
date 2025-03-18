<template>
    <div class="logs-container">
      <h3 class="logs-title">Log de eventos</h3>
      <div class="logs-content">
        <div
          v-for="(log, index) in logs"
          :key="index"
          class="log-entry"
          :class="logType(log)"
        >
          <span class="timestamp">{{ formatTime(log.timestamp) }}</span>
          <span class="message">{{ log.message }}</span>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      logs: Array
    },
    methods: {
      logType(log) {
        return {
          'call': log.message.includes('chamado'),
          'action': log.message.includes('se movendo') || log.message.includes('chegou'),
          'error': log.message.includes('Parada')
        };
      },
      formatTime(timestamp) {
        return new Date(timestamp).toLocaleTimeString('en-GB', {
          hour: '2-digit',
          minute: '2-digit',
          second: '2-digit'
        });
      }
    }
  }
  </script>
  
  <style scoped>
  .logs {
  height: 80%;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  padding: 1rem;
  overflow-y: auto;
 }
  .logs-container {
    background: white;
    border-radius: 12px;
    padding: 1rem;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
  }
  
  .logs-title {
    font-size: 1.1rem;
    color: #495057;
    margin-bottom: 1rem;
  }
  
  .logs-content {
    max-height: 50%;
    overflow-y: auto;
  }
  
  .log-entry {
    display: flex;
    gap: 1rem;
    padding: 0.5rem;
    font-size: 0.9rem;
    border-bottom: 1px solid #eee;
    font-family: 'Fira Code', monospace;
   white-space: nowrap;
  }
  
  .timestamp {
    color: #6c757d;
    min-width: 70px;
  }
  
  .message {
    color: #212529;
    white-space: nowrap;
  }
  
  .log-entry.call .message { color: #0d6efd; }
  .log-entry.action .message { color: #198754; }
  .log-entry.error .message { color: #dc3545; }
  </style>