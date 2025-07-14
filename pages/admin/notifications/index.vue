<template>
  <div>
    <div class="mb-8">
      <h1 class="text-3xl font-bold text-foreground mb-2">Notification Management</h1>
      <p class="text-muted-foreground">Manage notification settings, templates, and records.</p>
    </div>

    <!-- Notification Display -->
    <div v-if="notification.message" :class="[
      'p-4 mb-6 rounded-lg text-sm font-medium flex items-center justify-between',
      notification.type === 'success' ? 'bg-success/10 text-success border border-success' : 'bg-destructive/10 text-destructive border border-destructive'
    ]">
      <span>{{ notification.message }}</span>
      <button @click="clearNotification" class="text-current hover:opacity-80">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
      </button>
    </div>

    <!-- Tabs Navigation -->
    <div class="mb-6 border-b border-border">
      <nav class="-mb-px flex space-x-8" aria-label="Tabs">
        <button
          @click="activeTab = 'templates'"
          :class="[
            activeTab === 'templates'
              ? 'border-primary text-primary'
              : 'border-transparent text-muted-foreground hover:text-foreground hover:border-gray-300',
            'whitespace-nowrap py-4 px-1 border-b-2 font-medium text-sm transition-colors duration-200'
          ]"
        >
          Message Templates
        </button>
        <button
          @click="activeTab = 'records'"
          :class="[
            activeTab === 'records'
              ? 'border-primary text-primary'
              : 'border-transparent text-muted-foreground hover:text-foreground hover:border-gray-300',
            'whitespace-nowrap py-4 px-1 border-b-2 font-medium text-sm transition-colors duration-200'
          ]"
        >
          Notification Records
        </button>
        <button
          @click="activeTab = 'settings'"
          :class="[
            activeTab === 'settings'
              ? 'border-primary text-primary'
              : 'border-transparent text-muted-foreground hover:text-foreground hover:border-gray-300',
            'whitespace-nowrap py-4 px-1 border-b-2 font-medium text-sm transition-colors duration-200'
          ]"
        >
          Other Settings
        </button>
      </nav>
    </div>

    <!-- Tab Content -->
    <div class="tab-content">
      <!-- Message Templates Tab -->
      <div v-if="activeTab === 'templates'" class="card p-6 bg-card text-card-foreground rounded-lg shadow-sm mb-8">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-lg font-semibold text-foreground">Message Templates</h3>
          <button @click="openAddTemplateDialog" :disabled="isLoadingTemplates" class="btn btn-primary px-4 py-2">
            <span v-if="isLoadingTemplates" class="flex items-center">
              <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              Adding...
            </span>
            <span v-else>Add New Template</span>
          </button>
        </div>
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-border">
            <thead>
              <tr>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Name</th>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Channel</th>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Subject</th>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Description</th>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Actions</th>
              </tr>
            </thead>
            <tbody class="bg-card divide-y divide-border">
              <tr v-for="template in messageTemplates" :key="template.id">
                <td class="px-4 py-3 whitespace-nowrap text-sm font-medium text-foreground">{{ template.name }}</td>
                <td class="px-4 py-3 whitespace-nowrap text-sm text-muted-foreground">{{ template.channel }}</td>
                <td class="px-4 py-3 whitespace-nowrap text-sm text-muted-foreground">{{ template.subject || '-' }}</td>
                <td class="px-4 py-3 whitespace-nowrap text-sm text-muted-foreground">{{ template.description || '-' }}</td>
                <td class="px-4 py-3 whitespace-nowrap text-sm">
                  <button @click="openEditTemplateDialog(template)" :disabled="isLoadingTemplates" class="text-primary hover:text-primary/80 mr-2">
                    <span v-if="isLoadingTemplates && currentEditingTemplateId === template.id">Editing...</span>
                    <span v-else>Edit</span>
                  </button>
                  <button @click="deleteTemplate(template.id)" :disabled="isLoadingTemplates" class="text-destructive hover:text-destructive/80">
                    <span v-if="isLoadingTemplates && currentDeletingTemplateId === template.id">Deleting...</span>
                    <span v-else>Delete</span>
                  </button>
                </td>
              </tr>
              <tr v-if="messageTemplates.length === 0 && !isLoadingTemplates">
                <td colspan="5" class="px-4 py-3 text-center text-sm text-muted-foreground">No message templates found.</td>
              </tr>
              <tr v-if="isLoadingTemplates && messageTemplates.length === 0">
                <td colspan="5" class="px-4 py-3 text-center text-sm text-muted-foreground">Loading templates...</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- Notification Records Tab -->
      <div v-if="activeTab === 'records'" class="card p-6 bg-card text-card-foreground rounded-lg shadow-sm">
        <h3 class="text-lg font-semibold text-foreground mb-4">Notification Records</h3>
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-border">
            <thead>
              <tr>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Event Type</th>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">To</th>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Subject</th>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Template Used</th>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Sent At</th>
                <th class="px-4 py-2 text-left text-xs font-medium text-muted-foreground uppercase tracking-wider">Actions</th>
              </tr>
            </thead>
            <tbody class="bg-card divide-y divide-border">
              <tr v-for="record in notificationRecords" :key="record.id">
                <td class="px-4 py-3 whitespace-nowrap text-sm font-medium text-foreground">{{ record.eventType }}</td>
                <td class="px-4 py-3 whitespace-nowrap text-sm text-muted-foreground">{{ record.to }}</td>
                <td class="px-4 py-3 whitespace-nowrap text-sm text-muted-foreground">{{ record.subject || record.templateName }}</td>
                <td class="px-4 py-3 whitespace-nowrap text-sm text-muted-foreground">{{ record.useTemplate ? 'Yes' : 'No' }}</td>
                <td class="px-4 py-3 whitespace-nowrap text-sm text-muted-foreground">{{ new Date(record.createdAt).toLocaleString() }}</td>
                <td class="px-4 py-3 whitespace-nowrap text-sm">
                  <button 
                    @click="openNotificationDetails(record)" 
                    class="text-primary hover:text-primary/80 font-medium"
                  >
                    View Details
                  </button>
                </td>
              </tr>
              <tr v-if="notificationRecords.length === 0">
                <td colspan="6" class="px-4 py-3 text-center text-sm text-muted-foreground">No notification records found.</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- Other Settings Tab -->
      <div v-if="activeTab === 'settings'" class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <!-- Email Settings -->
        <div class="card p-6 bg-card text-card-foreground rounded-lg shadow-sm">
          <h3 class="text-lg font-semibold text-foreground mb-4">Email Settings</h3>
          <form @submit.prevent="saveEmailSettings">
            <div class="space-y-4">
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">SMTP Host</label>
                <input v-model="emailSettings.smtpHost" type="text" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="smtp.example.com" />
              </div>
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">SMTP Port</label>
                <input v-model.number="emailSettings.smtpPort" type="number" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="587" />
              </div>
              <div class="flex items-center">
                <input v-model="emailSettings.useSsl" type="checkbox" class="h-4 w-4 text-primary focus:ring-primary border-input rounded" />
                <label class="ml-2 block text-sm text-foreground">Use SSL</label>
              </div>
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">Sender Email</label>
                <input v-model="emailSettings.senderEmail" type="email" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="noreply@example.com" />
              </div>
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">Sender Name</label>
                <input v-model="emailSettings.senderName" type="text" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="TrendAI Notifications" />
              </div>
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">Username</label>
                <input v-model="emailSettings.username" type="text" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="smtp_username" />
              </div>
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">Password</label>
                <input v-model="emailSettings.password" type="password" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="********" />
              </div>
            </div>
            <div class="mt-6 flex justify-end">
              <button type="submit" :disabled="isLoadingEmailSettings" class="btn btn-primary px-4 py-2">
                <span v-if="isLoadingEmailSettings" class="flex items-center">
                  <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                  </svg>
                  Saving...
                </span>
                <span v-else>Save Email Settings</span>
              </button>
            </div>
          </form>
        </div>

        <!-- Push Notification Settings -->
        <div class="card p-6 bg-card text-card-foreground rounded-lg shadow-sm">
          <h3 class="text-lg font-semibold text-foreground mb-4">Push Notification Settings</h3>
          <form @submit.prevent="savePushSettings">
            <div class="space-y-4">
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">FCM Server Key</label>
                <input v-model="pushSettings.fcmServerKey" type="text" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="Your Firebase Cloud Messaging Server Key" />
              </div>
            </div>
            <div class="mt-6 flex justify-end">
              <button type="submit" :disabled="isLoadingPushSettings" class="btn btn-primary px-4 py-2">
                <span v-if="isLoadingPushSettings" class="flex items-center">
                  <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                  </svg>
                  Saving...
                </span>
                <span v-else>Save Push Settings</span>
              </button>
            </div>
          </form>
        </div>

        <!-- SMS Settings -->
        <div class="card p-6 bg-card text-card-foreground rounded-lg shadow-sm">
          <h3 class="text-lg font-semibold text-foreground mb-4">SMS Settings</h3>
          <form @submit.prevent="saveSmsSettings">
            <div class="space-y-4">
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">Provider Name</label>
                <input v-model="smsSettings.providerName" type="text" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="Twilio, NetGSM, etc." />
              </div>
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">API Key</label>
                <input v-model="smsSettings.apiKey" type="text" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="Your SMS API Key" />
              </div>
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">API Secret</label>
                <input v-model="smsSettings.apiSecret" type="text" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="Your SMS API Secret" />
              </div>
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">Sender Number</label>
                <input v-model="smsSettings.senderNumber" type="text" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="+1234567890" />
              </div>
            </div>
            <div class="mt-6 flex justify-end">
              <button type="submit" :disabled="isLoadingSmsSettings" class="btn btn-primary px-4 py-2">
                <span v-if="isLoadingSmsSettings" class="flex items-center">
                  <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                  </svg>
                  Saving...
                </span>
                <span v-else>Save SMS Settings</span>
              </button>
            </div>
          </form>
        </div>

        <!-- Telegram Settings -->
        <div class="card p-6 bg-card text-card-foreground rounded-lg shadow-sm">
          <h3 class="text-lg font-semibold text-foreground mb-4">Telegram Settings</h3>
          <form @submit.prevent="saveTelegramSettings">
            <div class="space-y-4">
              <div>
                <label class="block text-sm font-medium text-foreground mb-2">Bot Token</label>
                <input v-model="telegramSettings.botToken" type="text" class="w-full px-3 py-2 border border-input rounded-lg focus:ring-2 focus:ring-primary focus:border-primary bg-background text-foreground" placeholder="Your Telegram Bot Token" />
              </div>
            </div>
            <div class="mt-6 flex justify-end">
              <button type="submit" :disabled="isLoadingTelegramSettings" class="btn btn-primary px-4 py-2">
                <span v-if="isLoadingTelegramSettings" class="flex items-center">
                  <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                  </svg>
                  Saving...
                </span>
                <span v-else>Save Telegram Settings</span>
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- Notification Template Add/Edit Dialog -->
    <NotificationTemplateDialog
      :is-visible="showTemplateDialog"
      :template="templateToEdit"
      :is-loading="isLoadingTemplates"
      @save="handleSaveTemplate"
      @close="closeTemplateDialog"
    />

    <!-- Notification Details Modal -->
    <NotificationDetailsModal
      :is-visible="showDetailsModal"
      :notification="selectedNotification"
      @close="closeDetailsModal"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import NotificationTemplateDialog from '~/components/NotificationTemplateDialog.vue';
import NotificationDetailsModal from '~/components/NotificationDetailsModal.vue';

definePageMeta({
  layout: 'admin'
})

const activeTab = ref('templates'); // Default active tab

// Loading states
const isLoadingTemplates = ref(false);
const currentEditingTemplateId = ref(null);
const currentDeletingTemplateId = ref(null);

const isLoadingEmailSettings = ref(false);
const isLoadingPushSettings = ref(false);
const isLoadingSmsSettings = ref(false);
const isLoadingTelegramSettings = ref(false);

// Notification state
const notification = ref({
  message: '',
  type: '' // 'success' or 'destructive'
});

const showNotification = (message, type) => {
  notification.value.message = message;
  notification.value.type = type;
  setTimeout(() => {
    clearNotification();
  }, 5000); // Clear notification after 5 seconds
};

const clearNotification = () => {
  notification.value.message = '';
  notification.value.type = '';
};

// Mock Data for Settings
const emailSettings = ref({
  smtpHost: 'smtp.example.com',
  smtpPort: 587,
  useSsl: true,
  senderEmail: 'noreply@trendai.com',
  senderName: 'TrendAI Notifications',
  username: 'trendai_smtp',
  password: 'mock_password_email'
});

const pushSettings = ref({
  fcmServerKey: 'mock_fcm_server_key_12345'
});

const smsSettings = ref({
  providerName: 'Twilio',
  apiKey: 'mock_sms_api_key_abc',
  apiSecret: 'mock_sms_api_secret_xyz',
  senderNumber: '+1501712266'
});

const telegramSettings = ref({
  botToken: 'mock_telegram_bot_token_67890'
});

// Mock Data for Message Templates
const messageTemplates = ref([
  {
    id: 'tpl1',
    dataType: 'Project',
    name: 'WelcomeEmail',
    description: 'Email sent to new users upon registration.',
    body: 'Welcome to TrendAI! Your journey starts now.',
    channel: 'Email',
    subject: 'Welcome to TrendAI!',
    isHtml: true,
  },
  {
    id: 'tpl2',
    dataType: 'Project',
    name: 'PasswordResetSMS',
    description: 'SMS for password reset verification.',
    body: 'Your password reset code is: {code}',
    channel: 'SMS',
    subject: null,
    isHtml: false,
  },
  {
    id: 'tpl3',
    dataType: 'Project',
    name: 'NewFeaturePush',
    description: 'Push notification for new feature announcement.',
    body: 'Exciting new features are here! Check them out.',
    channel: 'PushNotification',
    subject: 'New Features Available!',
    isHtml: false,
  }
]);

// Mock Data for Notification Records
const notificationRecords = ref([
  {
    id: 'rec1',
    eventType: 'UserRegistered',
    to: 'user1@example.com',
    body: 'Welcome to TrendAI! Your journey starts now.',
    subject: 'Welcome to TrendAI!',
    templateName: 'WelcomeEmail',
    useTemplate: true,
    isHtml: true,
    createdAt: new Date(Date.now() - 3600000).toISOString() // 1 hour ago
  },
  {
    id: 'rec2',
    eventType: 'PasswordReset',
    to: '+1234567890',
    body: 'Your password reset code is: 123456',
    subject: null,
    templateName: 'PasswordResetSMS',
    useTemplate: true,
    isHtml: false,
    createdAt: new Date(Date.now() - 7200000).toISOString() // 2 hours ago
  },
  {
    id: 'rec3',
    eventType: 'SystemAlert',
    to: 'admin@example.com',
    body: 'Database backup failed. Please check logs.',
    subject: 'Critical System Alert',
    templateName: null,
    useTemplate: false,
    isHtml: false,
    createdAt: new Date(Date.now() - 10800000).toISOString() // 3 hours ago
  }
]);

// Methods for Settings
const saveEmailSettings = async () => {
  isLoadingEmailSettings.value = true;
  clearNotification();
  try {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1500));
    console.log('Saving Email Settings:', emailSettings.value);
    showNotification('Email Settings saved successfully!', 'success');
  } catch (error) {
    console.error('Error saving email settings:', error);
    showNotification('Failed to save Email Settings.', 'destructive');
  } finally {
    isLoadingEmailSettings.value = false;
  }
};

const savePushSettings = async () => {
  isLoadingPushSettings.value = true;
  clearNotification();
  try {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1500));
    console.log('Saving Push Notification Settings:', pushSettings.value);
    showNotification('Push Notification Settings saved successfully!', 'success');
  } catch (error) {
    console.error('Error saving push settings:', error);
    showNotification('Failed to save Push Notification Settings.', 'destructive');
  } finally {
    isLoadingPushSettings.value = false;
  }
};

const saveSmsSettings = async () => {
  isLoadingSmsSettings.value = true;
  clearNotification();
  try {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1500));
    console.log('Saving SMS Settings:', smsSettings.value);
    showNotification('SMS Settings saved successfully!', 'success');
  } catch (error) {
    console.error('Error saving SMS settings:', error);
    showNotification('Failed to save SMS Settings.', 'destructive');
  } finally {
    isLoadingSmsSettings.value = false;
  }
};

const saveTelegramSettings = async () => {
  isLoadingTelegramSettings.value = true;
  clearNotification();
  try {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1500));
    console.log('Saving Telegram Settings:', telegramSettings.value);
    showNotification('Telegram Settings saved successfully!', 'success');
  } catch (error) {
    console.error('Error saving Telegram settings:', error);
    showNotification('Failed to save Telegram Settings.', 'destructive');
  } finally {
    isLoadingTelegramSettings.value = false;
  }
};

// Dialog state for Message Templates
const showTemplateDialog = ref(false);
const templateToEdit = ref(null); // Holds the template object when editing, null when adding

// Details modal state
const showDetailsModal = ref(false);
const selectedNotification = ref(null);

const openAddTemplateDialog = () => {
  templateToEdit.value = null; // Clear for new template
  showTemplateDialog.value = true;
};

const openEditTemplateDialog = (template) => {
  templateToEdit.value = { ...template }; // Pass a copy to avoid direct mutation
  showTemplateDialog.value = true;
};

const closeTemplateDialog = () => {
  showTemplateDialog.value = false;
  templateToEdit.value = null; // Clear template to edit
};

const handleSaveTemplate = async (templateData) => {
  isLoadingTemplates.value = true;
  clearNotification();
  try {
    await new Promise(resolve => setTimeout(resolve, 1500)); // Simulate API call

    if (templateData.id) {
      // Editing existing template
      const index = messageTemplates.value.findIndex(t => t.id === templateData.id);
      if (index !== -1) {
        messageTemplates.value[index] = templateData;
        showNotification(`Template "${templateData.name}" updated successfully!`, 'success');
      }
    } else {
      // Adding new template
      const newId = `tpl${messageTemplates.value.length + 1}`;
      messageTemplates.value.push({ ...templateData, id: newId });
      showNotification('New template added successfully!', 'success');
    }
    closeTemplateDialog();
  } catch (error) {
    console.error('Error saving template:', error);
    showNotification(`Failed to save template "${templateData.name}".`, 'destructive');
  } finally {
    isLoadingTemplates.value = false;
  }
};

const deleteTemplate = async (id) => {
  if (confirm('Are you sure you want to delete this template?')) {
    isLoadingTemplates.value = true;
    currentDeletingTemplateId.value = id;
    clearNotification();
    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 1500));
      messageTemplates.value = messageTemplates.value.filter(t => t.id !== id);
      showNotification('Template deleted successfully!', 'success');
    } catch (error) {
      console.error('Error deleting template:', error);
      showNotification('Failed to delete template.', 'destructive');
    } finally {
      isLoadingTemplates.value = false;
      currentDeletingTemplateId.value = null;
    }
  }
};

// Notification details methods
const openNotificationDetails = (notification) => {
  selectedNotification.value = notification;
  showDetailsModal.value = true;
};

const closeDetailsModal = () => {
  showDetailsModal.value = false;
  selectedNotification.value = null;
};
</script>
