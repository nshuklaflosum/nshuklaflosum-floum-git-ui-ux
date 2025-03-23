<script setup lang="ts">
import { ref } from 'vue'

// Import Lucide icons from lucide-vue-next
import {
  Search,
  Plus,
  Settings2,
  History,
  Edit2,
  Trash2,
  CheckCircle2,
  XCircle,
  ScrollText,
  Repeat,
  FileCode2
} from 'lucide-vue-next'

// Import all your child components (.vue files)
import AddAgentModal, { type Agent } from './components/AddAgentModal.vue'
import AddGitIntegrationModal from './components/AddGitIntegrationModal.vue'
import ExternalServiceSettingsModal from './components/ExternalServiceSettingsModal.vue'
import SyncStatusModal from './components/SyncStatusModal.vue'
import SyncLogsModal from './components/SyncLogsModal.vue'
import ToggleSwitch from './components/ToggleSwitch.vue'

// Reactive state replacing useState
const showAddAgent = ref(false)
const showAddGit = ref(false)
const showSettings = ref(false)
const showSyncStatus = ref(false)
const showSyncLogs = ref(false)

const agents = ref<Agent[]>([
  {
    name: 'Production Heroku Agent',
    url: 'https://prod-agent.herokuapp.com',
    type: 'heroku',
    enabled: true
  },
  {
    name: 'AWS Development Agent',
    url: 'https://dev-agent.aws.example.com',
    type: 'aws',
    enabled: true
  }
])

// Connections array is fine as a plain array if no reactivity is needed
const connections = [
  {
    name: 'Main Repository',
    provider: 'GitHub',
    agent: 'Production Heroku Agent',
    status: 'synced',
    lastSync: '2024-03-15 14:30',
    isEnabled: true,
    isBidirectional: true,
    isSFDX: true
  },
  {
    name: 'Legacy System',
    provider: 'Bitbucket',
    agent: 'AWS Development Agent',
    status: 'failed',
    lastSync: '2024-03-15 10:15',
    isEnabled: false,
    isBidirectional: false,
    isSFDX: false
  }
]

// Functions that manipulate agents
function handleSaveAgent(newAgent: Agent) {
  agents.value.push(newAgent)
}

function handleDeleteAgent(agentName: string) {
  agents.value = agents.value.filter((agent) => agent.name !== agentName)
}

function handleToggleAgent(agentName: string) {
  agents.value = agents.value.map((agent) =>
    agent.name === agentName ? { ...agent, enabled: !agent.enabled } : agent
  )
}
</script>

<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header / Page Title -->
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      <div class="flex justify-between items-center mb-8">
        <div>
          <h1 class="text-2xl font-bold text-gray-900">Git Connector</h1>
          <p class="mt-1 text-sm text-gray-500">
            Connect and manage Git repositories from various providers.
          </p>
        </div>
        <div class="flex gap-3">
          <button
            @click="showAddAgent = true"
            class="inline-flex items-center px-4 py-2 border border-gray-300 rounded-md
                   shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50"
          >
            <Plus class="h-4 w-4 mr-2" />
            Add Agent
          </button>
          <button
            @click="showSettings = true"
            class="inline-flex items-center px-4 py-2 border border-gray-300 rounded-md
                   shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50"
          >
            <Settings2 class="h-4 w-4 mr-2" />
            External Service Settings
          </button>
          <button
            @click="showAddGit = true"
            class="inline-flex items-center px-4 py-2 border border-transparent rounded-md
                   shadow-sm text-sm font-medium text-white bg-black hover:bg-gray-900"
          >
            <Plus class="h-4 w-4 mr-2" />
            Add Git Connection
          </button>
        </div>
      </div>

      <!-- Agents Table -->
      <div class="bg-white shadow rounded-lg overflow-hidden mb-8">
        <div class="px-6 py-4 border-b border-gray-200">
          <h2 class="text-lg font-medium text-gray-900">Configured Agents</h2>
        </div>
        <table class="min-w-full divide-y divide-gray-200">
          <thead class="bg-gray-50">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                Agent Name
              </th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                URL
              </th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                Type
              </th>
              <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                Status
              </th>
              <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">
                Actions
              </th>
            </tr>
          </thead>
          <tbody class="bg-white divide-y divide-gray-200">
            <tr v-for="agent in agents" :key="agent.name">
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm font-medium text-gray-900">
                  {{ agent.name }}
                </div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm text-gray-500">
                  {{ agent.url }}
                </div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm text-gray-500">
                  {{ agent.type }}
                </div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <span
                  :class="[
                    'inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium',
                    agent.enabled ? 'bg-green-100 text-green-800' : 'bg-gray-100 text-gray-800'
                  ]"
                >
                  {{ agent.enabled ? 'Active' : 'Inactive' }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium space-x-3">
                <button
                  @click="handleToggleAgent(agent.name)"
                  class="text-gray-600 hover:text-gray-900"
                >
                  <ToggleSwitch :enabled="agent.enabled" />
                </button>
                <button
                  @click="handleDeleteAgent(agent.name)"
                  class="text-red-600 hover:text-red-900"
                >
                  <Trash2 class="h-4 w-4" />
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Connections Table -->
      <div class="flex justify-between items-center mb-6">
        <div class="relative flex-1 max-w-lg">
          <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
            <Search class="h-5 w-5 text-gray-400" />
          </div>
          <input
            type="text"
            class="block w-full pl-10 pr-3 py-2 border border-gray-300
                   rounded-md leading-5 bg-white placeholder-gray-500
                   focus:outline-none focus:placeholder-gray-400
                   focus:ring-1 focus:ring-blue-500 focus:border-blue-500
                   sm:text-sm"
            placeholder="Search connections..."
          />
        </div>
        <div class="flex gap-3">
          <button
            @click="showSyncLogs = true"
            class="inline-flex items-center px-4 py-2 border border-gray-300 rounded-md
                   shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50"
          >
            <ScrollText class="h-4 w-4 mr-2" />
            View Logs
          </button>
          <button
            @click="showSyncStatus = true"
            class="inline-flex items-center px-4 py-2 border border-gray-300 rounded-md
                   shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50"
          >
            <History class="h-4 w-4 mr-2" />
            View Sync Status
          </button>
        </div>
      </div>

      <div class="bg-white shadow rounded-lg overflow-hidden">
        <table class="min-w-full divide-y divide-gray-200">
          <thead class="bg-gray-50">
            <tr>
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
              >
                Connection Name
              </th>
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
              >
                Provider
              </th>
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
              >
                Agent
              </th>
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
              >
                Status
              </th>
              <th
                class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
              >
                Last Sync
              </th>
              <th
                class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider"
              >
                Actions
              </th>
            </tr>
          </thead>
          <tbody class="bg-white divide-y divide-gray-200">
            <tr v-for="connection in connections" :key="connection.name">
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm font-medium text-gray-900">
                  {{ connection.name }}
                </div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                {{ connection.provider }}
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                {{ connection.agent }}
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <span
                  :class="[
                    'inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium',
                    connection.status === 'synced'
                      ? 'bg-green-100 text-green-800'
                      : 'bg-red-100 text-red-800'
                  ]"
                >
                  <!-- Ternary check for the correct icon -->
                  <component
                    :is="connection.status === 'synced' ? CheckCircle2 : XCircle"
                    class="h-4 w-4 mr-1"
                  />
                  {{ connection.status.charAt(0).toUpperCase() + connection.status.slice(1) }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                {{ connection.lastSync }}
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium space-x-3">
                <button
                  class="inline-flex items-center"
                  title="Enable Connection"
                >
                  <ToggleSwitch :enabled="connection.isEnabled" />
                </button>
                <button
                  :class="[
                    'inline-flex items-center p-1.5 rounded-full',
                    connection.isBidirectional
                      ? 'bg-green-600 text-white'
                      : 'bg-gray-200 text-gray-400'
                  ]"
                  title="Bidirectional Sync"
                >
                  <Repeat class="h-4 w-4" />
                </button>
                <button
                  :class="[
                    'inline-flex items-center p-1.5 rounded-full',
                    connection.isSFDX
                      ? 'bg-green-600 text-white'
                      : 'bg-gray-200 text-gray-400'
                  ]"
                  title="Convert to SFDX"
                >
                  <FileCode2 class="h-4 w-4" />
                </button>
                <button
                  class="text-blue-600 hover:text-blue-900"
                  title="Edit Connection"
                >
                  <Edit2 class="h-4 w-4" />
                </button>
                <button
                  class="text-red-600 hover:text-red-900"
                  title="Delete Connection"
                >
                  <Trash2 class="h-4 w-4" />
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Modals Section -->
    <AddAgentModal
      v-if="showAddAgent"
      @close="showAddAgent = false"
      @save="handleSaveAgent"
    />
    <AddGitIntegrationModal
      v-if="showAddGit"
      @close="showAddGit = false"
    />
    <ExternalServiceSettingsModal
      v-if="showSettings"
      @close="showSettings = false"
    />
    <SyncStatusModal
      v-if="showSyncStatus"
      @close="showSyncStatus = false"
    />
    <SyncLogsModal
      v-if="showSyncLogs"
      @close="showSyncLogs = false"
    />
  </div>
</template>