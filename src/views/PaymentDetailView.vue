<template>
  <AdminLayout>
    <div class="space-y-6" v-if="payment">
      <!-- Back Button -->
      <div class="flex items-center">
        <router-link to="/payments" class="flex items-center text-gray-600 hover:text-gray-900">
          <svg class="w-5 h-5 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
          </svg>
          Back to Payments
        </router-link>
      </div>

      <!-- Payment Header -->
      <div class="bg-white rounded-xl shadow-card p-6">
        <div class="flex items-center justify-between">
          <div>
            <h1 class="text-2xl font-bold text-gray-900">Payment Details</h1>
            <p class="text-gray-600 mt-1">Payment ID: {{ payment.paymentId }}</p>
          </div>
          <div class="flex items-center space-x-4">
            <span class="px-3 py-1 text-sm font-medium bg-green-100 text-green-800 rounded-full">
              Completed
            </span>
            <button 
              @click="downloadInvoice"
              class="px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors flex items-center space-x-2"
            >
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
              </svg>
              <span>Download Invoice</span>
            </button>
          </div>
        </div>
      </div>

      <!-- Payment Information Grid -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <!-- Customer Information -->
        <div class="bg-white rounded-xl shadow-card p-6">
          <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
            <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiAccount" />
            </svg>
            Customer Information
          </h2>
          <div class="space-y-3">
            <div class="flex items-center space-x-3">
              <div class="w-12 h-12 bg-primary-100 rounded-full flex items-center justify-center">
                <svg class="w-6 h-6 text-primary-600" fill="currentColor" viewBox="0 0 24 24">
                  <path :d="mdiAccount" />
                </svg>
              </div>
              <div>
                <h3 class="text-sm font-medium text-gray-900">{{ payment.customerName }}</h3>
                <p class="text-sm text-gray-500">{{ payment.customerEmail }}</p>
              </div>
            </div>
            <div class="grid grid-cols-2 gap-4 pt-4 border-t border-gray-200">
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">User Type</label>
                <p class="text-sm text-gray-900 mt-1">{{ payment.userType || 'Registered' }}</p>
              </div>
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Phone</label>
                <p class="text-sm text-gray-900 mt-1">{{ payment.customerPhone || '+1 (555) 123-4567' }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- Transaction Information -->
        <div class="bg-white rounded-xl shadow-card p-6">
          <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
            <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiCreditCard" />
            </svg>
            Transaction Details
          </h2>
          <div class="space-y-3">
            <div class="grid grid-cols-2 gap-4">
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Payment Method</label>
                <div class="flex items-center mt-1">
                  <svg class="w-4 h-4 text-blue-600 mr-2" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiCreditCard" />
                  </svg>
                  <span class="text-sm text-gray-900">{{ payment.method }}</span>
                </div>
              </div>
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Transaction ID</label>
                <p class="text-sm text-gray-900 mt-1 font-mono">{{ payment.transactionRef }}</p>
              </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Date & Time</label>
                <p class="text-sm text-gray-900 mt-1">{{ formatDate(payment.date) }}</p>
                <p class="text-sm text-gray-500">{{ payment.time }}</p>
              </div>
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Status</label>
                <span class="inline-block px-2 py-1 text-xs font-medium bg-green-100 text-green-800 rounded-full mt-1">
                  Completed
                </span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Booking Information -->
      <div class="bg-white rounded-xl shadow-card p-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
            <path :d="mdiCalendarCheck" />
          </svg>
          Booking Information
        </h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div>
            <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Booking ID</label>
            <p class="text-sm font-medium text-gray-900 mt-1">{{ payment.bookingId }}</p>
          </div>
          <div>
            <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Product</label>
            <p class="text-sm text-gray-900 mt-1">{{ payment.productName }}</p>
            <p class="text-sm text-gray-500">{{ payment.productType }}</p>
          </div>
          <div>
            <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Location</label>
            <p class="text-sm text-gray-900 mt-1">{{ payment.locationName }}</p>
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
        <div class="space-y-4">
          <div class="flex justify-between">
            <span class="text-gray-600">Base Price</span>
            <span class="text-gray-900">${{ priceBreakdown.basePrice }}</span>
          </div>
          <div class="flex justify-between" v-if="priceBreakdown.additionalFacilities > 0">
            <span class="text-gray-600">Additional Facilities</span>
            <span class="text-gray-900">${{ priceBreakdown.additionalFacilities }}</span>
          </div>
          <div class="flex justify-between" v-if="priceBreakdown.discount > 0">
            <span class="text-gray-600">Discount</span>
            <span class="text-green-600">-${{ priceBreakdown.discount }}</span>
          </div>
          <div class="flex justify-between">
            <span class="text-gray-600">Subtotal</span>
            <span class="text-gray-900">${{ priceBreakdown.subtotal }}</span>
          </div>
          <div class="flex justify-between">
            <span class="text-gray-600">Taxes & Fees ({{ priceBreakdown.taxRate }}%)</span>
            <span class="text-gray-900">${{ priceBreakdown.taxes }}</span>
          </div>
          <div class="border-t border-gray-200 pt-4">
            <div class="flex justify-between">
              <span class="text-lg font-semibold text-gray-900">Total Amount</span>
              <span class="text-lg font-bold text-primary-600">${{ payment.amount }}</span>
            </div>
          </div>
        </div>

        <!-- Commission Information -->
        <div class="mt-6 pt-6 border-t border-gray-200">
          <h3 class="text-sm font-medium text-gray-900 mb-3">Commission Details</h3>
          <div class="grid grid-cols-1 md:grid-cols-3 gap-4 text-sm">
            <div>
              <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Commission Rate</label>
              <p class="text-gray-900 mt-1">{{ commissionDetails.rate }}%</p>
            </div>
            <div>
              <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Commission Amount</label>
              <p class="text-gray-900 mt-1">${{ commissionDetails.amount }}</p>
            </div>
            <div>
              <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Net Amount</label>
              <p class="text-gray-900 mt-1">${{ commissionDetails.netAmount }}</p>
            </div>
          </div>
        </div>
      </div>

      <!-- Additional Information -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <!-- Payment Processing -->
        <div class="bg-white rounded-xl shadow-card p-6">
          <h2 class="text-lg font-semibold text-gray-900 mb-4">Payment Processing</h2>
          <div class="space-y-3">
            <div class="flex justify-between">
              <span class="text-gray-600">Processing Fee</span>
              <span class="text-gray-900">${{ processingDetails.fee }}</span>
            </div>
            <div class="flex justify-between">
              <span class="text-gray-600">Gateway</span>
              <span class="text-gray-900">{{ processingDetails.gateway }}</span>
            </div>
            <div class="flex justify-between">
              <span class="text-gray-600">Authorization Code</span>
              <span class="text-gray-900 font-mono">{{ processingDetails.authCode }}</span>
            </div>
          </div>
        </div>

        <!-- Refund Information -->
        <div class="bg-white rounded-xl shadow-card p-6">
          <h2 class="text-lg font-semibold text-gray-900 mb-4">Refund Policy</h2>
          <div class="space-y-3">
            <div class="flex justify-between">
              <span class="text-gray-600">Refund Status</span>
              <span class="text-gray-900">{{ refundInfo.status }}</span>
            </div>
            <div class="flex justify-between">
              <span class="text-gray-600">Refundable Amount</span>
              <span class="text-gray-900">${{ refundInfo.refundableAmount }}</span>
            </div>
            <div class="flex justify-between">
              <span class="text-gray-600">Refund Deadline</span>
              <span class="text-gray-900">{{ refundInfo.deadline }}</span>
            </div>
            <button 
              v-if="refundInfo.status === 'Eligible'"
              class="w-full mt-4 px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 transition-colors"
            >
              Process Refund
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Loading state -->
    <div v-else class="flex items-center justify-center h-64">
      <div class="animate-spin rounded-full h-8 w-8 border-b-2 border-primary-600"></div>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { 
  mdiAccount, 
  mdiCreditCard, 
  mdiCalendarCheck, 
  mdiCurrencyUsd
} from '@mdi/js'

const route = useRoute()

// Sample payment data
const payment = ref({
  id: 'PAY-001',
  paymentId: 'PM-2024-001',
  customerName: 'John Doe',
  customerEmail: 'john.doe@example.com',
  customerPhone: '+1 (555) 123-4567',
  userType: 'Registered',
  amount: 150,
  method: 'Credit Card',
  productName: 'Executive Board Room',
  productType: 'Meeting Room',
  locationName: 'Main Branch - Downtown',
  bookingId: 'BR-2034',
  date: '2024-08-15',
  time: '10:30 AM',
  transactionRef: 'TXN-789123456'
})

// Price breakdown
const priceBreakdown = ref({
  basePrice: 120,
  additionalFacilities: 20,
  discount: 0,
  subtotal: 140,
  taxRate: 7.5,
  taxes: 10.50,
  total: 150
})

// Commission details
const commissionDetails = ref({
  rate: 10.0,
  amount: 15.00,
  netAmount: 135.00
})

// Processing details
const processingDetails = ref({
  fee: 4.35,
  gateway: 'Stripe',
  authCode: 'AUTH123456'
})

// Refund information
const refundInfo = ref({
  status: 'Eligible',
  refundableAmount: 150,
  deadline: 'Aug 22, 2024'
})

// Methods
const formatDate = (dateString: string) => {
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    weekday: 'long',
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  })
}

const downloadInvoice = () => {
  // In a real app, this would generate and download a PDF invoice
  alert('Invoice download started. Check your downloads folder.')
}

onMounted(() => {
  // In a real app, fetch payment data based on route.params.id
  console.log('Loading payment details for ID:', route.params.id)
})
</script>
