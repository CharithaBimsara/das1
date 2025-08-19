<template>
  <AdminLayout>
    <div class="space-y-6">
      <!-- Page Header -->
      <div class="flex items-center justify-between">
        <div>
          <h1 class="text-2xl font-bold text-gray-900">Admin Profile</h1>
          <p class="text-gray-600">Manage your account settings and preferences</p>
        </div>
        <div class="flex items-center space-x-3">
          <button
            @click="editMode = !editMode"
            class="px-4 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors flex items-center space-x-2"
          >
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
              <path :d="editMode ? mdiClose : mdiPencil" />
            </svg>
            <span>{{ editMode ? 'Cancel' : 'Edit Profile' }}</span>
          </button>
          <button
            v-if="editMode"
            @click="saveProfile"
            class="px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors flex items-center space-x-2"
          >
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiContentSave" />
            </svg>
            <span>Save Changes</span>
          </button>
        </div>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <!-- Profile Overview Card -->
        <div class="lg:col-span-1">
          <div class="bg-white rounded-xl shadow-card p-6">
            <div class="text-center">
              <div class="relative inline-block">
                <img 
                  class="h-24 w-24 rounded-full object-cover mx-auto"
                  :src="profile.avatar"
                  :alt="profile.name"
                />
                <button 
                  v-if="editMode"
                  class="absolute bottom-0 right-0 bg-primary-600 text-white rounded-full p-2 hover:bg-primary-700 transition-colors"
                  @click="changeAvatar"
                >
                  <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiCamera" />
                  </svg>
                </button>
              </div>
              <h3 class="mt-4 text-lg font-semibold text-gray-900">{{ profile.name }}</h3>
              <p class="text-gray-600">{{ profile.role }}</p>
              <p class="text-sm text-gray-500 mt-1">{{ profile.email }}</p>
              
              <div class="mt-6 flex items-center justify-center space-x-4">
                <div class="text-center">
                  <p class="text-2xl font-bold text-gray-900">{{ profile.stats.totalLogins }}</p>
                  <p class="text-sm text-gray-500">Total Logins</p>
                </div>
                <div class="text-center">
                  <p class="text-2xl font-bold text-gray-900">{{ profile.stats.daysSinceJoin }}</p>
                  <p class="text-sm text-gray-500">Days Active</p>
                </div>
              </div>

              <div class="mt-6 pt-6 border-t border-gray-200">
                <div class="flex items-center justify-center space-x-2 text-sm text-gray-600">
                  <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiClockOutline" />
                  </svg>
                  <span>Last login: {{ formatDate(profile.lastLogin) }}</span>
                </div>
                <div class="flex items-center justify-center space-x-2 text-sm text-gray-600 mt-2">
                  <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiCalendarPlus" />
                  </svg>
                  <span>Joined: {{ formatDate(profile.joinDate) }}</span>
                </div>
              </div>
            </div>
          </div>

          <!-- Quick Actions -->
          <div class="mt-6 bg-white rounded-xl shadow-card p-6">
            <h4 class="text-lg font-semibold text-gray-900 mb-4">Quick Actions</h4>
            <div class="space-y-2">
              <button @click="showChangePasswordModal = true" class="w-full flex items-center px-3 py-2 text-sm text-gray-700 hover:bg-gray-50 rounded-lg transition-colors">
                <svg class="w-4 h-4 mr-3" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiKey" />
                </svg>
                Change Password
              </button>
              <button class="w-full flex items-center px-3 py-2 text-sm text-gray-700 hover:bg-gray-50 rounded-lg transition-colors">
                <svg class="w-4 h-4 mr-3" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiShieldCheck" />
                </svg>
                Two-Factor Authentication
              </button>
              <button class="w-full flex items-center px-3 py-2 text-sm text-gray-700 hover:bg-gray-50 rounded-lg transition-colors">
                <svg class="w-4 h-4 mr-3" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiDownload" />
                </svg>
                Download Activity Report
              </button>
            </div>
          </div>
        </div>

        <!-- Profile Details -->
        <div class="lg:col-span-2 space-y-6">
          <!-- Personal Information -->
          <div class="bg-white rounded-xl shadow-card p-6">
            <h3 class="text-lg font-semibold text-gray-900 mb-6">Personal Information</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Full Name</label>
                <input
                  v-if="editMode"
                  type="text"
                  v-model="profile.name"
                  class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                />
                <p v-else class="text-gray-900">{{ profile.name }}</p>
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Email Address</label>
                <input
                  v-if="editMode"
                  type="email"
                  v-model="profile.email"
                  class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                />
                <p v-else class="text-gray-900">{{ profile.email }}</p>
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Phone Number</label>
                <input
                  v-if="editMode"
                  type="tel"
                  v-model="profile.phone"
                  class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                />
                <p v-else class="text-gray-900">{{ profile.phone }}</p>
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Role</label>
                <p class="text-gray-900 flex items-center">
                  <span :class="getRoleClass(profile.roleKey)" class="px-2 py-1 text-xs font-medium rounded-full mr-2">
                    {{ profile.role }}
                  </span>
                </p>
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Department</label>
                <input
                  v-if="editMode"
                  type="text"
                  v-model="profile.department"
                  class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                />
                <p v-else class="text-gray-900">{{ profile.department }}</p>
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Employee ID</label>
                <p class="text-gray-900 font-mono">{{ profile.employeeId }}</p>
              </div>
            </div>
          </div>

          <!-- Security Settings -->
          <div class="bg-white rounded-xl shadow-card p-6">
            <h3 class="text-lg font-semibold text-gray-900 mb-6">Security Settings</h3>
            <div class="space-y-6">
              <div class="flex items-center justify-between p-4 border border-gray-200 rounded-lg">
                <div>
                  <h4 class="text-sm font-medium text-gray-900">Two-Factor Authentication</h4>
                  <p class="text-sm text-gray-500">Add an extra layer of security to your account</p>
                </div>
                <div class="flex items-center">
                  <span :class="profile.security.twoFactorEnabled ? 'text-green-600' : 'text-gray-500'" class="text-sm font-medium mr-3">
                    {{ profile.security.twoFactorEnabled ? 'Enabled' : 'Disabled' }}
                  </span>
                  <button 
                    @click="toggle2FA"
                    :class="profile.security.twoFactorEnabled ? 'bg-green-600 hover:bg-green-700' : 'bg-gray-200 hover:bg-gray-300'"
                    class="relative inline-flex h-6 w-11 items-center rounded-full transition-colors"
                  >
                    <span 
                      :class="profile.security.twoFactorEnabled ? 'translate-x-6' : 'translate-x-1'"
                      class="inline-block h-4 w-4 transform rounded-full bg-white transition-transform"
                    />
                  </button>
                </div>
              </div>

              <div class="flex items-center justify-between p-4 border border-gray-200 rounded-lg">
                <div>
                  <h4 class="text-sm font-medium text-gray-900">Email Notifications</h4>
                  <p class="text-sm text-gray-500">Receive email notifications for important events</p>
                </div>
                <div class="flex items-center">
                  <span :class="profile.security.emailNotifications ? 'text-green-600' : 'text-gray-500'" class="text-sm font-medium mr-3">
                    {{ profile.security.emailNotifications ? 'Enabled' : 'Disabled' }}
                  </span>
                  <button 
                    @click="toggleEmailNotifications"
                    :class="profile.security.emailNotifications ? 'bg-green-600 hover:bg-green-700' : 'bg-gray-200 hover:bg-gray-300'"
                    class="relative inline-flex h-6 w-11 items-center rounded-full transition-colors"
                  >
                    <span 
                      :class="profile.security.emailNotifications ? 'translate-x-6' : 'translate-x-1'"
                      class="inline-block h-4 w-4 transform rounded-full bg-white transition-transform"
                    />
                  </button>
                </div>
              </div>

              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Last Password Change</label>
                  <p class="text-gray-900">{{ formatDate(profile.security.lastPasswordChange) }}</p>
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Active Sessions</label>
                  <p class="text-gray-900">{{ profile.security.activeSessions }} session(s)</p>
                </div>
              </div>
            </div>
          </div>

          <!-- Activity Summary -->
          <div class="bg-white rounded-xl shadow-card p-6">
            <h3 class="text-lg font-semibold text-gray-900 mb-6">Recent Activity</h3>
            <div class="space-y-4">
              <div v-for="activity in recentActivities" :key="activity.id" class="flex items-start space-x-3">
                <div class="flex-shrink-0">
                  <div :class="getActivityIconClass(activity.type)" class="w-8 h-8 rounded-full flex items-center justify-center">
                    <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                      <path :d="getActivityIcon(activity.type)" />
                    </svg>
                  </div>
                </div>
                <div class="flex-1 min-w-0">
                  <p class="text-sm font-medium text-gray-900">{{ activity.action }}</p>
                  <p class="text-sm text-gray-500">{{ activity.description }}</p>
                  <p class="text-xs text-gray-400 mt-1">{{ formatDateTime(activity.timestamp) }}</p>
                </div>
              </div>
            </div>
          </div>

          <!-- Permissions -->
          <div class="bg-white rounded-xl shadow-card p-6">
            <h3 class="text-lg font-semibold text-gray-900 mb-6">Permissions & Access</h3>
            <div class="grid grid-cols-2 md:grid-cols-3 gap-3">
              <div 
                v-for="permission in profile.permissions" 
                :key="permission"
                class="flex items-center px-3 py-2 bg-gray-50 rounded-lg"
              >
                <svg class="w-4 h-4 text-green-600 mr-2" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiCheck" />
                </svg>
                <span class="text-sm text-gray-700">{{ permission }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Change Password Modal -->
      <div v-if="showChangePasswordModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
        <div class="bg-white rounded-xl shadow-xl max-w-md w-full">
          <div class="p-6 border-b border-gray-200">
            <div class="flex items-center justify-between">
              <h2 class="text-xl font-semibold text-gray-900">Change Password</h2>
              <button @click="closeChangePasswordModal" class="text-gray-400 hover:text-gray-600">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                </svg>
              </button>
            </div>
          </div>

          <form @submit.prevent="changePassword" class="p-6 space-y-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Current Password</label>
              <div class="relative">
                <input
                  :type="showCurrentPassword ? 'text' : 'password'"
                  v-model="passwordForm.currentPassword"
                  required
                  class="w-full border border-gray-300 rounded-lg px-3 py-2 pr-10 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                  placeholder="Enter current password"
                />
                <button
                  type="button"
                  @click="showCurrentPassword = !showCurrentPassword"
                  class="absolute inset-y-0 right-0 pr-3 flex items-center"
                >
                  <svg class="w-5 h-5 text-gray-400" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="showCurrentPassword ? mdiEyeOff : mdiEye" />
                  </svg>
                </button>
              </div>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">New Password</label>
              <div class="relative">
                <input
                  :type="showNewPassword ? 'text' : 'password'"
                  v-model="passwordForm.newPassword"
                  required
                  minlength="8"
                  class="w-full border border-gray-300 rounded-lg px-3 py-2 pr-10 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                  placeholder="Enter new password"
                />
                <button
                  type="button"
                  @click="showNewPassword = !showNewPassword"
                  class="absolute inset-y-0 right-0 pr-3 flex items-center"
                >
                  <svg class="w-5 h-5 text-gray-400" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="showNewPassword ? mdiEyeOff : mdiEye" />
                  </svg>
                </button>
              </div>
              <div class="mt-1">
                <div class="flex items-center space-x-1 text-xs">
                  <div :class="passwordStrength.length ? 'text-green-600' : 'text-gray-400'" class="flex items-center">
                    <svg class="w-3 h-3 mr-1" fill="currentColor" viewBox="0 0 24 24">
                      <path :d="passwordStrength.length ? mdiCheck : mdiClose" />
                    </svg>
                    <span>8+ characters</span>
                  </div>
                  <div :class="passwordStrength.uppercase ? 'text-green-600' : 'text-gray-400'" class="flex items-center">
                    <svg class="w-3 h-3 mr-1" fill="currentColor" viewBox="0 0 24 24">
                      <path :d="passwordStrength.uppercase ? mdiCheck : mdiClose" />
                    </svg>
                    <span>Uppercase</span>
                  </div>
                  <div :class="passwordStrength.number ? 'text-green-600' : 'text-gray-400'" class="flex items-center">
                    <svg class="w-3 h-3 mr-1" fill="currentColor" viewBox="0 0 24 24">
                      <path :d="passwordStrength.number ? mdiCheck : mdiClose" />
                    </svg>
                    <span>Number</span>
                  </div>
                </div>
              </div>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Confirm New Password</label>
              <div class="relative">
                <input
                  :type="showConfirmPassword ? 'text' : 'password'"
                  v-model="passwordForm.confirmPassword"
                  required
                  class="w-full border border-gray-300 rounded-lg px-3 py-2 pr-10 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-black"
                  placeholder="Confirm new password"
                />
                <button
                  type="button"
                  @click="showConfirmPassword = !showConfirmPassword"
                  class="absolute inset-y-0 right-0 pr-3 flex items-center"
                >
                  <svg class="w-5 h-5 text-gray-400" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="showConfirmPassword ? mdiEyeOff : mdiEye" />
                  </svg>
                </button>
              </div>
              <p v-if="passwordForm.confirmPassword && !passwordsMatch" class="mt-1 text-sm text-red-600">
                Passwords do not match
              </p>
            </div>

            <div class="flex justify-end space-x-3 pt-4">
              <button
                type="button"
                @click="closeChangePasswordModal"
                class="px-4 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors"
              >
                Cancel
              </button>
              <button
                type="submit"
                :disabled="!isPasswordFormValid"
                class="px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors disabled:opacity-50 disabled:cursor-not-allowed"
              >
                Change Password
              </button>
            </div>
          </form>
        </div>
      </div>

      <!-- Save Confirmation -->
      <div v-if="showSaveMessage" class="fixed bottom-4 right-4 bg-green-500 text-white px-6 py-3 rounded-lg shadow-lg flex items-center space-x-2">
        <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
          <path :d="mdiCheck" />
        </svg>
        <span>Profile updated successfully!</span>
      </div>

      <!-- Password Change Success -->
      <div v-if="showPasswordChangeSuccess" class="fixed bottom-4 right-4 bg-green-500 text-white px-6 py-3 rounded-lg shadow-lg flex items-center space-x-2">
        <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
          <path :d="mdiCheck" />
        </svg>
        <span>Password changed successfully!</span>
      </div>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import {
  mdiPencil,
  mdiClose,
  mdiContentSave,
  mdiCamera,
  mdiKey,
  mdiShieldCheck,
  mdiDownload,
  mdiClockOutline,
  mdiCalendarPlus,
  mdiCheck,
  mdiLogin,
  mdiCog,
  mdiAccountEdit,
  mdiSecurity,
  mdiEye,
  mdiEyeOff
} from '@mdi/js'

// State
const editMode = ref(false)
const showSaveMessage = ref(false)
const showChangePasswordModal = ref(false)
const showPasswordChangeSuccess = ref(false)
const showCurrentPassword = ref(false)
const showNewPassword = ref(false)
const showConfirmPassword = ref(false)

// Password form
const passwordForm = ref({
  currentPassword: '',
  newPassword: '',
  confirmPassword: ''
})

// Profile data
const profile = ref({
  name: 'John Administrator',
  email: 'john.admin@cowork.com',
  phone: '+1 (555) 123-4567',
  role: 'Super Administrator',
  roleKey: 'super-admin',
  department: 'IT Administration',
  employeeId: 'EMP-001',
  avatar: 'https://images.unsplash.com/photo-1472099645785-5658abf4ff4e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80',
  joinDate: '2023-01-15T09:00:00Z',
  lastLogin: '2024-08-15T14:30:00Z',
  stats: {
    totalLogins: 847,
    daysSinceJoin: 213
  },
  security: {
    twoFactorEnabled: true,
    emailNotifications: true,
    lastPasswordChange: '2024-07-01T10:00:00Z',
    activeSessions: 2
  },
  permissions: [
    'Dashboard Access',
    'User Management',
    'Company Management',
    'Booking Management',
    'Customer Management',
    'Facility Management',
    'Product Management',
    'Location Management',
    'Payment Management',
    'Reports Access',
    'Activity Logs',
    'System Settings',
    'Bulk Operations',
    'Export Data',
    'Audit Trail'
  ]
})

// Recent activities
const recentActivities = ref([
  {
    id: 1,
    type: 'login',
    action: 'Logged in',
    description: 'Successful login from Chrome browser',
    timestamp: '2024-08-15T14:30:00Z'
  },
  {
    id: 2,
    type: 'settings',
    action: 'Updated system settings',
    description: 'Modified notification preferences',
    timestamp: '2024-08-15T11:15:00Z'
  },
  {
    id: 3,
    type: 'user',
    action: 'Created new user',
    description: 'Added Emma Support to the system',
    timestamp: '2024-08-15T09:45:00Z'
  },
  {
    id: 4,
    type: 'security',
    action: 'Security review',
    description: 'Reviewed failed login attempts',
    timestamp: '2024-08-14T16:20:00Z'
  }
])

// Computed properties
const passwordStrength = computed(() => {
  const password = passwordForm.value.newPassword
  return {
    length: password.length >= 8,
    uppercase: /[A-Z]/.test(password),
    number: /[0-9]/.test(password)
  }
})

const passwordsMatch = computed(() => {
  return passwordForm.value.newPassword === passwordForm.value.confirmPassword
})

const isPasswordFormValid = computed(() => {
  return passwordForm.value.currentPassword &&
         passwordForm.value.newPassword &&
         passwordForm.value.confirmPassword &&
         passwordStrength.value.length &&
         passwordStrength.value.uppercase &&
         passwordStrength.value.number &&
         passwordsMatch.value
})

// Methods
const formatDate = (dateString: string) => {
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  })
}

const formatDateTime = (dateString: string) => {
  const date = new Date(dateString)
  return date.toLocaleString('en-US', {
    month: 'short',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}

const getRoleClass = (role: string) => {
  switch (role) {
    case 'super-admin':
      return 'bg-red-100 text-red-800'
    case 'admin':
      return 'bg-purple-100 text-purple-800'
    case 'manager':
      return 'bg-blue-100 text-blue-800'
    case 'operator':
      return 'bg-green-100 text-green-800'
    default:
      return 'bg-gray-100 text-gray-800'
  }
}

const getActivityIconClass = (type: string) => {
  switch (type) {
    case 'login':
      return 'bg-green-100 text-green-600'
    case 'settings':
      return 'bg-blue-100 text-blue-600'
    case 'user':
      return 'bg-purple-100 text-purple-600'
    case 'security':
      return 'bg-red-100 text-red-600'
    default:
      return 'bg-gray-100 text-gray-600'
  }
}

const getActivityIcon = (type: string) => {
  switch (type) {
    case 'login':
      return mdiLogin
    case 'settings':
      return mdiCog
    case 'user':
      return mdiAccountEdit
    case 'security':
      return mdiSecurity
    default:
      return mdiCheck
  }
}

const saveProfile = () => {
  // In a real app, this would save to backend
  console.log('Saving profile:', profile.value)
  
  editMode.value = false
  showSaveMessage.value = true
  setTimeout(() => {
    showSaveMessage.value = false
  }, 3000)
}

const changeAvatar = () => {
  // In a real app, this would open file picker
  alert('Avatar change functionality would be implemented here')
}

const toggle2FA = () => {
  profile.value.security.twoFactorEnabled = !profile.value.security.twoFactorEnabled
}

const toggleEmailNotifications = () => {
  profile.value.security.emailNotifications = !profile.value.security.emailNotifications
}

const closeChangePasswordModal = () => {
  showChangePasswordModal.value = false
  // Reset form
  passwordForm.value = {
    currentPassword: '',
    newPassword: '',
    confirmPassword: ''
  }
  // Reset password visibility
  showCurrentPassword.value = false
  showNewPassword.value = false
  showConfirmPassword.value = false
}

const changePassword = () => {
  // In a real app, this would validate current password and update with new one
  console.log('Changing password:', {
    currentPassword: passwordForm.value.currentPassword,
    newPassword: passwordForm.value.newPassword
  })

  // Simulate API call
  setTimeout(() => {
    // Update last password change date
    profile.value.security.lastPasswordChange = new Date().toISOString()

    // Close modal
    closeChangePasswordModal()

    // Show success message
    showPasswordChangeSuccess.value = true
    setTimeout(() => {
      showPasswordChangeSuccess.value = false
    }, 3000)
  }, 500)
}
</script>
