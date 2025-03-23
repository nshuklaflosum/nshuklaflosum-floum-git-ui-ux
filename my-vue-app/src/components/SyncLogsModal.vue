<script setup lang="ts">
import { defineEmits, ref, computed } from 'vue'

const emits = defineEmits<{
  (e: 'close'): void
}>()

function handleClose() {
  emits('close')
}

function handleRefresh() {
  console.log('Refresh logs...')
}

function handleExport() {
  const blob = new Blob([logs.value.join('\n')], { type: 'text/plain;charset=utf-8' })
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.href = url
  link.download = 'system.log'
  link.click()
  URL.revokeObjectURL(url)
}

const logs = ref<string[]>([
  '2025-03-06T05:45:11.145Z flosum-agent [INFO] [Child] -> [sync.child]: closed',
  '2025-03-06T05:47:10.307Z flosum-agent [INFO] [request-64560456-0a73-4ff4-9970-f8a21c7b39da] -> Instantiated new context at /api/info',
  '2025-03-06T05:47:10.383Z flosum-agent [INFO] [request-231d0069-12d8-4f35-ab54-e92e90555d5c] -> Instantiated new context at /favicon.ico',
  '2025-03-06T05:47:10.385Z flosum-agent [LOG] [DefaultErrorMiddleware] -> Endpoint /favicon.ico not found',
  '2025-03-06T05:47:16.740Z flosum-agent [INFO] [request-2407d9ad-0103-475c-8146-5ba1bc785e00] -> Instantiated new context at /api/v1/git/devops/sync/disable',
  '2025-03-06T05:47:17.452Z flosum-agent [LOG] [SalesforceAuthService] -> Check auth: current state - Idle',
  '2025-03-06T05:47:17.453Z flosum-agent [LOG] [SalesforceAuthService] -> Update access token',
  '2025-03-06T05:47:17.521Z flosum-agent [LOG] [SalesforceAuthService] -> Create authorized request failed: authorization is not done',
  '2025-03-06T05:47:17.782Z flosum-agent [ERROR] [SalesforceAuthService] -> Update access token failed: invalid_grant'
])

const searchTerm = ref('')
const filteredLogs = computed(() => {
  if (!searchTerm.value) return logs.value
  return logs.value.filter(log =>
    log.toLowerCase().includes(searchTerm.value.toLowerCase())
  )
})
</script>
<template>
  <div class="fixed inset-0 bg-gray-500 bg-opacity-75 flex items-center justify-center p-4 z-50">
    <div class="bg-white rounded-lg shadow-xl max-w-6xl w-full">
      <!-- Header -->
      <div class="flex justify-between items-center p-6 border-b">
        <h3 class="text-xl font-medium">System Logs</h3>
        <button @click="handleClose" class="text-gray-400 hover:text-gray-500">
          <span class="font-bold">✕</span>
        </button>
      </div>

      <!-- Body -->
      <div class="p-6">
        <!-- Search Bar -->
        <div class="mb-6">
          <div class="relative">
            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
              <span class="h-5 w-5 text-gray-400 font-bold">🔎</span>
            </div>
            <input
              v-model="searchTerm"
              type="text"
              placeholder="Search logs..."
              class="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-md leading-5
                     bg-white placeholder-gray-500 focus:outline-none focus:placeholder-gray-400
                     focus:ring-1 focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
            />
          </div>
        </div>

        <!-- Logs Display -->
        <div class="bg-black rounded-lg overflow-hidden">
          <div class="p-4 bg-gray-900 border-b border-gray-800">
            <span class="text-gray-400 text-sm font-mono">system.log</span>
          </div>
          <div class="p-4 h-96 overflow-y-auto font-mono text-sm">
            <div
              v-for="(log, index) in filteredLogs"
              :key="index"
              class="py-1 text-gray-300 whitespace-pre-wrap"
            >
              {{ log }}
            </div>
          </div>
        </div>
      </div>

      <!-- Footer -->
      <div class="px-6 py-4 bg-gray-50 flex justify-end space-x-3 rounded-b-lg">
        <button
          @click="handleRefresh"
          class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-black hover:bg-gray-900"
        >
          <span class="mr-2">↻</span>
          Refresh
        </button>
        <button
          @click="handleExport"
          class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-black hover:bg-gray-900"
        >
          Export
        </button>
        <button
          @click="handleClose"
          class="px-4 py-2 border border-gray-300 rounded-md text-sm font-medium text-gray-700 hover:bg-gray-50"
        >
          Close
        </button>
      </div>
    </div>
  </div>
</template>