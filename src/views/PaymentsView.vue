<template>
  <AdminLayout>
    <div class="space-y-6">
      <!-- Page Header -->
      <div class="flex items-center justify-between">
        <div>
          <h1 class="text-2xl font-bold text-gray-900">Payments Management</h1>
          <p class="text-gray-600">Manage payments, commissions, and transaction records</p>
        </div>
        <div class="flex items-center space-x-3">
          <button
            @click="showCommissionModal = true"
            class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors flex items-center space-x-2"
          >
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiCog" />
            </svg>
            <span>Commission Setup</span>
          </button>
          <div class="text-sm text-gray-500">
            Total Payments: {{ filteredPayments.length }}
          </div>
        </div>
      </div>

      <!-- Commission Overview Cards -->
      <div class="grid grid-cols-1 md:grid-cols-4 gap-6">
        <div class="bg-white rounded-xl shadow-card p-6">
          <div class="flex items-center">
            <div class="flex-shrink-0">
              <div class="w-12 h-12 bg-green-100 rounded-lg flex items-center justify-center">
                <svg class="w-6 h-6 text-green-600" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiCurrencyUsd" />
                </svg>
              </div>
            </div>
            <div class="ml-4">
              <h3 class="text-sm font-medium text-gray-500">Total Revenue</h3>
              <p class="text-2xl font-bold text-gray-900">${{ paymentStats.totalRevenue.toLocaleString() }}</p>
            </div>
          </div>
        </div>

        <div class="bg-white rounded-xl shadow-card p-6">
          <div class="flex items-center">
            <div class="flex-shrink-0">
              <div class="w-12 h-12 bg-blue-100 rounded-lg flex items-center justify-center">
                <svg class="w-6 h-6 text-blue-600" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiPercent" />
                </svg>
              </div>
            </div>
            <div class="ml-4">
              <h3 class="text-sm font-medium text-gray-500">Commission Earned</h3>
              <p class="text-2xl font-bold text-gray-900">${{ paymentStats.commissionEarned.toLocaleString() }}</p>
            </div>
          </div>
        </div>

        <div class="bg-white rounded-xl shadow-card p-6">
          <div class="flex items-center">
            <div class="flex-shrink-0">
              <div class="w-12 h-12 bg-purple-100 rounded-lg flex items-center justify-center">
                <svg class="w-6 h-6 text-purple-600" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiCreditCard" />
                </svg>
              </div>
            </div>
            <div class="ml-4">
              <h3 class="text-sm font-medium text-gray-500">Transactions Today</h3>
              <p class="text-2xl font-bold text-gray-900">{{ paymentStats.transactionsToday }}</p>
            </div>
          </div>
        </div>

        <div class="bg-white rounded-xl shadow-card p-6">
          <div class="flex items-center">
            <div class="flex-shrink-0">
              <div class="w-12 h-12 bg-orange-100 rounded-lg flex items-center justify-center">
                <svg class="w-6 h-6 text-orange-600" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiTrendingUp" />
                </svg>
              </div>
            </div>
            <div class="ml-4">
              <h3 class="text-sm font-medium text-gray-500">Avg Transaction</h3>
              <p class="text-2xl font-bold text-gray-900">${{ paymentStats.avgTransaction }}</p>
            </div>
          </div>
        </div>
      </div>

      <!-- Filters -->
      <div class="bg-white rounded-xl shadow-card p-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4">Filters</h2>
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Date Range From</label>
            <input
              type="date"
              v-model="filters.dateFrom"
              class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent"
            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Date Range To</label>
            <input
              type="date"
              v-model="filters.dateTo"
              class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent"
            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Product</label>
            <select v-model="filters.product" class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent">
              <option value="">All Products</option>
              <option value="meeting-room">Meeting Room</option>
              <option value="hot-desk">Hot Desk</option>
              <option value="private-office">Private Office</option>
              <option value="event-space">Event Space</option>
            </select>
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Location</label>
            <select v-model="filters.location" class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent">
              <option value="">All Locations</option>
              <option value="main-branch">Main Branch - Downtown</option>
              <option value="tech-hub">Tech Hub - Silicon Valley</option>
              <option value="creative-quarter">Creative Quarter</option>
            </select>
          </div>
        </div>
        <div class="mt-4 flex justify-end">
          <button
            @click="resetFilters"
            class="px-4 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors"
          >
            Reset Filters
          </button>
        </div>
      </div>

      <!-- Payments Table -->
      <div class="bg-white rounded-xl shadow-card overflow-hidden">
        <div class="px-6 py-4 border-b border-gray-200">
          <h2 class="text-lg font-semibold text-gray-900">Payment List ({{ filteredPayments.length }})</h2>
        </div>
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Customer Name
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Amount
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Method
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Product
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Location
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Date
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr v-for="payment in filteredPayments" :key="payment.id" class="hover:bg-gray-50">
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="flex items-center">
                    <div class="flex-shrink-0 h-10 w-10">
                      <div class="h-10 w-10 rounded-full bg-primary-100 flex items-center justify-center">
                        <svg class="w-6 h-6 text-primary-600" fill="currentColor" viewBox="0 0 24 24">
                          <path :d="mdiAccount" />
                        </svg>
                      </div>
                    </div>
                    <div class="ml-4">
                      <div class="text-sm font-medium text-gray-900">{{ payment.customerName }}</div>
                      <div class="text-sm text-gray-500">{{ payment.customerEmail }}</div>
                    </div>
                  </div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm font-semibold text-gray-900">${{ payment.amount }}</div>
                  <div class="text-sm text-gray-500">{{ payment.paymentId }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="flex items-center">
                    <svg class="w-5 h-5 text-blue-600 mr-2" fill="currentColor" viewBox="0 0 24 24">
                      <path :d="mdiCreditCard" />
                    </svg>
                    <span class="text-sm text-gray-900">{{ payment.method }}</span>
                  </div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm text-gray-900">{{ payment.productName }}</div>
                  <div class="text-sm text-gray-500">{{ payment.productType }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm text-gray-900">{{ payment.locationName }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm text-gray-900">{{ formatDate(payment.date) }}</div>
                  <div class="text-sm text-gray-500">{{ payment.time }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                  <router-link :to="`/payments/${payment.id}`" class="text-primary-600 hover:text-primary-900 flex items-center space-x-1" title="View Payment Details">
                    <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                      <path :d="mdiEye" />
                    </svg>
                  </router-link>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Empty State -->
        <div v-if="filteredPayments.length === 0" class="text-center py-12">
          <svg class="mx-auto h-12 w-12 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 9V7a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2m2 4h10a2 2 0 002-2v-6a2 2 0 00-2-2H9a2 2 0 00-2 2v6a2 2 0 002 2zm7-5a2 2 0 11-4 0 2 2 0 014 0z" />
          </svg>
          <h3 class="mt-2 text-sm font-medium text-gray-900">No payments found</h3>
          <p class="mt-1 text-sm text-gray-500">No payments match the current filters.</p>
        </div>
      </div>
    </div>

    <!-- Commission Setup Modal -->
    <div v-if="showCommissionModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
      <div class="bg-white rounded-xl shadow-xl max-w-2xl w-full max-h-[90vh] overflow-y-auto">
        <div class="p-6 border-b border-gray-200">
          <div class="flex items-center justify-between">
            <h2 class="text-xl font-semibold text-gray-900">Commission Setup</h2>
            <button @click="showCommissionModal = false" class="text-gray-400 hover:text-gray-600">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          </div>
        </div>
        
        <form @submit.prevent="saveCommissionSettings" class="p-6 space-y-6">
          <div>
            <h3 class="text-lg font-medium text-gray-900 mb-4">Location-based Commission Rates</h3>
            
            <div v-for="location in commissionSettings" :key="location.id" class="border border-gray-200 rounded-lg p-4 mb-4">
              <div class="flex items-center justify-between mb-3">
                <h4 class="text-sm font-medium text-gray-900">{{ location.name }}</h4>
                <span class="text-sm text-gray-500">{{ location.company }}</span>
              </div>
              
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Commission Rate (%)</label>
                  <input
                    type="number"
                    v-model="location.commissionRate"
                    min="0"
                    max="100"
                    step="0.1"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent"
                    placeholder="0.0"
                  />
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Fixed Fee ($)</label>
                  <input
                    type="number"
                    v-model="location.fixedFee"
                    min="0"
                    step="0.01"
                    class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent"
                    placeholder="0.00"
                  />
                </div>
              </div>
              
              <div class="mt-3">
                <label class="flex items-center">
                  <input 
                    type="checkbox" 
                    v-model="location.isActive"
                    class="rounded border-gray-300 text-primary-600 focus:ring-primary-500"
                  />
                  <span class="ml-2 text-sm text-gray-700">Active commission for this location</span>
                </label>
              </div>
            </div>
          </div>

          <div class="border-t border-gray-200 pt-6">
            <h3 class="text-lg font-medium text-gray-900 mb-4">Global Settings</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Default Commission Rate (%)</label>
                <input
                  type="number"
                  v-model="globalSettings.defaultCommissionRate"
                  min="0"
                  max="100"
                  step="0.1"
                  class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent"
                />
              </div>
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Payment Processing Fee (%)</label>
                <input
                  type="number"
                  v-model="globalSettings.processingFee"
                  min="0"
                  max="10"
                  step="0.01"
                  class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent"
                />
              </div>
            </div>
          </div>

          <div class="flex justify-end space-x-4 pt-6 border-t border-gray-200">
            <button
              type="button"
              @click="showCommissionModal = false"
              class="px-4 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors"
            >
              Cancel
            </button>
            <button
              type="submit"
              class="px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors"
            >
              Save Settings
            </button>
          </div>
        </form>
      </div>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { 
  mdiCog, 
  mdiCurrencyUsd, 
  mdiPercent, 
  mdiCreditCard, 
  mdiTrendingUp, 
  mdiAccount, 
  mdiEye
} from '@mdi/js'

// State
const showCommissionModal = ref(false)

// Filters
const filters = ref({
  dateFrom: '',
  dateTo: '',
  product: '',
  location: ''
})

// Payment statistics
const paymentStats = ref({
  totalRevenue: 45750,
  commissionEarned: 4575,
  transactionsToday: 12,
  avgTransaction: 156
})

// Commission settings
const commissionSettings = ref([
  {
    id: 'LC-001',
    name: 'Main Branch - Downtown',
    company: 'WorkHub Co.',
    commissionRate: 10.0,
    fixedFee: 5.00,
    isActive: true
  },
  {
    id: 'LC-002',
    name: 'Tech Hub - Silicon Valley',
    company: 'FlexSpace Inc.',
    commissionRate: 12.5,
    fixedFee: 3.00,
    isActive: true
  },
  {
    id: 'LC-003',
    name: 'Creative Quarter',
    company: 'Creative Spaces LLC',
    commissionRate: 8.0,
    fixedFee: 7.50,
    isActive: false
  }
])

// Global settings
const globalSettings = ref({
  defaultCommissionRate: 10.0,
  processingFee: 2.9
})

// Sample payments data
const payments = ref([
  {
    id: 'PAY-001',
    paymentId: 'PM-2024-001',
    customerName: 'John Doe',
    customerEmail: 'john.doe@example.com',
    amount: 150,
    method: 'Credit Card',
    productName: 'Executive Board Room',
    productType: 'Meeting Room',
    locationName: 'Main Branch - Downtown',
    bookingId: 'BR-2034',
    date: '2024-08-15',
    time: '10:30 AM',
    transactionRef: 'TXN-789123456'
  },
  {
    id: 'PAY-002',
    paymentId: 'PM-2024-002',
    customerName: 'Jane Smith',
    customerEmail: 'jane.smith@example.com',
    amount: 60,
    method: 'Credit Card',
    productName: 'Hot Desk Area',
    productType: 'Hot Desk',
    locationName: 'Tech Hub - Silicon Valley',
    bookingId: 'BR-2033',
    date: '2024-08-15',
    time: '2:15 PM',
    transactionRef: 'TXN-789123457'
  },
  {
    id: 'PAY-003',
    paymentId: 'PM-2024-003',
    customerName: 'Mike Johnson',
    customerEmail: 'mike.johnson@example.com',
    amount: 400,
    method: 'Credit Card',
    productName: 'Private Office Suite',
    productType: 'Private Office',
    locationName: 'Main Branch - Downtown',
    bookingId: 'BR-2032',
    date: '2024-08-14',
    time: '9:45 AM',
    transactionRef: 'TXN-789123458'
  },
  {
    id: 'PAY-004',
    paymentId: 'PM-2024-004',
    customerName: 'Sarah Wilson',
    customerEmail: 'sarah.wilson@example.com',
    amount: 50,
    method: 'Credit Card',
    productName: 'Meeting Room Small',
    productType: 'Meeting Room',
    locationName: 'Creative Quarter',
    bookingId: 'BR-2031',
    date: '2024-08-14',
    time: '3:20 PM',
    transactionRef: 'TXN-789123459'
  },
  {
    id: 'PAY-005',
    paymentId: 'PM-2024-005',
    customerName: 'Robert Davis',
    customerEmail: 'robert.davis@example.com',
    amount: 180,
    method: 'Credit Card',
    productName: 'Conference Room',
    productType: 'Meeting Room',
    locationName: 'Tech Hub - Silicon Valley',
    bookingId: 'BR-2030',
    date: '2024-08-13',
    time: '11:00 AM',
    transactionRef: 'TXN-789123460'
  }
])

// Computed properties
const filteredPayments = computed(() => {
  let filtered = payments.value

  // Apply date filters
  if (filters.value.dateFrom) {
    filtered = filtered.filter(payment => payment.date >= filters.value.dateFrom)
  }
  if (filters.value.dateTo) {
    filtered = filtered.filter(payment => payment.date <= filters.value.dateTo)
  }

  // Apply product filter
  if (filters.value.product) {
    const productMap: Record<string, string[]> = {
      'meeting-room': ['Meeting Room'],
      'hot-desk': ['Hot Desk'],
      'private-office': ['Private Office'],
      'event-space': ['Event Space']
    }
    filtered = filtered.filter(payment => 
      productMap[filters.value.product]?.includes(payment.productType)
    )
  }

  // Apply location filter
  if (filters.value.location) {
    const locationMap: Record<string, string> = {
      'main-branch': 'Main Branch - Downtown',
      'tech-hub': 'Tech Hub - Silicon Valley',
      'creative-quarter': 'Creative Quarter'
    }
    filtered = filtered.filter(payment => 
      payment.locationName === locationMap[filters.value.location]
    )
  }

  return filtered
})

// Methods
const formatDate = (dateString: string) => {
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'short',
    day: 'numeric'
  })
}

const resetFilters = () => {
  filters.value = {
    dateFrom: '',
    dateTo: '',
    product: '',
    location: ''
  }
}

const saveCommissionSettings = () => {
  // In a real app, this would save to the backend
  console.log('Saving commission settings:', commissionSettings.value)
  console.log('Global settings:', globalSettings.value)
  showCommissionModal.value = false
  alert('Commission settings saved successfully!')
}
</script>
