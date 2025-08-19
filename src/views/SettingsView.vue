<template>
  <AdminLayout>
    <div class="space-y-6">
      <!-- Page Header -->
      <div class="flex items-center justify-between">
        <div>
          <h1 class="text-2xl font-bold text-gray-900">Settings</h1>
          <p class="text-gray-600">Manage system settings and configurations</p>
        </div>
        <div class="flex items-center space-x-3">
          <button
            @click="saveAllSettings"
            class="px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors flex items-center space-x-2"
          >
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiContentSave" />
            </svg>
            <span>Save Changes</span>
          </button>
        </div>
      </div>

      <!-- Settings Navigation -->
      <div class="bg-white rounded-xl shadow-card overflow-hidden">
        <div class="border-b border-gray-200">
          <nav class="-mb-px flex space-x-8 px-6">
            <button
              v-for="tab in settingsTabs"
              :key="tab.key"
              @click="activeTab = tab.key"
              :class="[
                'py-4 px-1 border-b-2 font-medium text-sm transition-colors',
                activeTab === tab.key
                  ? 'border-primary-500 text-primary-600'
                  : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300'
              ]"
            >
              <div class="flex items-center space-x-2">
                <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="tab.icon" />
                </svg>
                <span>{{ tab.label }}</span>
              </div>
            </button>
          </nav>
        </div>

        <div class="p-6">
          <!-- General Settings -->
          <div v-if="activeTab === 'general'" class="space-y-6">
            <div>
              <h3 class="text-lg font-semibold text-gray-900 mb-4">General Settings</h3>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">System Name</label>
                  <input
                    type="text"
                    v-model="settings.general.systemName"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    placeholder="CoWork Admin System"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Admin Email</label>
                  <input
                    type="email"
                    v-model="settings.general.adminEmail"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    placeholder="admin@cowork.com"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Timezone</label>
                  <select v-model="settings.general.timezone" class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black">
                    <option value="UTC">UTC</option>
                    <option value="America/New_York">Eastern Time</option>
                    <option value="America/Chicago">Central Time</option>
                    <option value="America/Denver">Mountain Time</option>
                    <option value="America/Los_Angeles">Pacific Time</option>
                    <option value="Europe/London">London</option>
                    <option value="Asia/Tokyo">Tokyo</option>
                  </select>
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Currency</label>
                  <select v-model="settings.general.currency" class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black">
                    <option value="USD">USD - US Dollar</option>
                    <option value="EUR">EUR - Euro</option>
                    <option value="GBP">GBP - British Pound</option>
                    <option value="JPY">JPY - Japanese Yen</option>
                    <option value="CAD">CAD - Canadian Dollar</option>
                  </select>
                </div>
              </div>
            </div>

            <div>
              <h4 class="text-md font-medium text-gray-900 mb-3">System Preferences</h4>
              <div class="space-y-3">
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.general.enableNotifications"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Enable email notifications</span>
                </label>
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.general.enableAuditLog"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Enable audit logging</span>
                </label>
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.general.maintenanceMode"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Maintenance mode</span>
                </label>
              </div>
            </div>
          </div>

          <!-- Security Settings -->
          <div v-if="activeTab === 'security'" class="space-y-6">
            <div>
              <h3 class="text-lg font-semibold text-gray-900 mb-4">Security Settings</h3>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Session Timeout (minutes)</label>
                  <input
                    type="number"
                    v-model="settings.security.sessionTimeout"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="5"
                    max="480"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Max Login Attempts</label>
                  <input
                    type="number"
                    v-model="settings.security.maxLoginAttempts"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="3"
                    max="10"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Password Min Length</label>
                  <input
                    type="number"
                    v-model="settings.security.passwordMinLength"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="6"
                    max="20"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">2FA Default</label>
                  <select v-model="settings.security.twoFactorDefault" class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black">
                    <option value="optional">Optional</option>
                    <option value="required">Required</option>
                    <option value="disabled">Disabled</option>
                  </select>
                </div>
              </div>
            </div>

            <div>
              <h4 class="text-md font-medium text-gray-900 mb-3">Security Features</h4>
              <div class="space-y-3">
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.security.requireStrongPasswords"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Require strong passwords</span>
                </label>
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.security.enableIpWhitelist"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Enable IP whitelist</span>
                </label>
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.security.logSecurityEvents"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Log security events</span>
                </label>
              </div>
            </div>
          </div>

          <!-- Booking Settings -->
          <div v-if="activeTab === 'booking'" class="space-y-6">
            <div>
              <h3 class="text-lg font-semibold text-gray-900 mb-4">Booking Settings</h3>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Default Booking Duration (hours)</label>
                  <input
                    type="number"
                    v-model="settings.booking.defaultDuration"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="1"
                    max="24"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Max Advance Booking (days)</label>
                  <input
                    type="number"
                    v-model="settings.booking.maxAdvanceBooking"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="1"
                    max="365"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Cancellation Policy (hours)</label>
                  <input
                    type="number"
                    v-model="settings.booking.cancellationPolicy"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="1"
                    max="72"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Auto-confirm Bookings</label>
                  <select v-model="settings.booking.autoConfirm" class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black">
                    <option value="always">Always</option>
                    <option value="verified_users">Verified Users Only</option>
                    <option value="never">Never</option>
                  </select>
                </div>
              </div>
            </div>

            <div>
              <h4 class="text-md font-medium text-gray-900 mb-3">Booking Features</h4>
              <div class="space-y-3">
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.booking.allowRecurring"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Allow recurring bookings</span>
                </label>
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.booking.requireApproval"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Require admin approval</span>
                </label>
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.booking.sendReminders"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Send booking reminders</span>
                </label>
              </div>
            </div>
          </div>

          <!-- Payment Settings -->
          <div v-if="activeTab === 'payment'" class="space-y-6">
            <div>
              <h3 class="text-lg font-semibold text-gray-900 mb-4">Payment Settings</h3>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Default Commission Rate (%)</label>
                  <input
                    type="number"
                    v-model="settings.payment.defaultCommission"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="0"
                    max="50"
                    step="0.1"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Payment Due Days</label>
                  <input
                    type="number"
                    v-model="settings.payment.paymentDueDays"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="1"
                    max="90"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Late Fee (%)</label>
                  <input
                    type="number"
                    v-model="settings.payment.lateFee"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="0"
                    max="20"
                    step="0.1"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Refund Policy (days)</label>
                  <input
                    type="number"
                    v-model="settings.payment.refundPolicy"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    min="0"
                    max="30"
                  />
                </div>
              </div>
            </div>

            <div>
              <h4 class="text-md font-medium text-gray-900 mb-3">Payment Methods</h4>
              <div class="space-y-3">
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.payment.acceptCreditCard"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Accept credit cards</span>
                </label>
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.payment.acceptBankTransfer"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Accept bank transfers</span>
                </label>
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="settings.payment.acceptCrypto"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Accept cryptocurrency</span>
                </label>
              </div>
            </div>
          </div>

          <!-- Notification Settings -->
          <div v-if="activeTab === 'notifications'" class="space-y-6">
            <div>
              <h3 class="text-lg font-semibold text-gray-900 mb-4">Email Notifications</h3>
              <div class="space-y-4">
                <div class="flex items-center justify-between p-4 border border-gray-200 rounded-lg">
                  <div>
                    <h4 class="text-sm font-medium text-gray-900">New Booking Notifications</h4>
                    <p class="text-sm text-gray-500">Get notified when new bookings are made</p>
                  </div>
                  <label class="flex items-center">
                    <input 
                      type="checkbox" 
                      v-model="settings.notifications.newBooking"
                      class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                    />
                  </label>
                </div>
                
                <div class="flex items-center justify-between p-4 border border-gray-200 rounded-lg">
                  <div>
                    <h4 class="text-sm font-medium text-gray-900">Payment Notifications</h4>
                    <p class="text-sm text-gray-500">Get notified about payment status changes</p>
                  </div>
                  <label class="flex items-center">
                    <input 
                      type="checkbox" 
                      v-model="settings.notifications.payment"
                      class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                    />
                  </label>
                </div>
                
                <div class="flex items-center justify-between p-4 border border-gray-200 rounded-lg">
                  <div>
                    <h4 class="text-sm font-medium text-gray-900">System Alerts</h4>
                    <p class="text-sm text-gray-500">Get notified about system issues and maintenance</p>
                  </div>
                  <label class="flex items-center">
                    <input 
                      type="checkbox" 
                      v-model="settings.notifications.systemAlerts"
                      class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                    />
                  </label>
                </div>
                
                <div class="flex items-center justify-between p-4 border border-gray-200 rounded-lg">
                  <div>
                    <h4 class="text-sm font-medium text-gray-900">User Activity</h4>
                    <p class="text-sm text-gray-500">Get notified about user registrations and activity</p>
                  </div>
                  <label class="flex items-center">
                    <input 
                      type="checkbox" 
                      v-model="settings.notifications.userActivity"
                      class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                    />
                  </label>
                </div>
              </div>
            </div>

            <div>
              <h3 class="text-lg font-semibold text-gray-900 mb-4">Email Configuration</h3>
              <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">SMTP Server</label>
                  <input
                    type="text"
                    v-model="settings.notifications.smtpServer"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    placeholder="smtp.gmail.com"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">SMTP Port</label>
                  <input
                    type="number"
                    v-model="settings.notifications.smtpPort"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    placeholder="587"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">From Email</label>
                  <input
                    type="email"
                    v-model="settings.notifications.fromEmail"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    placeholder="noreply@cowork.com"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">From Name</label>
                  <input
                    type="text"
                    v-model="settings.notifications.fromName"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                    placeholder="CoWork Admin"
                  />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Save Confirmation -->
      <div v-if="showSaveMessage" class="fixed bottom-4 right-4 bg-green-500 text-white px-6 py-3 rounded-lg shadow-lg flex items-center space-x-2">
        <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
          <path :d="mdiCheck" />
        </svg>
        <span>Settings saved successfully!</span>
      </div>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { 
  mdiContentSave,
  mdiCog,
  mdiSecurity,
  mdiCalendarClock,
  mdiCurrencyUsd,
  mdiBell,
  mdiCheck
} from '@mdi/js'

// State
const activeTab = ref('general')
const showSaveMessage = ref(false)

// Settings tabs
const settingsTabs = [
  { key: 'general', label: 'General', icon: mdiCog },
  { key: 'security', label: 'Security', icon: mdiSecurity },
  { key: 'booking', label: 'Booking', icon: mdiCalendarClock },
  { key: 'payment', label: 'Payment', icon: mdiCurrencyUsd },
  { key: 'notifications', label: 'Notifications', icon: mdiBell }
]

// Settings data
const settings = ref({
  general: {
    systemName: 'CoWork Admin System',
    adminEmail: 'admin@cowork.com',
    timezone: 'UTC',
    currency: 'USD',
    enableNotifications: true,
    enableAuditLog: true,
    maintenanceMode: false
  },
  security: {
    sessionTimeout: 120,
    maxLoginAttempts: 5,
    passwordMinLength: 8,
    twoFactorDefault: 'optional',
    requireStrongPasswords: true,
    enableIpWhitelist: false,
    logSecurityEvents: true
  },
  booking: {
    defaultDuration: 2,
    maxAdvanceBooking: 30,
    cancellationPolicy: 24,
    autoConfirm: 'verified_users',
    allowRecurring: true,
    requireApproval: false,
    sendReminders: true
  },
  payment: {
    defaultCommission: 12.5,
    paymentDueDays: 30,
    lateFee: 5.0,
    refundPolicy: 7,
    acceptCreditCard: true,
    acceptBankTransfer: true,
    acceptCrypto: false
  },
  notifications: {
    newBooking: true,
    payment: true,
    systemAlerts: true,
    userActivity: false,
    smtpServer: 'smtp.gmail.com',
    smtpPort: 587,
    fromEmail: 'noreply@cowork.com',
    fromName: 'CoWork Admin'
  }
})

// Methods
const saveAllSettings = () => {
  // In a real app, this would save to backend
  console.log('Saving settings:', settings.value)
  
  // Show success message
  showSaveMessage.value = true
  setTimeout(() => {
    showSaveMessage.value = false
  }, 3000)
}
</script>
