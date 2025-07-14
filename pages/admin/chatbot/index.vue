<template>
  <div>
    <div class="mb-8">
      <h1 class="text-3xl font-bold text-foreground mb-2">Chatbot Management</h1>
      <p class="text-muted-foreground">Manage chat sessions, messages, and AI interactions</p>
    </div>

    <!-- Filters and Search -->
    <div class="card p-6 mb-6">
      <div class="flex flex-col sm:flex-row gap-4">
        <div class="flex-1">
          <label class="block text-sm font-medium text-foreground mb-2">Search Sessions</label>
          <div class="relative">
            <input
              v-model="searchQuery"
              type="text"
              placeholder="Search by user ID or session ID..."
              class="w-full pl-10 pr-4 py-2 border border-border rounded-lg bg-input text-foreground placeholder:text-muted-foreground focus:ring-2 focus:ring-primary focus:border-primary"
            />
            <svg class="absolute left-3 top-2.5 h-5 w-5 text-muted-foreground" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
            </svg>
          </div>
        </div>
        
        <div class="sm:w-48">
          <label class="block text-sm font-medium text-foreground mb-2">Filter by Model</label>
          <select 
            v-model="selectedModel"
            class="w-full px-3 py-2 border border-border rounded-lg bg-input text-foreground focus:ring-2 focus:ring-primary focus:border-primary"
          >
            <option value="">All Models</option>
            <option value="GPT-4">GPT-4</option>
            <option value="GPT-3.5">GPT-3.5</option>
            <option value="Claude">Claude</option>
          </select>
        </div>
        
        <div class="sm:w-48">
          <label class="block text-sm font-medium text-foreground mb-2">Processing Status</label>
          <select 
            v-model="selectedStatus"
            class="w-full px-3 py-2 border border-border rounded-lg bg-input text-foreground focus:ring-2 focus:ring-primary focus:border-primary"
          >
            <option value="">All Status</option>
            <option value="processed">Processed</option>
            <option value="pending">Pending</option>
            <option value="failed">Failed</option>
          </select>
        </div>
      </div>
    </div>

    <!-- Sessions List -->
    <div class="card">
      <div class="px-6 py-4 border-b border-border">
        <h3 class="text-lg font-semibold text-foreground">Chat Sessions</h3>
      </div>
      
      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-muted">
            <tr>
              <th class="px-6 py-3 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Session ID</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">User ID</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Messages</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Created</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Status</th>
              <th class="px-6 py-3 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Actions</th>
            </tr>
          </thead>
          <tbody class="bg-card divide-y divide-border">
            <tr 
              v-for="session in filteredSessions" 
              :key="session.id"
              class="hover:bg-accent transition-colors"
            >
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm font-medium text-foreground">{{ session.id.substring(0, 8) }}...</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm text-foreground">{{ session.userId.substring(0, 8) }}...</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm text-foreground">{{ session.messageCount }} messages</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <div class="text-sm text-foreground">{{ formatDate(session.createdAt) }}</div>
              </td>
              <td class="px-6 py-4 whitespace-nowrap">
                <span 
                  class="inline-flex px-2 py-1 text-xs font-semibold rounded-full"
                  :class="getStatusColor(session.status)"
                >
                  {{ session.status }}
                </span>
              </td>
              <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                <NuxtLink
                  :to="`/admin/chatbot/${session.id}`"
                  class="text-primary hover:text-primary-foreground mr-4"
                >
                  View
                </NuxtLink>
                <button
                  @click="deleteSession(session.id)"
                  class="text-destructive hover:text-destructive-foreground"
                >
                  Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup>
import { mockSessions } from '~/data/mockSessions.js';

definePageMeta({
  layout: 'admin'
})

const searchQuery = ref('')
const selectedModel = ref('')
const selectedStatus = ref('')

// Use mock data directly
const sessions = ref(mockSessions);

const filteredSessions = computed(() => {
  return sessions.value.filter(session => {
    const matchesSearch = !searchQuery.value || 
      session.id.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      session.userId.toLowerCase().includes(searchQuery.value.toLowerCase())
    
    const matchesModel = !selectedModel.value || 
      session.messages.some(msg => msg.aiModel === selectedModel.value)
    
    const matchesStatus = !selectedStatus.value || 
      (selectedStatus.value === 'processed' && session.status === 'completed') ||
      (selectedStatus.value === 'pending' && session.status === 'active') ||
      (selectedStatus.value === 'failed' && session.status === 'failed')
    
    return matchesSearch && matchesModel && matchesStatus
  })
})

const formatDate = (date) => {
  return new Intl.DateTimeFormat('en-US', {
    month: 'short',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  }).format(date)
}

const getStatusColor = (status) => {
  const colors = {
    'active': 'bg-blue-100 text-blue-800',
    'completed': 'bg-green-100 text-green-800',
    'failed': 'bg-red-100 text-red-800'
  }
  return colors[status] || 'bg-gray-100 text-gray-800'
}

const deleteSession = (sessionId) => {
  if (confirm('Are you sure you want to delete this session?')) {
    sessions.value = sessions.value.filter(s => s.id !== sessionId)
  }
}
</script>
