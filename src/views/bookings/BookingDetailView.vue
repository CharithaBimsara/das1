<template>
  <AdminLayout>
    <div class="space-y-6" v-if="booking">
      <!-- Back Button -->
      <div class="flex items-center">
        <router-link to="/bookings" class="flex items-center text-gray-600 hover:text-gray-900">
          <svg class="w-5 h-5 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
          </svg>
          Back to Bookings
        </router-link>
      </div>

      <!-- Booking Header -->
      <div class="bg-white rounded-xl shadow-card p-6">
        <div class="flex items-center justify-between">
          <div>
            <h1 class="text-2xl font-bold text-gray-900">Booking Details - {{ booking.id }}</h1>
            <p class="text-gray-600 mt-1">{{ booking.productName }} • {{ booking.date }}</p>
          </div>
          <div class="flex items-center space-x-4">
            <span :class="getStatusClass(booking.status)" class="px-3 py-1 text-sm font-medium rounded-full">
              {{ booking.status }}
            </span>
            <button 
              v-if="booking.status !== 'cancelled'"
              @click="confirmCancelBooking"
              class="px-4 py-2 bg-orange-600 text-white rounded-lg hover:bg-orange-700 transition-colors"
            >
              Cancel Booking
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
                <h3 class="text-sm font-medium text-gray-900">{{ booking.customerName }}</h3>
                <p class="text-sm text-gray-500">{{ booking.customerEmail }}</p>
              </div>
            </div>
            <div class="grid grid-cols-2 gap-4 pt-4 border-t border-gray-200">
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Phone</label>
                <p class="text-sm text-gray-900">{{ booking.customerPhone }}</p>
              </div>
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">User Type</label>
                <span :class="booking.userType === 'registered' ? 'bg-blue-100 text-blue-800' : 'bg-gray-100 text-gray-800'" 
                      class="inline-block px-2 py-1 text-xs font-medium rounded-full mt-1">
                  {{ booking.userType }}
                </span>
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
              <img class="w-16 h-16 rounded-lg object-cover" :src="booking.productImage" :alt="booking.productName">
              <div>
                <h3 class="text-sm font-medium text-gray-900">{{ booking.productName }}</h3>
                <p class="text-sm text-gray-500">{{ booking.productType }}</p>
                <p class="text-sm text-gray-500">{{ booking.locationName }}</p>
                <p class="text-sm text-gray-400">{{ booking.companyName }}</p>
              </div>
            </div>
            <div class="grid grid-cols-2 gap-4 pt-4 border-t border-gray-200">
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Capacity</label>
                <p class="text-sm text-gray-900">{{ booking.capacity }} {{ booking.capacity === 1 ? 'person' : 'people' }}</p>
              </div>
              <div>
                <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Facilities</label>
                <div class="flex flex-wrap gap-1 mt-1">
                  <span v-for="facility in booking.facilities" :key="facility" 
                        class="px-2 py-1 text-xs bg-gray-100 text-gray-700 rounded">
                    {{ facility }}
                  </span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Booking Time -->
      <div class="bg-white rounded-xl shadow-card p-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
          <svg class="w-5 h-5 mr-2" fill="currentColor" viewBox="0 0 24 24">
            <path :d="mdiCalendarClock" />
          </svg>
          Booking Time
        </h2>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="text-center p-4 bg-gray-50 rounded-lg">
            <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Date</label>
            <p class="text-lg font-semibold text-gray-900 mt-1">{{ formatDate(booking.date) }}</p>
          </div>
          <div class="text-center p-4 bg-gray-50 rounded-lg">
            <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">Start Time</label>
            <p class="text-lg font-semibold text-gray-900 mt-1">{{ booking.startTime }}</p>
          </div>
          <div class="text-center p-4 bg-gray-50 rounded-lg">
            <label class="text-xs font-medium text-gray-500 uppercase tracking-wider">End Time</label>
            <p class="text-lg font-semibold text-gray-900 mt-1">{{ booking.endTime }}</p>
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
            <span class="text-gray-600">Base Price</span>
            <span class="text-gray-900">${{ booking.basePrice }}</span>
          </div>
          <div class="flex justify-between">
            <span class="text-gray-600">Additional Facilities</span>
            <span class="text-gray-900">${{ booking.additionalFacilities }}</span>
          </div>
          <div class="border-t border-gray-200 pt-3">
            <div class="flex justify-between">
              <span class="text-lg font-semibold text-gray-900">Total</span>
              <span class="text-lg font-bold text-primary-600">${{ booking.totalPrice }}</span>
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
        <h3 class="text-lg font-medium text-gray-900 mb-2">Booking Not Found</h3>
        <p class="text-gray-600 mb-4">The booking with ID "{{ route.params.id }}" could not be found.</p>
        <router-link to="/bookings" class="inline-flex items-center px-4 py-2 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors">
          <svg class="w-4 h-4 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
          </svg>
          Back to Bookings
        </router-link>
      </div>
    </div>

    <!-- Cancel Confirmation Modal -->
    <div v-if="showCancelModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50" @click="closeCancelModal">
      <div class="relative top-20 mx-auto p-5 border w-96 shadow-lg rounded-md bg-white" @click.stop>
        <div class="mt-3">
          <div class="flex items-center justify-center mx-auto w-12 h-12 rounded-full bg-orange-100">
            <svg class="w-6 h-6 text-orange-600" fill="currentColor" viewBox="0 0 24 24">
              <path :d="mdiCancel" />
            </svg>
          </div>
          <h3 class="text-lg font-medium text-gray-900 text-center mt-4">Cancel Booking</h3>
          <div class="mt-2 px-7 py-3">
            <p class="text-sm text-gray-500 text-center">
              Are you sure you want to cancel this booking? This action cannot be undone.
            </p>
            <div v-if="booking" class="mt-4 p-3 bg-gray-50 rounded-lg text-gray-900">
              <div class="text-sm space-y-1">
                <div><strong>Booking ID:</strong> {{ booking.id }}</div>
                <div><strong>Customer:</strong> {{ booking.customerName }}</div>
                <div><strong>Product:</strong> {{ booking.productName }}</div>
                <div><strong>Date:</strong> {{ formatDate(booking.date) }}</div>
                <div><strong>Time:</strong> {{ booking.startTime }} - {{ booking.endTime }}</div>
                <div><strong>Total:</strong> ${{ booking.totalPrice }}</div>
              </div>
            </div>
          </div>
          <div class="flex items-center justify-center pt-4 space-x-4">
            <button
              @click="closeCancelModal"
              class="px-4 py-2 bg-gray-200 text-gray-800 text-sm font-medium rounded-md hover:bg-gray-300 transition-colors"
            >
              Keep Booking
            </button>
            <button
              @click="cancelBooking"
              :disabled="isCancelling"
              class="px-4 py-2 bg-orange-600 text-white text-sm font-medium rounded-md hover:bg-orange-700 transition-colors disabled:opacity-50 disabled:cursor-not-allowed flex items-center space-x-2"
            >
              <svg v-if="isCancelling" class="animate-spin -ml-1 mr-2 h-4 w-4 text-white" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              <span>{{ isCancelling ? 'Cancelling...' : 'Cancel Booking' }}</span>
            </button>
          </div>
        </div>
      </div>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import { useRoute } from 'vue-router'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { mdiAccount, mdiOfficeBuilding, mdiCalendarClock, mdiCurrencyUsd, mdiCancel } from '@mdi/js'

const route = useRoute()

// Modal state
const showCancelModal = ref(false)
const isCancelling = ref(false)

// Reactive bookings data - same as BookingsView
const allBookings = ref([
  // Confirmed Bookings (Active)
  {
    id: 'BR-2034',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=200&h=200&fit=crop&crop=center',
    customerName: 'John Doe',
    customerEmail: 'john.doe@example.com',
    customerPhone: '+1 (555) 123-4567',
    userType: 'registered',
    date: '2025-08-20',
    startTime: '10:00 AM',
    endTime: '12:00 PM',
    duration: '2 hours',
    totalPrice: 100,
    basePrice: 100,
    additionalFacilities: 0,
    taxes: 0,
    status: 'confirmed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.',
    capacity: 12,
    facilities: ['WiFi', 'Projector', 'Whiteboard', 'Video Conference', 'Air Conditioning']
  },
  {
    id: 'BR-2035',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=200&h=200&fit=crop&crop=center',
    customerName: 'Jane Smith',
    customerEmail: 'jane.smith@example.com',
    customerPhone: '+1 (555) 234-5678',
    userType: 'guest',
    date: '2025-08-21',
    startTime: '9:00 AM',
    endTime: '5:00 PM',
    duration: '8 hours',
    totalPrice: 64,
    basePrice: 64,
    additionalFacilities: 0,
    taxes: 0,
    status: 'confirmed',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.',
    capacity: 1,
    facilities: ['WiFi', 'Power Outlet', 'Shared Kitchen', 'Printer Access']
  },
  {
    id: 'BR-2036',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=200&h=200&fit=crop&crop=center',
    customerName: 'Robert Chen',
    customerEmail: 'robert.chen@example.com',
    customerPhone: '+1 (555) 345-6789',
    userType: 'registered',
    date: '2025-08-22',
    startTime: '2:00 PM',
    endTime: '4:00 PM',
    duration: '2 hours',
    totalPrice: 100,
    basePrice: 100,
    additionalFacilities: 0,
    taxes: 0,
    status: 'confirmed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.',
    capacity: 12,
    facilities: ['WiFi', 'Projector', 'Whiteboard', 'Video Conference', 'Air Conditioning']
  },
  {
    id: 'BR-2037',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=200&h=200&fit=crop&crop=center',
    customerName: 'Emily Rodriguez',
    customerEmail: 'emily.rodriguez@example.com',
    customerPhone: '+1 (555) 456-7890',
    userType: 'registered',
    date: '2025-08-23',
    startTime: '8:00 AM',
    endTime: '12:00 PM',
    duration: '4 hours',
    totalPrice: 32,
    basePrice: 32,
    additionalFacilities: 0,
    taxes: 0,
    status: 'confirmed',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.',
    capacity: 1,
    facilities: ['WiFi', 'Power Outlet', 'Shared Kitchen', 'Printer Access']
  },
  {
    id: 'BR-2038',
    productName: 'Private Dedicated Desk',
    productType: 'Dedicated Desk',
    productId: 'PROD003',
    productImage: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=200&h=200&fit=crop&crop=center',
    customerName: 'David Kim',
    customerEmail: 'david.kim@example.com',
    customerPhone: '+1 (555) 567-8901',
    userType: 'guest',
    date: '2025-08-24',
    startTime: '9:00 AM',
    endTime: '6:00 PM',
    duration: '9 hours',
    totalPrice: 450,
    basePrice: 450,
    additionalFacilities: 0,
    taxes: 0,
    status: 'confirmed',
    location: 'business-center',
    locationName: 'Business Center',
    companyName: 'Global Solutions Inc.',
    capacity: 1,
    facilities: ['WiFi', 'Private Storage', 'Ergonomic Chair', 'Desk Lamp', 'Personal Phone Line']
  },
  {
    id: 'BR-2039',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=200&h=200&fit=crop&crop=center',
    customerName: 'Sophie Brown',
    customerEmail: 'sophie.brown@example.com',
    customerPhone: '+1 (555) 678-9012',
    userType: 'registered',
    date: '2025-08-25',
    startTime: '1:00 PM',
    endTime: '3:00 PM',
    duration: '2 hours',
    totalPrice: 100,
    basePrice: 100,
    additionalFacilities: 0,
    taxes: 0,
    status: 'confirmed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.',
    capacity: 12,
    facilities: ['WiFi', 'Projector', 'Whiteboard', 'Video Conference', 'Air Conditioning']
  },
  {
    id: 'BR-2040',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=200&h=200&fit=crop&crop=center',
    customerName: 'Alex Johnson',
    customerEmail: 'alex.johnson@example.com',
    customerPhone: '+1 (555) 789-0123',
    userType: 'registered',
    date: '2025-08-26',
    startTime: '10:00 AM',
    endTime: '6:00 PM',
    duration: '8 hours',
    totalPrice: 64,
    basePrice: 64,
    additionalFacilities: 0,
    taxes: 0,
    status: 'confirmed',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.',
    capacity: 1,
    facilities: ['WiFi', 'Power Outlet', 'Shared Kitchen', 'Printer Access']
  },
  {
    id: 'BR-2041',
    productName: 'Private Dedicated Desk',
    productType: 'Dedicated Desk',
    productId: 'PROD003',
    productImage: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=200&h=200&fit=crop&crop=center',
    customerName: 'Maria Garcia',
    customerEmail: 'maria.garcia@example.com',
    customerPhone: '+1 (555) 890-1234',
    userType: 'guest',
    date: '2025-08-27',
    startTime: '8:00 AM',
    endTime: '5:00 PM',
    duration: '9 hours',
    totalPrice: 450,
    basePrice: 450,
    additionalFacilities: 0,
    taxes: 0,
    status: 'confirmed',
    location: 'business-center',
    locationName: 'Business Center',
    companyName: 'Global Solutions Inc.',
    capacity: 1,
    facilities: ['WiFi', 'Private Storage', 'Ergonomic Chair', 'Desk Lamp', 'Personal Phone Line']
  },
  // Completed Bookings (History)
  {
    id: 'BR-2020',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=200&h=200&fit=crop&crop=center',
    customerName: 'Mike Johnson',
    customerEmail: 'mike.johnson@example.com',
    customerPhone: '+1 (555) 678-9012',
    userType: 'registered',
    date: '2025-08-15',
    startTime: '9:00 AM',
    endTime: '11:00 AM',
    duration: '2 hours',
    totalPrice: 100,
    basePrice: 100,
    additionalFacilities: 0,
    taxes: 0,
    status: 'completed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.',
    capacity: 12,
    facilities: ['WiFi', 'Projector', 'Whiteboard', 'Video Conference', 'Air Conditioning']
  },
  {
    id: 'BR-2021',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=200&h=200&fit=crop&crop=center',
    customerName: 'Lisa Thompson',
    customerEmail: 'lisa.thompson@example.com',
    customerPhone: '+1 (555) 789-0123',
    userType: 'registered',
    date: '2025-08-16',
    startTime: '8:00 AM',
    endTime: '4:00 PM',
    duration: '8 hours',
    totalPrice: 64,
    basePrice: 64,
    additionalFacilities: 0,
    taxes: 0,
    status: 'completed',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.',
    capacity: 1,
    facilities: ['WiFi', 'Power Outlet', 'Shared Kitchen', 'Printer Access']
  },
  // Cancelled Bookings
  {
    id: 'BR-2010',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=200&h=200&fit=crop&crop=center',
    customerName: 'Sarah Wilson',
    customerEmail: 'sarah.wilson@example.com',
    customerPhone: '+1 (555) 890-1234',
    userType: 'guest',
    date: '2025-08-12',
    startTime: '3:00 PM',
    endTime: '5:00 PM',
    duration: '2 hours',
    totalPrice: 100,
    basePrice: 100,
    additionalFacilities: 0,
    taxes: 0,
    status: 'cancelled',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.',
    capacity: 12,
    facilities: ['WiFi', 'Projector', 'Whiteboard', 'Video Conference', 'Air Conditioning']
  }
])

// Current booking based on route parameter - now reactive with fallbacks
const booking = computed(() => {
  const bookingId = route.params.id as string
  const foundBooking = allBookings.value.find(b => b.id === bookingId)
  
  if (foundBooking) {
    console.log('Booking found:', foundBooking.id, foundBooking)
    
    // Return booking with fallbacks for missing fields
    return {
      ...foundBooking,
      // Ensure all required fields have fallback values
      customerEmail: foundBooking.customerEmail || `${foundBooking.customerName?.toLowerCase().replace(' ', '.')}@example.com`,
      customerPhone: foundBooking.customerPhone || '+1 (555) 000-0000',
      basePrice: foundBooking.basePrice || foundBooking.totalPrice || 0,
      additionalFacilities: foundBooking.additionalFacilities || 0,
      taxes: foundBooking.taxes || 0,
      capacity: foundBooking.capacity || 1,
      facilities: foundBooking.facilities || ['WiFi', 'Basic Amenities'],
      locationName: foundBooking.locationName || 'Location Not Specified',
      companyName: foundBooking.companyName || 'Company Not Specified'
    }
  } else {
    console.warn('Booking not found for ID:', bookingId)
    console.log('Available booking IDs:', allBookings.value.map(b => b.id))
  }
  
  return null
})

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

const formatDate = (dateString: string) => {
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    weekday: 'long',
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  })
}

// Modal functions
const confirmCancelBooking = () => {
  showCancelModal.value = true
}

const closeCancelModal = () => {
  showCancelModal.value = false
  isCancelling.value = false
}

const cancelBooking = async () => {
  if (!booking.value) return
  
  isCancelling.value = true
  
  try {
    // Simulate API call delay
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // Update booking status
    booking.value.status = 'cancelled'
    
    // Save cancellation to localStorage for persistence across views
    const bookingStatuses = JSON.parse(localStorage.getItem('bookingStatuses') || '{}')
    bookingStatuses[booking.value.id] = 'cancelled'
    localStorage.setItem('bookingStatuses', JSON.stringify(bookingStatuses))
    
    // Log the cancellation for audit purposes
    const cancelledBookings = JSON.parse(localStorage.getItem('cancelledBookings') || '[]')
    cancelledBookings.push({
      ...booking.value,
      cancelledAt: new Date().toISOString(),
      cancelledBy: 'Admin'
    })
    localStorage.setItem('cancelledBookings', JSON.stringify(cancelledBookings))
    
    closeCancelModal()
    
    // Show success message
    console.log('Booking cancelled successfully:', booking.value.id)
    
  } catch (error) {
    console.error('Error cancelling booking:', error)
    // Handle error (show toast notification, etc.)
  } finally {
    isCancelling.value = false
  }
}

onMounted(() => {
  console.log('Loading booking details for ID:', route.params.id)
  
  // Method to sync missing bookings from BookingsView
  const syncBookingsData = () => {
    // Load bookings from localStorage if available (same as BookingsView)
    const savedBookings = localStorage.getItem('allBookings')
    if (savedBookings) {
      try {
        const parsedBookings = JSON.parse(savedBookings)
        if (Array.isArray(parsedBookings) && parsedBookings.length > 0) {
          // Replace current bookings with saved data to ensure sync
          allBookings.value = parsedBookings
          console.log('✅ Synced bookings from localStorage:', parsedBookings.length)
          return true
        }
      } catch (error) {
        console.warn('⚠️ Error loading bookings from localStorage:', error)
      }
    }
    return false
  }
  
  // Try to sync data first
  const synced = syncBookingsData()
  
  // Update booking statuses from localStorage (sync with BookingsView)
  const bookingStatuses = JSON.parse(localStorage.getItem('bookingStatuses') || '{}')
  allBookings.value.forEach(booking => {
    if (bookingStatuses[booking.id]) {
      booking.status = bookingStatuses[booking.id]
    }
  })
  
  // Log current booking lookup for debugging
  const bookingId = route.params.id as string
  const foundBooking = allBookings.value.find(b => b.id === bookingId)
  
  if (foundBooking) {
    console.log('✅ Booking found and loaded:', foundBooking.id, foundBooking.productName)
  } else {
    console.error('❌ Booking not found for ID:', bookingId)
    console.log('📋 Available bookings:', allBookings.value.map(b => ({ id: b.id, name: b.productName })))
    
    if (!synced) {
      console.log('💡 Tip: Make sure BookingsView and BookingDetailView are using the same data source')
    }
  }
})
</script>
