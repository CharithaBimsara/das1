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
            <span 
              class="px-3 py-1 text-sm font-medium rounded-full"
              :class="getStatusClass(payment.status)"
            >
              {{ payment.status.charAt(0).toUpperCase() + payment.status.slice(1) }}
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
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Booking ID</label>
                <p class="text-sm text-gray-900 mt-1">{{ payment.bookingId }}</p>
              </div>
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Payment Date</label>
                <p class="text-sm text-gray-900 mt-1">{{ formatDate(payment.date) }}</p>
                <p class="text-sm text-gray-500">{{ payment.time }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- Location & Product Information -->
        <div class="bg-white rounded-xl shadow-card p-6">
          <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
            <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiMapMarker" />
            </svg>
            Location & Product Details
          </h2>
          <div class="space-y-4">
            <div>
              <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Location</label>
              <p class="text-sm font-medium text-gray-900 mt-1">{{ payment.locationName }}</p>
            </div>
            <div>
              <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Product</label>
              <p class="text-sm text-gray-900 mt-1">{{ payment.productName }}</p>
              <p class="text-sm text-gray-500">{{ payment.productType }}</p>
            </div>
            <div>
              <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Status</label>
              <span 
                class="inline-block px-2 py-1 text-xs font-medium rounded-full mt-1"
                :class="getStatusClass(payment.status)"
              >
                {{ payment.status === 'pending' ? 'Pending (Subscription)' : 'Paid' }}
              </span>
            </div>
          </div>
        </div>
      </div>

      <!-- Additional Facilities -->
      <div class="bg-white rounded-xl shadow-card p-6" v-if="payment.additionalFacilities && payment.additionalFacilities.length > 0">
        <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
            <path d="M12,2A10,10 0 0,0 2,12A10,10 0 0,0 12,22A10,10 0 0,0 22,12A10,10 0 0,0 12,2M13,7H11V11H7V13H11V17H13V13H17V11H13V7Z" />
          </svg>
          Additional Facilities
        </h2>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
          <div 
            v-for="facility in payment.additionalFacilities" 
            :key="facility.name"
            class="bg-gray-50 rounded-lg p-4"
          >
            <div class="flex justify-between items-center">
              <span class="text-sm font-medium text-gray-900">{{ facility.name }}</span>
              <span class="text-sm font-semibold text-gray-900">${{ facility.price }}</span>
            </div>
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
          <div class="flex justify-between" v-if="additionalFacilitiesTotal > 0">
            <span class="text-gray-600">Additional Facilities</span>
            <span class="text-gray-900">${{ additionalFacilitiesTotal }}</span>
          </div>
          <div class="flex justify-between">
            <span class="text-lg font-semibold text-gray-900">Total Amount</span>
            <span class="text-lg font-bold text-primary-600">${{ payment.totalAmount }}</span>
          </div>
        </div>

        <!-- Commission Information -->
        <div class="mt-6 pt-6 border-t border-gray-200">
          <h3 class="text-sm font-medium text-gray-900 mb-3">PayMedia Commission Details</h3>
          <div class="grid grid-cols-1 md:grid-cols-3 gap-4 text-sm">
            <div>
              <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Commission Rate</label>
              <p class="text-gray-900 mt-1">10.0%</p>
            </div>
            <div>
              <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Commission Amount</label>
              <p class="text-green-600 font-semibold mt-1">$15.00</p>
            </div>
            <div>
              <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Net Amount to Partner</label>
              <p class="text-gray-900 mt-1">$135.00</p>
            </div>
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
import jsPDF from 'jspdf'
import { useRoute } from 'vue-router'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { 
  mdiAccount, 
  mdiMapMarker, 
  mdiCurrencyUsd
} from '@mdi/js'

const route = useRoute()

// Sample payment data based on payments view structure
const payment = ref({
  id: 'PAY-001',
  paymentId: 'PM-2024-001',
  bookingId: 'BR-2034',
  customerName: 'John Doe',
  customerEmail: 'john.doe@example.com',
  productName: 'Executive Board Room',
  productType: 'Meeting Room',
  locationName: 'Main Branch - Downtown',
  baseAmount: 120,
  additionalFacilities: [
    { name: 'Projector', price: 20 },
    { name: 'Catering', price: 10 }
  ],
  totalAmount: 150,
  commission: 15.00,
  commissionRate: 10.0,
  status: 'paid',
  date: '2024-08-15',
  time: '10:30 AM'
})

// Computed properties
const additionalFacilitiesTotal = computed(() => {
  if (!payment.value.additionalFacilities || payment.value.additionalFacilities.length === 0) {
    return 0
  }
  return payment.value.additionalFacilities.reduce((total, facility) => total + facility.price, 0)
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

const getStatusClass = (status: string) => {
  switch (status) {
    case 'paid':
      return 'bg-green-100 text-green-800'
    case 'pending':
      return 'bg-yellow-100 text-yellow-800'
    default:
      return 'bg-gray-100 text-gray-800'
  }
}

const downloadInvoice = () => {
  // Generate and download a PDF invoice with table layout
  const doc = new jsPDF()
  const p = payment.value

  doc.setFontSize(18)
  doc.text('Invoice', 14, 18)
  doc.setFontSize(12)

  // Main info table
  const mainHeaders = ['Field', 'Value']
  const mainRows = [
    ['Invoice #', p.paymentId],
    ['Date', `${p.date} ${p.time}`],
    ['Customer', p.customerName],
    ['Email', p.customerEmail],
    ['Booking ID', p.bookingId],
    ['Location', p.locationName],
    ['Product', `${p.productName} (${p.productType})`],
    ['Status', p.status === 'pending' ? 'Pending (Subscription)' : 'Paid']
  ]

  let y = 26
  // Draw main info table
  doc.setFont('bold')
  doc.text(mainHeaders[0], 14, y)
  doc.text(mainHeaders[1], 70, y)
  doc.setFont('normal')
  y += 6
  mainRows.forEach(row => {
    doc.text(row[0], 14, y)
    doc.text(String(row[1]), 70, y)
    y += 6
  })

  // Additional Facilities Table
  y += 4
  doc.setFont('bold')
  doc.text('Additional Facilities', 14, y)
  doc.setFont('normal')
  y += 6
  if (p.additionalFacilities && p.additionalFacilities.length > 0) {
    doc.text('Name', 18, y)
    doc.text('Price', 70, y)
    y += 6
    p.additionalFacilities.forEach(facility => {
      doc.text(facility.name, 18, y)
      doc.text(`$${facility.price}`, 70, y)
      y += 6
    })
  } else {
    doc.text('None', 18, y)
    y += 6
  }

  // Price Breakdown
  y += 4
  doc.setFont('bold')
  doc.text('Price Breakdown', 14, y)
  doc.setFont('normal')
  y += 6
  doc.text('Total Amount', 18, y)
  doc.text(`$${p.totalAmount}`, 70, y)
  y += 6
  doc.text('Commission', 18, y)
  doc.text(`$${p.commission} (${p.commissionRate}%)`, 70, y)
  y += 6
  doc.text('Net Amount to Partner', 18, y)
  doc.text(`$${(p.totalAmount - p.commission).toFixed(2)}`, 70, y)

  doc.save(`invoice_${p.paymentId}.pdf`)
}

onMounted(() => {
  // In a real app, fetch payment data based on route.params.id
  console.log('Loading payment details for ID:', route.params.id)
  
  // Sample data mapping based on the payments data structure
  const samplePayments = [
    {
      id: 'PAY-001',
      paymentId: 'PM-2024-001',
      bookingId: 'BR-2034',
      customerName: 'John Doe',
      customerEmail: 'john.doe@example.com',
      productName: 'Executive Board Room',
      productType: 'Meeting Room',
      locationName: 'Main Branch - Downtown',
      baseAmount: 120,
      additionalFacilities: [
        { name: 'Projector', price: 20 },
        { name: 'Catering', price: 10 }
      ],
      totalAmount: 150,
      commission: 15.00,
      commissionRate: 10.0,
      status: 'paid',
      date: '2024-08-15',
      time: '10:30 AM'
    },
    {
      id: 'PAY-002',
      paymentId: 'PM-2024-002',
      bookingId: 'BR-2033',
      customerName: 'Jane Smith',
      customerEmail: 'jane.smith@example.com',
      productName: 'Hot Desk Area',
      productType: 'Hot Desk',
      locationName: 'Tech Hub - Silicon Valley',
      baseAmount: 50,
      additionalFacilities: [
        { name: 'Monitor', price: 10 }
      ],
      totalAmount: 60,
      commission: 7.50,
      commissionRate: 12.5,
      status: 'paid',
      date: '2024-08-15',
      time: '2:15 PM'
    }
  ]
  
  // Find payment by ID from route params
  const foundPayment = samplePayments.find(p => p.id === route.params.id)
  if (foundPayment) {
    payment.value = foundPayment
  }
})
</script>