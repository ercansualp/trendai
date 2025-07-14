<template>
  <div class="h-full flex flex-col">
    <div class="mb-6 flex items-center justify-between">
      <div>
        <h1 class="text-3xl font-bold text-foreground mb-2">Chat Session Details</h1>
        <p class="text-muted-foreground">Review the full conversation for this session</p>
      </div>
      <NuxtLink
        to="/admin/chatbot"
        class="inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-primary-foreground bg-primary hover:bg-primary/90 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-primary"
      >
        <svg class="w-4 h-4 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18"></path>
        </svg>
        Back to Sessions
      </NuxtLink>
    </div>

    <div v-if="session" class="flex-1 card flex flex-col" style="height: calc(100vh - 200px);">
      <!-- Session Header -->
      <div class="px-6 py-4 border-b border-border bg-muted rounded-t-lg">
        <div class="flex items-center justify-between">
          <div class="flex items-center space-x-3">
            <div class="w-10 h-10 bg-primary/10 rounded-full flex items-center justify-center">
              <svg class="w-6 h-6 text-primary" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"></path>
              </svg>
            </div>
            <div>
              <h3 class="font-semibold text-foreground">Session ID: {{ session.id.substring(0, 8) }}...</h3>
              <p class="text-sm text-muted-foreground">User ID: {{ session.userId.substring(0, 8) }}...</p>
            </div>
          </div>
          
          <div class="flex items-center space-x-2">
            <span 
              class="inline-flex px-3 py-1 text-sm font-semibold rounded-full"
              :class="getStatusColor(session.status)"
            >
              {{ session.status }}
            </span>
            <span class="text-sm text-muted-foreground">
              {{ formatDate(session.createdAt) }}
            </span>
          </div>
        </div>
      </div>

      <!-- Messages Area -->
      <div ref="messagesContainer" class="flex-1 overflow-y-auto p-6 space-y-4 bg-background">
        <div v-if="session.messages.length === 0" class="text-center text-muted-foreground mt-20">
          <svg class="w-16 h-16 mx-auto mb-4 text-muted" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"></path>
          </svg>
          <p class="text-lg font-medium">No messages in this session</p>
          <p class="text-sm">This session might be empty or an error occurred.</p>
        </div>

        <div
          v-for="message in session.messages"
          :key="message.id"
          class="flex"
          :class="message.isUser ? 'justify-end' : 'justify-start'"
        >
          <div
            class="max-w-xs lg:max-w-md px-4 py-3 rounded-2xl shadow-sm"
            :class="message.isUser 
              ? 'bg-primary text-primary-foreground rounded-br-sm' 
              : 'bg-secondary text-secondary-foreground rounded-bl-sm'"
          >
            <div v-if="!message.isUser" class="flex items-center mb-1">
              <span class="text-xs font-medium text-muted-foreground">{{ message.aiModel }}</span>
              <span 
                v-if="message.isProcessed"
                class="ml-2 w-2 h-2 bg-green-500 rounded-full"
                title="Processed"
              ></span>
              <span 
                v-else
                class="ml-2 w-2 h-2 bg-yellow-500 rounded-full animate-pulse"
                title="Processing"
              ></span>
            </div>
            
            <p class="text-sm whitespace-pre-wrap">{{ message.content }}</p>
            
            <div class="flex items-center justify-between mt-2">
              <span class="text-xs opacity-75">
                {{ formatTime(message.createdAt) }}
              </span>
              <div v-if="message.retryCount > 0" class="text-xs opacity-75 text-destructive">
                Retry: {{ message.retryCount }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-else class="flex-1 flex items-center justify-center text-muted-foreground">
      <p class="text-lg">Loading session details or session not found...</p>
    </div>
  </div>
</template>

<script setup>
import { mockSessions } from '~/data/mockSessions.js';

definePageMeta({
  layout: 'admin'
})

const route = useRoute()
const sessionId = route.params.sessionId
const session = ref(null)
const messagesContainer = ref(null)

onMounted(() => {
  // Find the session directly from mockSessions
  session.value = mockSessions.find(s => s.id === sessionId);
  nextTick(() => {
    scrollToBottom();
  });
});

const formatDate = (date) => {
  return new Intl.DateTimeFormat('en-US', {
    month: 'short',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  }).format(date)
}

const formatTime = (date) => {
  return new Intl.DateTimeFormat('tr-TR', {
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

const scrollToBottom = () => {
  if (messagesContainer.value) {
    messagesContainer.value.scrollTop = messagesContainer.value.scrollHeight
  }
}
</script>
