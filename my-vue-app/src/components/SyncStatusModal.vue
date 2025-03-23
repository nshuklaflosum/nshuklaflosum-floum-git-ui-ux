<script setup lang="ts">
import { ref } from 'vue'
import {
  GitBranch,
  CheckCircle2,
  XCircle,
  ChevronDown,
  ChevronRight,
  ArrowRight,
  ArrowLeft,
  RotateCw,
  Search,
  X
} from 'lucide-vue-next'

interface Branch {
  name: string
  status: string
  timestamp: string
  syncDirection: 'flosum-to-git' | 'git-to-flosum'
}

interface Repository {
  name: string
  provider: string
  agent: string
  status: string
  timestamp: string
  branches: Branch[]
}

defineEmits(['close'])

const repositories = ref<Repository[]>([
  {
    name: 'Main Repository',
    provider: 'GitHub',
    agent: 'Production Heroku Agent',
    status: 'Completed',
    timestamp: '2024-03-15 14:30:00',
    branches: [
      { name: 'main', status: 'Completed', timestamp: '2024-03-15 14:30:00', syncDirection: 'flosum-to-git' },
      { name: 'develop', status: 'Completed', timestamp: '2024-03-15 14:29:00', syncDirection: 'flosum-to-git' },
      { name: 'feature/auth', status: 'Not Synchronized', timestamp: '2024-03-15 14:28:00', syncDirection: 'flosum-to-git' },
      { name: 'feature/api', status: 'Completed', timestamp: '2024-03-15 14:15:00', syncDirection: 'flosum-to-git' },
      { name: 'release/v1.0', status: 'Completed', timestamp: '2024-03-15 14:10:00', syncDirection: 'flosum-to-git' }
    ]
  },
  {
    name: 'Legacy System',
    provider: 'Bitbucket',
    agent: 'AWS Development Agent',
    status: 'Not Synchronized',
    timestamp: '2024-03-15 10:15:00',
    branches: [
      { name: 'master', status: 'Not Synchronized', timestamp: '2024-03-15 10:15:00', syncDirection: 'flosum-to-git' },
      { name: 'staging', status: 'Completed', timestamp: '2024-03-15 10:10:00', syncDirection: 'flosum-to-git' },
      { name: 'feature/migration', status: 'Completed', timestamp: '2024-03-15 10:05:00', syncDirection: 'flosum-to-git' }
    ]
  }
])

const expandedRepos = ref<string[]>([])
const searchTerm = ref('')

function toggleRepository(name: string) {
  expandedRepos.value.includes(name)
    ? expandedRepos.value = expandedRepos.value.filter(n => n !== name)
    : expandedRepos.value.push(name)
}

function toggleSyncDirection(repoName: string, branchName: string) {
  repositories.value = repositories.value.map(repo => {
    if (repo.name === repoName) {
      return {
        ...repo,
        branches: repo.branches.map(branch =>
          branch.name === branchName
            ? {
                ...branch,
                syncDirection: branch.syncDirection === 'flosum-to-git' ? 'git-to-flosum' : 'flosum-to-git'
              }
            : branch
        )
      }
    }
    return repo
  })
}

function handleRefresh() {
  console.log('Refresh triggered...')
}

function handleStartSync() {
  console.log('Start Sync triggered...')
}
</script>
<template>
  <div class="fixed inset-0 bg-gray-500 bg-opacity-75 flex items-center justify-center p-4 z-50">
    <div class="bg-white rounded-lg shadow-xl max-w-6xl w-full">
      <!-- Header -->
      <div class="flex justify-between items-center p-6 border-b">
        <h3 class="text-xl font-semibold">Synchronization Status</h3>
        <button class="text-gray-400 hover:text-gray-500" @click="$emit('close')">
          <X class="h-5 w-5" />
        </button>
      </div>

      <!-- Search + Sync -->
      <div class="p-6">
        <div class="mb-6 flex justify-between items-center">
          <div class="relative flex-1 max-w-lg">
            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
              <Search class="h-5 w-5 text-gray-400" />
            </div>
            <input
              v-model="searchTerm"
              type="text"
              placeholder="Search repository or branch..."
              class="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-md leading-5
                     bg-white placeholder-gray-500 focus:outline-none focus:placeholder-gray-400
                     focus:ring-1 focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
            />
          </div>
          <button
            @click="handleStartSync"
            class="ml-4 px-4 py-2 bg-black text-white rounded-md hover:bg-gray-900 transition-colors flex items-center gap-2"
          >
            <RotateCw class="h-4 w-4" />
            Start Sync
          </button>
        </div>

        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider w-8"></th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Repository/Branch</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Provider</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Agent</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Direction</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Status</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Last Sync</th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <template v-for="repo in repositories" :key="repo.name">
                <tr class="bg-gray-50">
                  <td class="px-6 py-4 whitespace-nowrap">
                    <button @click="toggleRepository(repo.name)" class="text-gray-500 hover:text-gray-700">
                      <ChevronDown v-if="expandedRepos.includes(repo.name)" class="h-4 w-4" />
                      <ChevronRight v-else class="h-4 w-4" />
                    </button>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <div class="flex items-center">
                      <GitBranch class="h-5 w-5 text-gray-400 mr-3" />
                      <div class="text-sm font-medium text-gray-900">{{ repo.name }}</div>
                    </div>
                  </td>
                  <td class="px-6 py-4 text-sm text-gray-500">{{ repo.provider }}</td>
                  <td class="px-6 py-4 text-sm text-gray-500">{{ repo.agent }}</td>
                  <td class="px-6 py-4 text-sm text-gray-500">-</td>
                  <td class="px-6 py-4">
                    <span
                      class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium"
                      :class="repo.status === 'Completed' ? 'bg-green-100 text-green-800' : 'bg-yellow-100 text-yellow-800'"
                    >
                      <CheckCircle2 v-if="repo.status === 'Completed'" class="h-4 w-4 mr-1" />
                      <XCircle v-else class="h-4 w-4 mr-1" />
                      {{ repo.status }}
                    </span>
                  </td>
                  <td class="px-6 py-4 text-sm text-gray-500">{{ repo.timestamp }}</td>
                </tr>

                <!-- Branch Rows -->
                <tr
                  v-for="branch in repo.branches"
                  :key="`${repo.name}-${branch.name}`"
                  v-if="expandedRepos.includes(repo.name)"
                  class="hover:bg-gray-50"
                >
                  <td class="px-6 py-4 whitespace-nowrap"></td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <div class="flex items-center pl-8">
                      <div class="text-sm text-gray-500">{{ branch.name }}</div>
                    </div>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap"></td>
                  <td class="px-6 py-4 whitespace-nowrap"></td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <button
                      @click="toggleSyncDirection(repo.name, branch.name)"
                      class="inline-flex items-center text-sm text-gray-700 hover:text-gray-900"
                    >
                      <span class="mr-2">Flosum</span>
                      <ArrowRight v-if="branch.syncDirection === 'flosum-to-git'" class="h-4 w-4" />
                      <ArrowLeft v-else class="h-4 w-4" />
                      <span class="ml-2">Git</span>
                    </button>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <span
                      class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium"
                      :class="branch.status === 'Completed' ? 'bg-green-100 text-green-800' : 'bg-yellow-100 text-yellow-800'"
                    >
                      <CheckCircle2 v-if="branch.status === 'Completed'" class="h-4 w-4 mr-1" />
                      <XCircle v-else class="h-4 w-4 mr-1" />
                      {{ branch.status }}
                    </span>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ branch.timestamp }}</td>
                </tr>
              </template>
            </tbody>
          </table>
        </div>

        <!-- Footer -->
        <div class="px-6 py-4 bg-gray-50 flex justify-end space-x-3 rounded-b-lg">
          <button
            @click="handleStartSync"
            class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-black hover:bg-gray-900"
          >
            <RotateCw class="h-4 w-4 mr-2" />
            Refresh
          </button>
          <button
            @click="$emit('close')"
            class="px-4 py-2 border border-gray-300 rounded-md text-sm font-medium text-gray-700 hover:bg-gray-50"
          >
            Close
          </button>
        </div>
      </div>
    </div>
  </div>
</template>