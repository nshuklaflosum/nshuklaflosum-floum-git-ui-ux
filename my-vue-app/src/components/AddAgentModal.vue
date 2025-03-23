<script setup lang="ts">
import { reactive, defineEmits } from 'vue'

// Exporting the Agent interface
export interface Agent {
  name: string
  url: string
  type: string
  enabled: boolean
}

// Declare emitted events
const emits = defineEmits<{
  (e: 'close'): void
  (e: 'save', agent: Agent): void
}>()

// Use reactive instead of ref to avoid .value.name in templates
const agent = reactive<Agent>({
  name: '',
  url: '',
  type: '',
  enabled: true
})

function handleSubmit() {
  emits('save', agent)
  emits('close')
}

function handleClose() {
  emits('close')
}

function handleTestConnection() {
  console.log('Testing connection...')
}
</script>

<template>
  <div class="fixed inset-0 bg-gray-500 bg-opacity-75 flex items-center justify-center p-4 z-50">
    <div class="bg-white rounded-lg shadow-xl max-w-md w-full">
      <div class="flex justify-between items-center p-6 border-b">
        <h3 class="text-lg font-medium">Add Agent</h3>
        <button @click="handleClose" class="text-gray-400 hover:text-gray-500">
          <span class="font-bold">✕</span>
        </button>
      </div>

      <div class="p-6">
        <form @submit.prevent="handleSubmit" class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700">Agent Name</label>
            <input
              type="text"
              required
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3
                     focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
              placeholder="Production Agent"
              v-model="agent.name"
            />
          </div>

          <div>
            <label class="block text-sm font-medium text-gray-700">Agent URL</label>
            <input
              type="url"
              required
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3
                     focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
              placeholder="https://agent.example.com"
              v-model="agent.url"
            />
          </div>

          <div>
            <label class="block text-sm font-medium text-gray-700">Connection Type</label>
            <select
              required
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3
                     focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
              v-model="agent.type"
            >
              <option value="">- Select connection type -</option>
              <option value="heroku">Heroku</option>
              <option value="aws">AWS EC2</option>
              <option value="azure">Azure</option>
            </select>
          </div>

          <div class="flex items-center">
            <input
              type="checkbox"
              id="enabled"
              class="h-4 w-4 accent-black border-gray-300 rounded"
              v-model="agent.enabled"
            />
            <label for="enabled" class="ml-2 block text-sm text-gray-900">
              Enable Connection
            </label>
          </div>
        </form>
      </div>

      <div class="px-6 py-4 bg-gray-50 flex justify-end space-x-3 rounded-b-lg">
        <button
          @click="handleClose"
          class="px-4 py-2 border border-gray-300 rounded-md text-sm font-medium text-gray-700 hover:bg-gray-50"
        >
          Cancel
        </button>
        <button
          @click="handleTestConnection"
          class="px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-black hover:bg-gray-900"
        >
          Test Connection
        </button>
        <button
          @click="handleSubmit"
          class="px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-black hover:bg-gray-900"
        >
          Save
        </button>
      </div>
    </div>
  </div>
</template>