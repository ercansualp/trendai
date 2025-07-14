<template>
  <div class="h-full flex flex-col">
    <div class="mb-6">
      <h1 class="text-3xl font-bold text-foreground mb-2">Live Chat</h1>
      <p class="text-muted-foreground">Chat directly with the AI assistant</p>
    </div>

    <!-- Chat Container -->
    <div class="flex-1 card flex flex-col" style="height: calc(100vh - 200px);">
      <!-- Chat Header -->
      <div class="px-6 py-4 border-b border-border bg-secondary rounded-t-lg">
        <div class="flex items-center justify-between">
          <div class="flex items-center space-x-3">
            <div class="w-10 h-10 bg-primary/10 rounded-full flex items-center justify-center">
              <svg class="w-6 h-6 text-primary" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"></path>
              </svg>
            </div>
            <div>
              <h3 class="font-semibold text-foreground">TrendAI Assistant</h3>
              <p class="text-sm text-muted-foreground">{{ isTyping ? 'Typing...' : 'Online' }}</p>
            </div>
          </div>
          
          <div class="flex items-center space-x-2">
            <select 
              v-model="selectedModel"
              class="text-sm px-3 py-1 border border-input rounded-lg focus:ring-2 focus:ring-ring focus:border-primary bg-background text-foreground"
            >
              <option value="GPT-4">GPT-4</option>
              <option value="GPT-3.5">GPT-3.5</option>
              <option value="Claude">Claude</option>
            </select>
            
            <button
              @click="clearChat"
              class="text-muted-foreground hover:text-foreground p-2 rounded-lg hover:bg-accent"
              title="Clear chat"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path>
              </svg>
            </button>
          </div>
        </div>
      </div>

      <!-- Messages Area -->
      <div ref="messagesContainer" class="flex-1 overflow-y-auto p-6 space-y-4">
        <div v-if="messages.length === 0" class="text-center text-muted-foreground mt-20">
          <svg class="w-16 h-16 mx-auto mb-4 text-muted" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"></path>
          </svg>
          <p class="text-lg font-medium">Start a conversation</p>
          <p class="text-sm">Type a message below to begin chatting with the AI assistant</p>
        </div>

        <div
          v-for="message in messages"
          :key="message.id"
          class="flex"
          :class="message.isUser ? 'justify-end' : 'justify-start'"
        >
          <div
            class="max-w-xs lg:max-w-md px-4 py-3 rounded-2xl"
            :class="message.isUser 
              ? 'bg-primary text-primary-foreground rounded-br-sm' 
              : 'bg-secondary text-secondary-foreground rounded-bl-sm'"
          >
            <div v-if="!message.isUser" class="flex items-center mb-1">
              <span class="text-xs font-medium text-muted-foreground">{{ message.aiModel }}</span>
              <span 
                v-if="message.isProcessed"
                class="ml-2 w-2 h-2 bg-success rounded-full"
                title="Processed"
              ></span>
              <span 
                v-else
                class="ml-2 w-2 h-2 bg-warning rounded-full animate-pulse"
                title="Processing"
              ></span>
            </div>
            
            <p class="text-sm whitespace-pre-wrap">{{ message.content }}</p>
            
            <div class="flex items-center justify-between mt-2">
              <span class="text-xs opacity-75">
                {{ formatTime(message.timestamp) }}
              </span>
              <div v-if="message.retryCount > 0" class="text-xs opacity-75">
                Retry: {{ message.retryCount }}
              </div>
            </div>
          </div>
        </div>

        <!-- Typing Indicator -->
        <div v-if="isTyping" class="flex justify-start">
          <div class="bg-secondary rounded-2xl rounded-bl-sm px-4 py-3">
            <div class="flex space-x-1">
              <div class="w-2 h-2 bg-muted-foreground rounded-full animate-bounce"></div>
              <div class="w-2 h-2 bg-muted-foreground rounded-full animate-bounce" style="animation-delay: 0.1s"></div>
              <div class="w-2 h-2 bg-muted-foreground rounded-full animate-bounce" style="animation-delay: 0.2s"></div>
            </div>
          </div>
        </div>
      </div>

      <!-- Message Input -->
      <div class="border-t border-border p-4">
        <form @submit.prevent="sendMessage" class="flex space-x-3">
          <div class="flex-1 relative">
            <input
              v-model="newMessage"
              type="text"
              placeholder="Type your message..."
              class="w-full px-4 py-3 border border-input rounded-full focus:ring-2 focus:ring-ring focus:border-primary pr-12 bg-background text-foreground"
              :disabled="isTyping"
            />
            <button
              type="button"
              @click="toggleEmojiPicker"
              class="absolute right-3 top-1/2 transform -translate-y-1/2 text-muted-foreground hover:text-foreground"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14.828 14.828a4 4 0 01-5.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
              </svg>
            </button>
          </div>
          
          <button
            type="submit"
            :disabled="!newMessage.trim() || isTyping"
            class="px-6 py-3 bg-primary text-primary-foreground rounded-full hover:bg-primary/90 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
          >
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"></path>
            </svg>
          </button>
        </form>
      </div>
    </div>

    <!-- Quick Actions -->
    <div class="mt-4 flex flex-wrap gap-2">
      <button
        v-for="quickAction in quickActions"
        :key="quickAction.text"
        @click="sendQuickMessage(quickAction.text)"
        class="px-3 py-1 text-sm bg-muted text-muted-foreground rounded-full hover:bg-muted/80 transition-colors"
      >
        {{ quickAction.text }}
      </button>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'admin'
})

const newMessage = ref('')
const messages = ref([])
const isTyping = ref(false)
const selectedModel = ref('GPT-4')
const messagesContainer = ref(null)
const currentSessionId = ref(null)

const quickActions = [
  { text: 'Hello, how can you help me?' },
  { text: 'What are your capabilities?' },
  { text: 'Explain machine learning' },
  { text: 'Help me with coding' },
  { text: 'What is the weather like?' }
]

// Initialize session
onMounted(() => {
  currentSessionId.value = generateSessionId()
  // Add welcome message
  addMessage({
    content: 'Hello! I\'m your AI assistant. How can I help you today?',
    isUser: false,
    aiModel: selectedModel.value,
    isProcessed: true
  })
})

const generateSessionId = () => {
  return 'session-' + Math.random().toString(36).substr(2, 9)
}

const generateMessageId = () => {
  return 'msg-' + Math.random().toString(36).substr(2, 9)
}

const addMessage = (messageData) => {
  const message = {
    id: generateMessageId(),
    timestamp: new Date(),
    retryCount: 0,
    ...messageData
  }
  messages.value.push(message)
  nextTick(() => {
    scrollToBottom()
  })
  return message
}

const sendMessage = async () => {
  if (!newMessage.value.trim() || isTyping.value) return

  const userMessage = newMessage.value.trim()
  newMessage.value = ''

  // Add user message
  addMessage({
    content: userMessage,
    isUser: true,
    isProcessed: true
  })

  // Show typing indicator
  isTyping.value = true

  try {
    // Simulate AI response delay
    await new Promise(resolve => setTimeout(resolve, 1000 + Math.random() * 2000))

    // Generate AI response (in real app, this would be an API call)
    const aiResponse = await generateAIResponse(userMessage)

    // Add AI response
    addMessage({
      content: aiResponse,
      isUser: false,
      aiModel: selectedModel.value,
      isProcessed: true
    })

  } catch (error) {
    console.error('Error sending message:', error)
    
    // Add error message
    addMessage({
      content: 'Sorry, I encountered an error processing your message. Please try again.',
      isUser: false,
      aiModel: selectedModel.value,
      isProcessed: false,
      retryCount: 1
    })
  } finally {
    isTyping.value = false
  }
}

const generateAIResponse = async (userMessage) => {
  // Simulate different responses based on message content
  const responses = {
    'hello': 'Hello! How can I assist you today?',
    'help': 'I\'m here to help! I can answer questions, provide information, help with coding, explain concepts, and much more. What would you like to know?',
    'capabilities': 'I can help you with a wide range of tasks including:\n\n• Answering questions\n• Explaining complex topics\n• Helping with code\n• Writing and editing\n• Problem solving\n• Data analysis\n• And much more!\n\nWhat specific area would you like help with?',
    'machine learning': 'Machine Learning is a subset of artificial intelligence (AI) that enables computers to learn and improve from experience without being explicitly programmed for every task.\n\nKey concepts:\n• Algorithms learn patterns from data\n• Models make predictions on new data\n• Common types: supervised, unsupervised, reinforcement learning\n• Applications: image recognition, natural language processing, recommendation systems\n\nWould you like me to explain any specific aspect in more detail?',
    'coding': 'I\'d be happy to help you with coding! I can assist with:\n\n• Writing code in various languages\n• Debugging and troubleshooting\n• Code review and optimization\n• Explaining programming concepts\n• Architecture and design patterns\n\nWhat programming language or specific coding challenge are you working on?',
    'weather': 'I don\'t have access to real-time weather data, but I can help you understand:\n\n• How to integrate weather APIs\n• Weather data analysis\n• Climate patterns\n• Weather prediction models\n\nFor current weather, I recommend checking a weather service like OpenWeatherMap or your local weather app.'
  }

  const lowerMessage = userMessage.toLowerCase()
  
  for (const [key, response] of Object.entries(responses)) {
    if (lowerMessage.includes(key)) {
      return response
    }
  }

  // Default response
  return `I understand you're asking about "${userMessage}". While I'd love to provide a detailed response, this is a demo interface. In a real implementation, this would connect to your AI service to generate contextual responses based on your configured AI model (${selectedModel.value}).`
}

const sendQuickMessage = (message) => {
  newMessage.value = message
  sendMessage()
}

const clearChat = () => {
  if (confirm('Are you sure you want to clear the chat history?')) {
    messages.value = []
    currentSessionId.value = generateSessionId()
    
    // Add welcome message
    addMessage({
      content: 'Chat cleared. How can I help you?',
      isUser: false,
      aiModel: selectedModel.value,
      isProcessed: true
    })
  }
}

const toggleEmojiPicker = () => {
  // Emoji picker functionality would go here
  console.log('Toggle emoji picker')
}

const scrollToBottom = () => {
  if (messagesContainer.value) {
    messagesContainer.value.scrollTop = messagesContainer.value.scrollHeight
  }
}

const formatTime = (date) => {
  return new Intl.DateTimeFormat('tr-TR', {
    hour: '2-digit',
    minute: '2-digit'
  }).format(date)
}

// Watch for model changes
watch(selectedModel, (newModel) => {
  addMessage({
    content: `AI model switched to ${newModel}`,
    isUser: false,
    aiModel: newModel,
    isProcessed: true
  })
})
</script>
