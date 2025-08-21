<template>
  <AdminLayout>
    <div class="space-y-6" v-if="subscription">
      <!-- Back Button -->
      <div class="flex items-center">
        <router-link to="/bookings?tab=subscriptions" class="flex items-center text-gray-600 hover:text-gray-900">
          <svg class="w-5 h-5 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
          </svg>
          Back to Bookings
        </router-link>
      </div>

      <!-- Subscription Header -->
      <div class="bg-white rounded-xl shadow-card p-6">
        <div class="flex items-center justify-between">
          <div>
            <h1 class="text-2xl font-bold text-gray-900">Subscription Details – {{ subscription.id }}</h1>
            <p class="text-gray-600 mt-1">{{ subscription.productName }} • {{ formatSubscriptionType(subscription.subscriptionType) }}</p>
          </div>
          <div class="flex items-center space-x-4">
            <span :class="getStatusClass(subscription.status)" class="px-3 py-1 text-sm font-medium rounded-full">
              {{ subscription.status }}
            </span>
            <button 
              v-if="subscription.status !== 'cancelled'"
              @click="confirmCancelSubscription"
              class="px-4 py-2 bg-orange-600 text-white rounded-lg hover:bg-orange-700 transition-colors"
            >
              Cancel Subscription
            </button>
          </div>
        </div>
      </div>

      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <!-- Customer Information -->
        <div class="bg-white rounded-xl shadow-card p-6">
          <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
            <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiAccount" />
            </svg>
            Customer Information
          </h2>
          <div class="space-y-4">
            <div class="flex items-center space-x-3">
              <div class="w-12 h-12 bg-primary-100 rounded-full flex items-center justify-center">
                <svg class="w-6 h-6 text-primary-600" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiAccount" />
                </svg>
              </div>
              <div>
                <h3 class="text-lg font-semibold text-gray-900">{{ subscription.customerName }}</h3>
                <p class="text-sm text-gray-500">{{ subscription.customerEmail }}</p>
                <div v-if="getCustomerDetails(subscription).isRegistered" class="flex items-center space-x-2 mt-1">
                  <span class="text-xs text-blue-600 font-medium">ID: {{ getCustomerDetails(subscription).customerId }}</span>
                  <span class="text-xs text-gray-400">•</span>
                  <span class="text-xs text-gray-600">{{ getCustomerDetails(subscription).totalBookings }} total bookings</span>
                </div>
              </div>
            </div>
            <div class="grid grid-cols-2 gap-4 pt-4 border-t border-gray-200">
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Phone</label>
                <p class="text-sm text-gray-900">{{ subscription.customerPhone }}</p>
              </div>
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">User Type</label>
                <div class="flex items-center space-x-2 mt-1">
                  <span :class="subscription.userType === 'registered' ? 'bg-blue-100 text-blue-800' : 'bg-gray-100 text-gray-800'" 
                        class="inline-block px-2 py-1 text-xs font-medium rounded-full">
                    {{ subscription.userType }}
                  </span>
                  <span v-if="getCustomerDetails(subscription).isRegistered"
                        :class="getCustomerStatusClass(getCustomerDetails(subscription).customerStatus || 'inactive')"
                        class="inline-block px-2 py-1 text-xs font-medium rounded-full">
                    {{ getCustomerDetails(subscription).customerStatus || 'inactive' }}
                  </span>
                </div>
              </div>
            </div>
           
            <div class="pt-4 border-t border-gray-200">
              <div class="flex items-center space-x-3">
                <button v-if="getCustomerDetails(subscription).isRegistered" 
                        @click="viewCustomerProfile" 
                        class="px-4 py-2 bg-blue-600 text-white text-sm font-medium rounded-lg hover:bg-blue-700 transition-colors flex items-center">
                  <svg class="w-4 h-4 mr-2" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiEye" />
                  </svg>
                  View Profile
                </button>
                <button v-if="getCustomerDetails(subscription).isRegistered" 
                        @click="openSendMessageModal" 
                        class="px-4 py-2 bg-green-600 text-white text-sm font-medium rounded-lg hover:bg-green-700 transition-colors flex items-center">
                  <svg class="w-4 h-4 mr-1" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiMessage" />
                  </svg>
                  Send Message
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Product Information -->
        <div class="bg-white rounded-xl shadow-card p-6">
          <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
            <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiOfficeBuilding" />
            </svg>
            Product Information
          </h2>
          <div class="space-y-4">
            <div class="flex items-center space-x-4">
              <img class="w-16 h-16 rounded-lg object-cover" :src="subscription.productImage" :alt="subscription.productName">
              <div>
                <h3 class="text-sm font-medium text-gray-900">{{ subscription.productName }}</h3>
                <p class="text-sm text-gray-500">{{ formatSubscriptionType(subscription.subscriptionType) }}</p>
                <p class="text-sm text-gray-500">{{ subscription.locationName }}</p>
              </div>
            </div>
            <div class="grid grid-cols-2 gap-4 pt-4 border-t border-gray-200">
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Capacity</label>
                <p class="text-sm text-gray-900">{{ subscription.capacity }} {{ subscription.capacity === 1 ? 'person' : 'people' }}</p>
              </div>
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Facilities</label>
                <div class="flex flex-wrap gap-1 mt-1">
                  <span v-for="facility in subscription.facilities" :key="facility" 
                        class="px-2 py-1 text-xs bg-gray-100 text-gray-700 rounded">
                    {{ facility }}
                  </span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Subscription Details -->
      <div class="bg-white rounded-xl shadow-card p-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
            <path :d="mdiCalendarClock" />
          </svg>
          Subscription Details
        </h2>
        
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="text-center p-4 bg-blue-50 rounded-lg">
            <label class="text-xs font-medium text-blue-600 uppercase tracking-wider">Subscribed Date</label>
            <p class="text-lg font-semibold text-blue-900 mt-1">{{ formatDate(subscription.subscribedDate) }}</p>
            <span class="text-xs text-blue-600 mt-1 block">Start Date</span>
          </div>
          <div class="text-center p-4 bg-green-50 rounded-lg">
            <label class="text-xs font-medium text-green-600 uppercase tracking-wider">Billing Period</label>
            <p class="text-lg font-semibold text-green-900 mt-1 capitalize">{{ formatSubscriptionType(subscription.subscriptionType) }}</p>
            <span class="text-xs text-green-600 mt-1 block">Renewal Type</span>
          </div>
          <div class="text-center p-4 bg-purple-50 rounded-lg">
            <label class="text-xs font-medium text-purple-600 uppercase tracking-wider">Next Billing</label>
            <p class="text-lg font-semibold text-purple-900 mt-1">{{ formatDate(subscription.nextBillingDate) }}</p>
            <span class="text-xs text-purple-600 mt-1 block">Auto Renewal</span>
          </div>
        </div>
      </div>

      <!-- Price Breakdown -->
      <div class="bg-white rounded-xl shadow-card p-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
            <path :d="mdiCurrencyUsd" />
          </svg>
          Price Breakdown
        </h2>
        <div class="space-y-3">
          <div class="flex justify-between">
            <span class="text-gray-600">Base Price ({{ formatSubscriptionType(subscription.subscriptionType) }})</span>
            <span class="text-gray-900">${{ subscription.basePrice }}</span>
          </div>
          <div class="flex justify-between">
            <span class="text-gray-600">Additional Facilities</span>
            <span class="text-gray-900">${{ subscription.additionalFacilities }}</span>
          </div>
          <div class="border-t border-gray-200 pt-3">
            <div class="flex justify-between">
              <span class="text-lg font-semibold text-gray-900">Total ({{ formatSubscriptionType(subscription.subscriptionType) }})</span>
              <span class="text-lg font-bold text-primary-600">${{ subscription.totalPrice }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Loading/Not Found state -->
    <div v-else class="flex items-center justify-center h-64">
      <div class="text-center">
        <svg class="w-16 h-16 text-gray-400 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
        </svg>
        <h3 class="text-lg font-medium text-gray-900 mb-2">Subscription Not Found</h3>
        <p class="text-gray-600 mb-4">The subscription with ID "{{ route.params.id }}" could not be found.</p>
        <router-link to="/bookings?tab=subscriptions" class="inline-flex items-center px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors">
          <svg class="w-4 h-4 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
          </svg>
          Back to Bookings
        </router-link>
      </div>
    </div>

    

    <!-- Send Message Modal -->
    <div v-if="showMessageModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50" @click="closeMessageModal">
      <div class="relative top-10 mx-auto p-6 border w-full max-w-2xl shadow-lg rounded-lg bg-white" @click.stop>
        <div class="mt-3">
          <div class="flex items-center justify-center mx-auto w-12 h-12 rounded-full bg-blue-100">
            <svg class="w-6 h-6 text-blue-600" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiMessage" />
            </svg>
          </div>
          <h3 class="text-lg font-medium text-gray-900 text-center mt-4">Send Message to Customer</h3>
          <div class="mt-2 px-4 py-3">
            <div v-if="subscription" class="mb-4 p-3 bg-gray-50 rounded-lg">
              <div class="text-sm text-gray-900">
                <div class="flex items-center space-x-2 mb-2">
                  <div class="w-8 h-8 bg-primary-100 rounded-full flex items-center justify-center">
                    <svg class="w-4 h-4 text-primary-600" fill="currentColor" viewBox="0 0 24 24">
                      <path :d="mdiAccount" />
                    </svg>
                  </div>
                  <div>
                    <div class="font-medium">{{ subscription.customerName }}</div>
                    <div class="text-xs text-gray-500">{{ subscription.customerEmail }}</div>
                  </div>
                </div>
                <div class="text-xs text-gray-600">
                  <span class="font-medium">Subscription:</span> {{ subscription.productName }} - {{ formatSubscriptionType(subscription.subscriptionType) }}
                </div>
              </div>
            </div>
            
            <form @submit.prevent="sendMessage">
              <div class="mb-4">
                <label for="messageSubject" class="block text-sm font-medium text-gray-900 mb-2">Subject</label>
                <input
                  id="messageSubject"
                  v-model="messageForm.subject"
                  type="text"
                  required
                  class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-primary-500 focus:border-primary-500 text-gray-900"
                  placeholder="Enter message subject"
                />
              </div>
              <div class="mb-4">
                <label for="messageBody" class="block text-sm font-medium text-gray-700 mb-2">Message</label>
                <textarea
                  id="messageBody"
                  v-model="messageForm.message"
                  rows="4"
                  required
                  class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-primary-500 focus:border-primary-500 text-gray-900"
                  placeholder="Enter your message to the customer..."
                ></textarea>
              </div>
              <div class="mb-4">
                <label for="contactMethod" class="block text-sm font-medium text-gray-700 mb-2">Send via</label>
                <div class="space-y-3">
                  <label class="flex items-center">
                    <input
                      v-model="messageForm.sendVia"
                      type="radio"
                      value="email"
                      class="h-4 w-4 text-primary-600 focus:ring-primary-500 border-gray-300"
                    />
                    <span class="ml-2 text-sm text-gray-700">Email</span>
                  </label>
                  <label class="flex items-center">
                    <input
                      v-model="messageForm.sendVia"
                      type="radio"
                      value="phone"
                      class="h-4 w-4 text-primary-600 focus:ring-primary-500 border-gray-300"
                    />
                    <span class="ml-2 text-sm text-gray-700">Phone Number</span>
                  </label>
                </div>
              </div>
              <div v-if="messageForm.sendVia === 'email'" class="mb-4">
                <label for="recipientEmail" class="block text-sm font-medium text-gray-700 mb-2">Email Address</label>
                <input
                  id="recipientEmail"
                  v-model="messageForm.recipientEmail"
                  type="email"
                  :placeholder="subscription?.customerEmail || 'Enter email'"
                  class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-primary-500 focus:border-primary-500 text-gray-900"
                />
              </div>
              <div v-if="messageForm.sendVia === 'phone'" class="mb-4">
                <label for="recipientPhone" class="block text-sm font-medium text-gray-700 mb-2">Phone Number</label>
                <input
                  id="recipientPhone"
                  v-model="messageForm.recipientPhone"
                  type="tel"
                  :placeholder="subscription?.customerPhone || 'Enter phone number'"
                  class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-primary-500 focus:border-primary-500 text-gray-900"
                />
              </div>
            </form>
          </div>
          <div class="flex items-center justify-center pt-4 space-x-4">
            <button
              @click="closeMessageModal"
              type="button"
              class="px-4 py-2 bg-gray-200 text-gray-800 text-sm font-medium rounded-md hover:bg-gray-300 transition-colors"
            >
              Cancel
            </button>
            <button
              @click="sendMessage"
              :disabled="isSendingMessage || !messageForm.subject.trim() || !messageForm.message.trim()"
              class="px-4 py-2 bg-blue-600 text-white text-sm font-medium rounded-md hover:bg-blue-700 transition-colors disabled:opacity-50 disabled:cursor-not-allowed flex items-center space-x-2"
            >
              <svg v-if="isSendingMessage" class="animate-spin -ml-1 mr-2 h-4 w-4 text-white" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              <span>{{ isSendingMessage ? 'Sending...' : 'Send Message' }}</span>
            </button>
          </div>
        </div>
      </div>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { useCustomers } from '@/composables/useCustomers'
import { mdiAccount, mdiOfficeBuilding, mdiCalendarClock, mdiCurrencyUsd, mdiCancel, mdiEye, mdiEmail, mdiMessage } from '@mdi/js'

const route = useRoute()
const router = useRouter()

const { customers } = useCustomers()

// State
const showCancelModal = ref(false)
const showMessageModal = ref(false)
const isCancelling = ref(false)
const isSendingMessage = ref(false)

// Message modal state
const messageForm = ref({
  subject: '',
  message: '',
  sendVia: 'email', // 'email' or 'phone'
  priority: 'normal', // 'low', 'normal', 'high'
  recipientEmail: '',
  recipientPhone: ''
})

// Refund and cancellation state
const refundOptions = ref({
  isRefundApplicable: false,
  refundAmount: 0,
  processingMethod: 'original-payment',
  adminNotes: ''
})

const cancellationReason = ref({
  customMessage: '',
  sendViaEmail: true,
  sendViaSMS: false,
  customerEmail: '',
  customerPhone: ''
})

// Sample subscription data
const allSubscriptions = ref([
  {
    id: 'SUB-3001',
    productName: 'Dedicated Desk',
    productType: 'Subscription',
    productId: 'SUB001',
    productImage: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=200&h=200&fit=crop&crop=center',
    customerName: 'John Doe',
    customerEmail: 'john.doe@example.com',
    customerPhone: '+1 (555) 123-4567',
    userType: 'registered',
    subscriptionType: 'monthly',
    subscribedDate: '2025-08-01',
    nextBillingDate: '2025-09-01',
    totalPrice: 800,
    basePrice: 750,
    additionalFacilities: 50,
    status: 'confirmed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.',
    capacity: 1,
    facilities: ['WiFi', 'Private Storage', 'Ergonomic Chair', 'Desk Lamp', 'Personal Phone Line'],
    customerMessage: 'Looking forward to using the dedicated desk space for my consulting work.'
  },
  {
    id: 'SUB-3003',
    productName: 'Dedicated Desk',
    productType: 'Subscription',
    productId: 'SUB003',
    productImage: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=200&h=200&fit=crop&crop=center',
    customerName: 'Sarah Wilson',
    customerEmail: 'sarah.wilson@example.com',
    customerPhone: '+1 (555) 234-5678',
    userType: 'registered',
    subscriptionType: 'annually',
    subscribedDate: '2025-01-01',
    nextBillingDate: '2026-01-01',
    totalPrice: 8000,
    basePrice: 7500,
    additionalFacilities: 500,
    status: 'confirmed',
    location: 'business-center',
    locationName: 'Business Center',
    companyName: 'Global Solutions Inc.',
    capacity: 1,
    facilities: ['WiFi', 'Private Storage', 'Ergonomic Chair', 'Desk Lamp', 'Personal Phone Line', '24/7 Access'],
    customerMessage: ''
  }
])

// Find subscription by route param
const subscription = computed(() => {
  const id = route.params.id as string
  return allSubscriptions.value.find(sub => sub.id === id)
})

// Customer lookup function
const getCustomerDetails = (subscription: any) => {
  if (!subscription) return { isRegistered: false, customerData: null, customerId: null }
  
  if (subscription.userType === 'registered') {
    const customerData = customers.value.find(customer => 
      customer.email.toLowerCase() === subscription.customerEmail?.toLowerCase()
    )
    
    if (customerData) {
      return {
        isRegistered: true,
        customerData,
        customerId: customerData.id,
        displayName: customerData.name,
        totalBookings: customerData.totalBookings,
        memberSince: customerData.dateJoined,
        customerStatus: customerData.status
      }
    }
  }
  
  return {
    isRegistered: false,
    customerData: null,
    customerId: null,
    displayName: subscription.customerName,
    totalBookings: 0,
    memberSince: null,
    customerStatus: 'guest'
  }
}

// Methods
const getStatusClass = (status: string) => {
  switch (status) {
    case 'confirmed':
      return 'bg-green-100 text-green-800'
    case 'completed':
      return 'bg-gray-100 text-gray-800'
    case 'cancelled':
      return 'bg-red-100 text-red-800'
    default:
      return 'bg-gray-100 text-gray-800'
  }
}

const getCustomerStatusClass = (status: string) => {
  switch (status) {
    case 'active':
      return 'bg-green-100 text-green-800'
    case 'blocked':
      return 'bg-red-100 text-red-800'
    case 'inactive':
      return 'bg-gray-100 text-gray-800'
    default:
      return 'bg-gray-100 text-gray-800'
  }
}

const formatDate = (dateString: string) => {
  if (!dateString) return ''
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  })
}

const formatSubscriptionType = (type: string) => {
  return type.charAt(0).toUpperCase() + type.slice(1)
}

const viewCustomerProfile = () => {
  if (subscription.value && getCustomerDetails(subscription.value).isRegistered) {
    const customerId = getCustomerDetails(subscription.value).customerId
    router.push(`/customers/${customerId}`)
  }
}

const openSendMessageModal = () => {
  showMessageModal.value = true
  // Pre-fill customer information if available
  if (subscription.value) {
    messageForm.value.subject = `Regarding your subscription ${subscription.value.id}`
    messageForm.value.recipientEmail = subscription.value.customerEmail
    messageForm.value.recipientPhone = subscription.value.customerPhone
  }
}

const closeMessageModal = () => {
  showMessageModal.value = false
  messageForm.value.subject = ''
  messageForm.value.message = ''
  messageForm.value.sendVia = 'email'
  messageForm.value.priority = 'normal'
  messageForm.value.recipientEmail = ''
  messageForm.value.recipientPhone = ''
}

const sendMessage = async () => {
  if (!subscription.value || !messageForm.value.message.trim() || !messageForm.value.subject.trim()) return
  
  isSendingMessage.value = true
  
  try {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    // Create message log
    const messageLog = {
      subscriptionId: subscription.value.id,
      customerName: subscription.value.customerName,
      customerEmail: subscription.value.customerEmail,
      customerPhone: subscription.value.customerPhone,
      subject: messageForm.value.subject,
      message: messageForm.value.message,
      sentVia: messageForm.value.sendVia,
      recipientEmail: messageForm.value.recipientEmail,
      recipientPhone: messageForm.value.recipientPhone,
      priority: messageForm.value.priority,
      sentAt: new Date().toISOString(),
      sentBy: 'Admin'
    }
    
    console.log('Message sent:', messageLog)
    
    // Store message log
    const messageLogs = JSON.parse(localStorage.getItem('customerMessages') || '[]')
    messageLogs.push(messageLog)
    localStorage.setItem('customerMessages', JSON.stringify(messageLogs))
    
    closeMessageModal()
    alert(`Message sent successfully via ${messageForm.value.sendVia}!`)
    
  } catch (error) {
    console.error('Error sending message:', error)
    alert('Error sending message. Please try again.')
  } finally {
    isSendingMessage.value = false
  }
}

const confirmCancelSubscription = () => {
  if (subscription.value) {
    // Navigate to the cancel subscription page
    router.push(`/subscriptions/${subscription.value.id}/cancel`)
  }
}

const closeCancelModal = () => {
  showCancelModal.value = false
  refundOptions.value.isRefundApplicable = false
  refundOptions.value.refundAmount = 0
  cancellationReason.value.customMessage = ''
}

const cancelSubscription = async () => {
  if (!subscription.value) return
  
  isCancelling.value = true
  
  try {
    // Simulate API call delay
    await new Promise(resolve => setTimeout(resolve, 2000))
    
    // Update subscription status in data
    const subIndex = allSubscriptions.value.findIndex(sub => sub.id === subscription.value?.id)
    if (subIndex !== -1) {
      allSubscriptions.value[subIndex].status = 'cancelled'
    }
    
    // Log cancellation details
    const cancellationLog = {
      subscriptionId: subscription.value.id,
      customerName: subscription.value.customerName,
      cancelledAt: new Date().toISOString(),
      reason: cancellationReason.value.customMessage,
      refundProcessed: refundOptions.value.isRefundApplicable,
      refundAmount: refundOptions.value.isRefundApplicable ? refundOptions.value.refundAmount : 0,
      cancelledBy: 'Admin'
    }
    
    console.log('Subscription cancelled:', cancellationLog)
    
    // Store cancellation log
    const cancellationLogs = JSON.parse(localStorage.getItem('subscriptionCancellationLogs') || '[]')
    cancellationLogs.push(cancellationLog)
    localStorage.setItem('subscriptionCancellationLogs', JSON.stringify(cancellationLogs))
    
    closeCancelModal()
    
    // Show success message (you can implement a toast notification here)
    alert('Subscription cancelled successfully!')
    
  } catch (error) {
    console.error('Error cancelling subscription:', error)
    alert('Error cancelling subscription. Please try again.')
  } finally {
    isCancelling.value = false
  }
}

// Initialize page data
onMounted(() => {
  // Load subscription data from localStorage (from BookingsView)
  const savedBookings = localStorage.getItem('allBookings')
  if (savedBookings) {
    try {
      const parsedBookings = JSON.parse(savedBookings)
      const subscriptions = parsedBookings.filter((booking: any) => booking.productType === 'Subscription')
      if (subscriptions.length > 0) {
        allSubscriptions.value = subscriptions
        console.log('Loaded subscriptions from localStorage:', subscriptions.length)
      }
    } catch (error) {
      console.warn('Error loading subscriptions from localStorage:', error)
    }
  }
  
  // Update subscription statuses from localStorage (simulate real-time updates)
  const bookingStatuses = JSON.parse(localStorage.getItem('bookingStatuses') || '{}')
  allSubscriptions.value.forEach(subscription => {
    if (bookingStatuses[subscription.id]) {
      subscription.status = bookingStatuses[subscription.id]
    }
  })
})
</script>

<style scoped>
.shadow-card {
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06);
}
</style>
