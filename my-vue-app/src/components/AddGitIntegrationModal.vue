<script setup lang="ts">
import { ref, defineEmits } from 'vue'
import { Search } from 'lucide-vue-next'

// If you have lucide-vue-next installed, you can import icons:
// import { X, ChevronRight, ChevronLeft, Search } from 'lucide-vue-next'

const emits = defineEmits<{
  (e: 'close'): void
}>()

function handleClose() {
  emits('close')
}

// State variables that replaced useState in React
const selectedService = ref<string>('')
const selectedRepositories = ref<string[]>([])
const availableRepositories = ['Main'] // same as in React

// Move a repo from available to selected
function handleMoveToSelected(repo: string) {
  selectedRepositories.value.push(repo)
}

// Remove a repo from selected
function handleRemoveFromSelected(repo: string) {
  selectedRepositories.value = selectedRepositories.value.filter(r => r !== repo)
}

// Toggles for "Enable Bidirectional Sync" and "Enable Convert to SFDX"
const isBidirectional = ref(false)
const isSFDX = ref(false)

// Buttons that had no real logic in your React code
function handleTestConnection() {
  console.log('Testing connection...')
}
function handleSave() {
  console.log('Saving new Git Integration...')
}
</script>

<template>
  <div class="fixed inset-0 bg-gray-500 bg-opacity-75 flex items-center justify-center p-4 z-50">
    <div class="bg-white rounded-lg shadow-xl max-w-4xl w-full">
      <!-- Header -->
      <div class="flex justify-between items-center p-6 border-b">
        <h3 class="text-lg font-medium">Set up Connection</h3>
        <button @click="handleClose" class="text-gray-400 hover:text-gray-500">
          <!-- Replace with <X class=\"h-5 w-5\" /> if lucide-vue-next is installed -->
          <span class="font-bold">✕</span>
        </button>
      </div>

      <!-- Body -->
      <div class="p-6">
        <form class="space-y-6" @submit.prevent>
          <!-- Connection Name -->
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Name <span class="text-red-500">*</span>
            </label>
            <input
              type="text"
              placeholder="Connection Name"
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3
                     focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
            />
          </div>

          <!-- Select an Agent -->
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Connection <span class="text-red-500">*</span>
            </label>
            <select
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3
                     focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
            >
              <option value="">- Select an agent -</option>
              <option>Production Heroku Agent</option>
              <option>AWS Development Agent</option>
            </select>
          </div>

          <!-- Service -->
          <div>
            <label class="block text-sm font-medium text-gray-700">
              Service <span class="text-red-500">*</span>
            </label>
            <select
              v-model="selectedService"
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3
                     focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
            >
              <option value="">Select a service</option>
              <option value="github">GitHub</option>
              <option value="github-onprem">GitHub OnPremise</option>
              <option value="gitlab">GitLab</option>
              <option value="gitlab-onprem">GitLab OnPremise</option>
              <option value="bitbucket">BitBucket</option>
              <option value="bitbucket-onprem">BitBucket OnPremise</option>
              <option value="azure">Azure</option>
              <option value="azure-onprem">Azure OnPremise</option>
            </select>
          </div>

          <!-- Show GitHub-specific fields if selectedService includes 'github' -->
          <template v-if="selectedService.includes('github')">
            <div>
              <label class="block text-sm font-medium text-gray-700">Organization</label>
              <input
                type="text"
                class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3
                       focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
              />
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700">
                Token <span class="text-red-500">*</span>
              </label>
              <input
                type="password"
                class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3
                       focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
              />
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700">
                Username <span class="text-red-500">*</span>
              </label>
              <input
                type="text"
                class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3
                       focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
              />
            </div>
          </template>

          <!-- Two toggles for Bidirectional & SFDX -->
          <div class="flex items-center space-x-8">
            <!-- Toggle #1: Enable Bidirectional Sync -->
            <label class="flex items-center cursor-pointer">
              <!-- We create a custom toggle with CSS transitions -->
              <div
                class="relative inline-block w-12 h-6 rounded-full"
                :class="isBidirectional ? 'bg-black' : 'bg-gray-200'"
              >
                <input type="checkbox" class="sr-only" v-model="isBidirectional" />
                <div
                  class="dot absolute left-1 top-1 bg-white w-4 h-4 rounded-full transition"
                  :class="isBidirectional ? 'translate-x-6' : ''"
                />
              </div>
              <span class="ml-3 text-sm font-medium text-gray-700"
                >Enable Bidirectional Sync</span
              >
            </label>

            <!-- Toggle #2: Enable Convert to SFDX -->
            <label class="flex items-center cursor-pointer">
              <div
                class="relative inline-block w-12 h-6 rounded-full"
                :class="isSFDX ? 'bg-black' : 'bg-gray-200'"
              >
                <input type="checkbox" class="sr-only" v-model="isSFDX" />
                <div
                  class="dot absolute left-1 top-1 bg-white w-4 h-4 rounded-full transition"
                  :class="isSFDX ? 'translate-x-6' : ''"
                />
              </div>
              <span class="ml-3 text-sm font-medium text-gray-700"
                >Enable Convert to SFDX</span
              >
            </label>
          </div>

          <!-- Repo search -->
          <div>
            <div class="relative">
              <!-- If using lucide-vue-next, replace with <Search class='...' />. -->
              <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                <Search class="h-5 w-5 text-gray-400" />
              </div>
              <input
                type="text"
                class="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-md leading-5 bg-white placeholder-gray-500
                       focus:outline-none focus:placeholder-gray-400 focus:ring-1 focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
                placeholder="Search repositories..."
              />
            </div>
          </div>

          <!-- Repositories picker -->
          <div class="grid grid-cols-2 gap-4">
            <!-- Left side: Available Repos -->
            <div>
              <h4 class="text-sm font-medium text-gray-700 mb-2">
                Available Repositories
              </h4>
              <div class="border border-gray-300 rounded-md h-48 overflow-y-auto p-2">
                <div
                  v-for="repo in availableRepositories.filter(r => !selectedRepositories.includes(r))"
                  :key="repo"
                  class="flex justify-between items-center p-2 hover:bg-gray-50 cursor-pointer rounded"
                  @click="handleMoveToSelected(repo)"
                >
                  <span class="text-sm text-gray-700">{{ repo }}</span>
                  <!-- If using lucide-vue-next, replace with <ChevronRight class='...' /> -->
                  <span class="h-4 w-4 text-gray-400 font-bold">→</span>
                </div>
              </div>
            </div>

            <!-- Right side: Selected Repos -->
            <div>
              <h4 class="text-sm font-medium text-gray-700 mb-2">
                Selected Repositories
              </h4>
              <div class="border border-gray-300 rounded-md h-48 overflow-y-auto p-2">
                <div
                  v-for="repo in selectedRepositories"
                  :key="repo"
                  class="flex justify-between items-center p-2 hover:bg-gray-50 cursor-pointer rounded"
                  @click="handleRemoveFromSelected(repo)"
                >
                  <!-- If using lucide-vue-next, replace with <ChevronLeft class='...' /> -->
                  <span class="h-4 w-4 text-gray-400 font-bold">←</span>
                  <span class="text-sm text-gray-700">{{ repo }}</span>
                </div>
              </div>
            </div>
          </div>
        </form>
      </div>

      <!-- Footer -->
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
          @click="handleSave"
          class="px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-black hover:bg-gray-900"
        >
          Save
        </button>
      </div>
    </div>
  </div>
</template>