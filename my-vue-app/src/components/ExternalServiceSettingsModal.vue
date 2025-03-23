<script setup lang="ts">
import { defineEmits } from 'vue'

// If you have lucide-vue-next installed, you can import { X, Copy } from 'lucide-vue-next'
// And replace placeholders with <X /> or <Copy />

const emits = defineEmits<{
  (e: 'close'): void
}>()

function handleClose() {
  emits('close')
}

function handleCopyToken() {
  // The React code had no direct copy functionality.
  // You could implement a real copy to clipboard here.
  console.log('Copy token to clipboard')
}

function handleGenerateNewToken() {
  // Again, no real logic in React. Insert your own logic as needed.
  console.log('Generate new token')
}
</script>

<template>
  <div class="fixed inset-0 bg-gray-500 bg-opacity-75 flex items-center justify-center p-4 z-50">
    <div class="bg-white rounded-lg shadow-xl max-w-md w-full">
      <!-- Header -->
      <div class="flex justify-between items-center p-6 border-b">
        <h3 class="text-lg font-medium">External Service Settings</h3>
        <button @click="handleClose" class="text-gray-400 hover:text-gray-500">
          <!-- If lucide-vue-next is installed: <X class="h-5 w-5" /> -->
          <span class="font-bold">✕</span>
        </button>
      </div>

      <!-- Body -->
      <div class="p-6">
        <div class="space-y-4">
          <p class="text-sm text-gray-500">
            Generate an authorization token to connect external services with your
            account. This token will be used for authentication in your external services.
          </p>
          
          <!-- Authorization Token field -->
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Authorization Token</label>
            <div class="flex">
              <input
                type="text"
                readonly
                value="••••••••••••••••"
                class="flex-1 border border-gray-300 rounded-l-md shadow-sm py-2 px-3 bg-gray-50 text-gray-500 sm:text-sm"
              />
              <button
                @click="handleCopyToken"
                class="inline-flex items-center px-4 py-2 border border-l-0 border-gray-300 rounded-r-md text-sm font-medium text-gray-700 bg-gray-50 hover:bg-gray-100"
              >
                <!-- If lucide-vue-next is installed: <Copy class="h-4 w-4" /> -->
                <span class="font-bold">Copy</span>
              </button>
            </div>
          </div>

          <button
            @click="handleGenerateNewToken"
            class="w-full px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-black hover:bg-gray-900"
          >
            Generate New Token
          </button>
        </div>
      </div>

      <!-- Footer -->
      <div class="px-6 py-4 bg-gray-50 rounded-b-lg">
        <p class="text-xs text-gray-500">
          Note: Keep this token secure. If compromised, generate a new one immediately.
          The token will be required as an environment variable (SALESFORCE_TOKEN)
          in your external services.
        </p>
      </div>
    </div>
  </div>
</template>