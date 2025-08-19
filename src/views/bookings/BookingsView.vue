<template>
  <AdminLayout>
    <div class="space-y-6">
      

      <!-- Tabs -->
      <div class="bg-white rounded-xl shadow-card">
        <div class="border-b border-gray-200">
          <nav class="flex space-x-8 px-6" aria-label="Tabs">
            <button
              v-for="tab in tabs"
              :key="tab.id"
              @click="activeTab = tab.id"
              :class="[
                activeTab === tab.id
                  ? 'border-primary-500 text-primary-600'
                  : 'border-transparent text-gray-500 hover:text-gray-700 hover:border-gray-300',
                'whitespace-nowrap py-4 px-1 border-b-2 font-medium text-sm'
              ]"
            >
              {{ tab.name }}
              <span :class="[
                activeTab === tab.id ? 'bg-primary-100 text-primary-600' : 'bg-gray-100 text-gray-900',
                'ml-2 py-0.5 px-2.5 rounded-full text-xs font-medium'
              ]">
                {{ getTabCount(tab.id) }}
              </span>
            </button>
          </nav>
        </div>

        <!-- Filters -->
        <div class="p-6 border-b border-gray-200">
          <div class="grid grid-cols-1 md:grid-cols-5 gap-6">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Date Range</label>
              <input
                type="text"
                v-model="dateRangeDisplay"
                @click="showDatePicker = !showDatePicker"
                readonly
                class="date-input w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-sm text-gray-900 cursor-pointer"
                placeholder="Select Date"
              />
              <!-- Booking.com Style Date Range Picker -->
              <div v-if="showDatePicker" class="date-picker-container absolute z-50 mt-1 bg-white border border-gray-300 rounded-lg shadow-lg p-4 w-80 text-gray-900">
                <div class="flex justify-between items-center mb-4">
                  <button @click="previousMonth" class="p-1 hover:bg-gray-100 rounded">
                    <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M15.41 7.41L14 6l-6 6 6 6 1.41-1.41L10.83 12z"/>
                    </svg>
                  </button>
                  <span class="font-medium">{{ currentMonthYear }}</span>
                  <button @click="nextMonth" class="p-1 hover:bg-gray-100 rounded">
                    <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M10 6L8.59 7.41 13.17 12l-4.58 4.59L10 18l6-6z"/>
                    </svg>
                  </button>
                </div>
                
                <!-- Calendar Grid -->
                <div class="grid grid-cols-7 gap-1 mb-2">
                  <div v-for="day in ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa']" :key="day" class="text-xs font-medium text-gray-500 text-center py-2">
                    {{ day }}
                  </div>
                </div>
                
                <div class="grid grid-cols-7 gap-1">
                  <div
                    v-for="date in calendarDates"
                    :key="date.dateString"
                    @click="selectDate(date)"
                    :class="[
                      'text-sm text-center py-2 cursor-pointer rounded',
                      !date.isCurrentMonth ? 'text-gray-300' : 'text-gray-900',
                      isDateSelected(date) ? 'bg-primary-600 text-white' : '',
                      isDateInRange(date) ? 'bg-primary-100' : '',
                      'hover:bg-primary-50'
                    ]"
                  >
                    {{ date.day }}
                  </div>
                </div>

                <div class="flex justify-end items-end mt-4 pt-4 border-t border-gray-200">
                  <div class="flex space-x-2">
                    <button
                      @click="clearDateRange"
                      class="px-3 py-1 text-xs border border-gray-300 text-gray-600 rounded hover:bg-gray-50"
                    >
                      Clear
                    </button>
                    <button
                      @click="showDatePicker = false"
                      :disabled="!filters.startDate"
                      class="px-3 py-1 text-xs bg-primary-600 text-white rounded hover:bg-primary-700 disabled:opacity-50 disabled:cursor-not-allowed"
                    >
                      Apply
                    </button>
                  </div>
                </div>
              </div>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Location</label>
              <select v-model="filters.location" class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-sm text-gray-900">
                <option value="">All Locations</option>
                <option value="main-branch">Main Branch</option>
                <option value="tech-hub">Tech Hub</option>
                <option value="business-center">Business Center</option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">Product Type</label>
              <select v-model="filters.productType" class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-sm text-gray-900">
                <option value="">All Types</option>
                <option value="Meeting Room">Meeting Room</option>
                <option value="Hot Desk">Hot Desk</option>
                <option value="Dedicated Desk">Dedicated Desk</option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">User Type</label>
              <select v-model="filters.userType" class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-sm text-gray-900">
                <option value="">All Users</option>
                <option value="registered">Registered</option>
                <option value="guest">Guest</option>
              </select>
            </div>
          <div class="flex items-end justify-end">
            <button
              @click="resetFilters"
              class="px-6 py-2 border border-gray-300 text-gray-100 rounded-lg hover:bg-green-700 transition-colors bg-green-600"
            >
              Reset Filters
            </button>
          </div>
          
          </div>
        </div>

        <!-- Bookings Table -->
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Booking ID
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Product
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Customer
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Date & Time
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Duration
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Total Price
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Status
                </th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                  Actions
                </th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr v-for="booking in paginatedBookings" :key="booking.id" class="hover:bg-gray-50">
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm font-medium text-gray-900">{{ booking.id }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="flex items-center">
                    <div class="flex-shrink-0 h-10 w-10">
                      <img class="h-10 w-10 rounded-lg object-cover" :src="booking.productImage" :alt="booking.productName">
                    </div>
                    <div class="ml-4">
                      <div class="text-sm font-medium text-gray-900">{{ booking.productName }}</div>
                      <div class="text-sm text-gray-500">{{ booking.productType }}</div>
                    </div>
                  </div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm text-gray-900">{{ booking.customerName }}</div>
                  <div class="text-sm text-gray-500">{{ booking.userType }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <div class="text-sm text-gray-900">{{ booking.date }}</div>
                  <div class="text-sm text-gray-500">{{ booking.startTime }} - {{ booking.endTime }}</div>
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                  {{ booking.duration }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                  ${{ booking.totalPrice }}
                </td>
                <td class="px-6 py-4 whitespace-nowrap">
                  <span :class="getStatusClass(booking.status)" class="px-2 py-1 text-xs font-medium rounded-full">
                    {{ booking.status }}
                  </span>
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                  <div class="flex items-center space-x-3">
                    <!-- View Details Link -->
                    <router-link :to="viewBookingDetails(booking)" class="text-primary-600 hover:text-primary-900 flex items-center space-x-1" title="View Details">
                      <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                        <path :d="mdiEye" />
                      </svg>
                    </router-link>
                    
                    <!-- Alternative: View Details Button (programmatic navigation) -->
                    <!-- <button @click="$router.push(viewBookingDetails(booking))" class="text-primary-600 hover:text-primary-900 flex items-center space-x-1" title="View Details">
                      <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                        <path :d="mdiEye" />
                      </svg>
                    </button> -->
                    
                    <!-- Actions for upcoming bookings -->
                    <button v-if="activeTab === 'bookings' && booking.status !== 'cancelled'" @click="confirmCancelBooking(booking)" class="text-orange-600 hover:text-orange-900 flex items-center space-x-1" title="Cancel Booking">
                      <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                        <path :d="mdiCancel" />
                      </svg>
                    </button>
                    
                    <!-- Actions for history bookings -->
                    <button v-if="activeTab === 'history'" @click="confirmDeleteBooking(booking)" class="text-red-600 hover:text-red-900 flex items-center space-x-1" title="Delete Booking">
                      <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                        <path :d="mdiDelete" />
                      </svg>
                    </button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Pagination -->
        <div class="bg-white px-4 py-3 border-t border-gray-200 sm:px-6">
          <div class="flex items-center justify-between">
            <div class="flex-1 flex justify-between sm:hidden">
              <button @click="previousPage" :disabled="currentPage === 1"
                class="relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50">
                Previous
              </button>
              <button @click="nextPage" :disabled="currentPage === totalPages"
                class="ml-3 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50">
                Next
              </button>
            </div>
            <div class="hidden sm:flex-1 sm:flex sm:items-center sm:justify-between">
              <div>
                <p class="text-sm text-gray-700">
                  Showing {{ startItem }} to {{ endItem }} of {{ filteredBookings.length }} results
                </p>
              </div>
              <div>
                <nav class="relative z-0 inline-flex rounded-md shadow-sm -space-x-px">
                  <button @click="previousPage" :disabled="currentPage === 1"
                    class="relative inline-flex items-center px-2 py-2 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-500 hover:bg-gray-50 disabled:opacity-50">
                    <svg class="h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
                      <path fill-rule="evenodd"
                        d="M12.707 5.293a1 1 0 010 1.414L9.414 10l3.293 3.293a1 1 0 01-1.414 1.414l-4-4a1 1 0 010-1.414l4-4a1 1 0 011.414 0z"
                        clip-rule="evenodd" />
                    </svg>
                  </button>
                  <button v-for="page in visiblePages" :key="page" @click="goToPage(page)" :class="[
                    page === currentPage
                      ? 'z-10 bg-primary-50 border-primary-500 text-primary-600'
                      : 'bg-white border-gray-300 text-gray-500 hover:bg-gray-50',
                    'relative inline-flex items-center px-4 py-2 border text-sm font-medium'
                  ]">
                    {{ page }}
                  </button>
                  <button @click="nextPage" :disabled="currentPage === totalPages"
                    class="relative inline-flex items-center px-2 py-2 rounded-r-md border border-gray-300 bg-white text-sm font-medium text-gray-500 hover:bg-gray-50 disabled:opacity-50">
                    <svg class="h-5 w-5" fill="currentColor" viewBox="0 0 20 20">
                      <path fill-rule="evenodd"
                        d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414 0z"
                        clip-rule="evenodd" />
                    </svg>
                  </button>
                </nav>
              </div>
            </div>
          </div>
        </div>

        <!-- Empty State -->
        <div v-if="filteredBookings.length === 0" class="text-center py-12">
          <svg class="mx-auto h-12 w-12 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
          </svg>
          <h3 class="mt-2 text-sm font-medium text-gray-900">No bookings found</h3>
          <p class="mt-1 text-sm text-gray-500">No bookings match the current filters.</p>
        </div>
      </div>
    </div>

    <!-- Cancel Confirmation Modal -->
    <div v-if="showCancelModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50" @click="closeCancelModal">
      <div class="relative top-10 mx-auto p-5 border w-[500px] shadow-lg rounded-md bg-white" @click.stop>
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
            <div v-if="bookingToCancel" class="mt-4 p-3 bg-gray-50 rounded-lg text-gray-900">
              <div class="text-sm space-y-1">
                <div><strong>Booking ID:</strong> {{ bookingToCancel.id }}</div>
                <div><strong>Customer:</strong> {{ bookingToCancel.customerName }}</div>
                <div><strong>Product:</strong> {{ bookingToCancel.productName }}</div>
                <div><strong>Date:</strong> {{ bookingToCancel.date }}</div>
                <div><strong>Time:</strong> {{ bookingToCancel.startTime }} - {{ bookingToCancel.endTime }}</div>
                <div><strong>Total Paid:</strong> ${{ bookingToCancel.totalPrice }}</div>
              </div>
            </div>

            <!-- Refund Options -->
            <div class="mt-4 p-3 bg-blue-50 rounded-lg border border-blue-200">
              <div class="flex items-start space-x-3">
                <input
                  id="refundApplicable"
                  v-model="refundOptions.isRefundApplicable"
                  type="checkbox"
                  class="mt-1 h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300 rounded"
                />
                <div class="flex-1">
                  <label for="refundApplicable" class="text-sm font-medium text-blue-900">
                    Refund Applicable
                  </label>
                  <p class="text-xs text-blue-700 mt-1">Check this if customer is eligible for refund</p>
                </div>
              </div>

              <!-- Refund Details (shown when checkbox is checked) -->
              <div v-if="refundOptions.isRefundApplicable" class="mt-4 space-y-4 pl-7">
                <!-- Refund Amount -->
                <div>
                  <label class="block text-sm font-medium text-blue-900 mb-1">Refund Amount</label>
                  <div class="flex items-center space-x-2">
                    <span class="text-sm text-blue-700">$</span>
                    <input
                      v-model="refundOptions.refundAmount"
                      type="number"
                      step="0.01"
                      :max="bookingToCancel?.totalPrice"
                      min="0"
                      class="w-24 text-sm border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500"
                      placeholder="0.00"
                    />
                    <span class="text-xs text-blue-600">
                      (Max: ${{ bookingToCancel?.totalPrice }})
                    </span>
                  </div>
                </div>

                <!-- Payment Slip Upload Section -->
                <div class="mt-4">
                  <label class="block text-sm font-medium text-blue-900 mb-2">Upload Payment Slip (Optional)</label>
                  
                  <!-- Drag and Drop Area -->
                  <div
                    @drop="handleFileDrop"
                    @dragover.prevent="isDragging = true"
                    @dragenter.prevent="isDragging = true"
                    @dragleave.prevent="isDragging = false"
                    :class="[
                      'border-2 border-dashed rounded-lg p-4 text-center cursor-pointer transition-colors',
                      isDragging ? 'border-blue-400 bg-blue-50' : 'border-gray-300 hover:border-blue-400 hover:bg-blue-50'
                    ]"
                    @click="triggerFileInput"
                  >
                    <input
                      ref="fileInput"
                      type="file"
                      class="hidden"
                      accept="image/*,.pdf"
                      @change="handleFileSelect"
                    />
                    
                    <div v-if="!uploadedFile">
                      <svg class="mx-auto h-8 w-8 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path>
                      </svg>
                      <p class="mt-1 text-xs text-gray-600">
                        <span class="font-medium text-blue-600">Click to upload</span> or drag and drop
                      </p>
                      <p class="text-xs text-gray-500">PNG, JPG, PDF up to 5MB</p>
                    </div>
                    
                    <!-- File Preview -->
                    <div v-else class="flex items-center justify-center space-x-2">
                      <svg class="h-6 w-6 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                      </svg>
                      <span class="text-sm text-green-700 font-medium">{{ uploadedFile.name }}</span>
                      <button @click.stop="removeFile" class="text-red-500 hover:text-red-700">
                        <svg class="h-4 w-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                        </svg>
                      </button>
                    </div>
                  </div>
                  
                  <!-- File Upload Progress -->
                  <div v-if="uploadProgress > 0 && uploadProgress < 100" class="mt-2">
                    <div class="bg-gray-200 rounded-full h-2">
                      <div class="bg-blue-600 h-2 rounded-full transition-all duration-300" :style="{ width: uploadProgress + '%' }"></div>
                    </div>
                    <p class="text-xs text-gray-600 mt-1">Uploading... {{ uploadProgress }}%</p>
                  </div>
                </div>

                <!-- Processing Method -->
                <div>
                  <label class="block text-sm font-medium text-blue-900 mb-1">Processing Method</label>
                  <select v-model="refundOptions.processingMethod" class="w-full text-sm border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                    <option value="original-payment">Refund to Original Payment Method</option>
                    <option value="bank-transfer">Bank Transfer</option>
                    <option value="cash">Cash Refund</option>
                    <option value="store-credit">Store Credit</option>
                  </select>
                </div>
              </div>
            </div>

          

            <!-- Cancellation Reason Section -->
            <div class="mt-4 p-3 bg-purple-50 rounded-lg border border-purple-200">
              <h4 class="text-sm font-medium text-purple-900 mb-3">Cancellation Reason</h4>
              
              

              <!-- Custom Reason Text -->
              <div class="space-y-2">
                <label class="block text-xs font-medium text-purple-800">Detailed Explanation:</label>
                <textarea
                  v-model="cancellationReason.customMessage"
                  rows="3"
                  class="w-full text-sm border-gray-300 rounded-md focus:ring-purple-500 focus:border-purple-500"
                  placeholder="Provide additional details about the cancellation reason for the customer..."
                ></textarea>
              </div>

              <!-- Customer Notification Options -->
              <div class="mt-4 pt-3 border-t border-purple-200">
                <h5 class="text-xs font-medium text-purple-900 mb-2">Send Reason to Customer via:</h5>
                <div class="space-y-2">
                  <div class="flex items-start space-x-3">
                    <input
                      id="sendReasonEmail"
                      v-model="cancellationReason.sendViaEmail"
                      type="checkbox"
                      class="mt-1 h-4 w-4 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
                      checked
                    />
                    <div class="flex-1">
                      <label for="sendReasonEmail" class="text-xs font-medium text-purple-800">
                        Email Notification with Reason
                      </label>
                      <input
                        v-if="cancellationReason.sendViaEmail"
                        v-model="cancellationReason.customerEmail"
                        type="email"
                        placeholder="customer@example.com"
                        class="mt-1 block w-full text-xs border-gray-300 rounded-md focus:ring-purple-500 focus:border-purple-500"
                      />
                    </div>
                  </div>

                  <div class="flex items-start space-x-3">
                    <input
                      id="sendReasonSMS"
                      v-model="cancellationReason.sendViaSMS"
                      type="checkbox"
                      class="mt-1 h-4 w-4 text-purple-600 focus:ring-purple-500 border-gray-300 rounded"
                    />
                    <div class="flex-1">
                      <label for="sendReasonSMS" class="text-xs font-medium text-purple-800">
                        SMS Notification with Reason
                      </label>
                      <input
                        v-if="cancellationReason.sendViaSMS"
                        v-model="cancellationReason.customerPhone"
                        type="tel"
                        placeholder="+1 (555) 123-4567"
                        class="mt-1 block w-full text-xs border-gray-300 rounded-md focus:ring-purple-500 focus:border-purple-500"
                      />
                    </div>
                  </div>
                </div>

                <!-- Validation Message -->
                <div v-if="!cancellationReason.sendViaEmail && !cancellationReason.sendViaSMS && cancellationReason.selectedReason" class="text-xs text-amber-600 bg-amber-50 p-2 rounded border border-amber-200 mt-2">
                  <svg class="w-3 h-3 inline mr-1" fill="currentColor" viewBox="0 0 24 24">
                    <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-2h2v2zm0-4h-2V7h2v6z"/>
                  </svg>
                  Consider notifying the customer about the cancellation reason for better customer service.
                </div>
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
              <span>{{ isCancelling ? 'Cancelling & Processing...' : 'Cancel & Notify Customer' }}</span>
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Delete Confirmation Modal -->
    <div v-if="showDeleteModal" class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full z-50" @click="closeDeleteModal">
      <div class="relative top-20 mx-auto p-5 border w-96 shadow-lg rounded-md bg-white" @click.stop>
        <div class="mt-3">
          <div class="flex items-center justify-center mx-auto w-12 h-12 rounded-full bg-red-100">
            <svg class="w-6 h-6 text-red-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1-1H8a1 1 0 00-1 1v3M4 7h16"></path>
            </svg>
          </div>
          <h3 class="text-lg font-medium text-gray-900 text-center mt-4">Delete Booking</h3>
          <div class="mt-2 px-7 py-3">
            <p class="text-sm text-gray-500 text-center">
              Are you sure you want to delete this booking permanently? This action cannot be undone.
            </p>
            <div v-if="bookingToDelete" class="mt-4 p-3 bg-gray-50 rounded-lg text-gray-900">
              <div class="text-sm space-y-1">
                <div><strong>Booking ID:</strong> {{ bookingToDelete.id }}</div>
                <div><strong>Customer:</strong> {{ bookingToDelete.customerName }}</div>
                <div><strong>Product:</strong> {{ bookingToDelete.productName }}</div>
                <div><strong>Date:</strong> {{ bookingToDelete.date }}</div>
              </div>
            </div>
          </div>
          <div class="flex items-center justify-center pt-4 space-x-4">
            <button
              @click="closeDeleteModal"
              class="px-4 py-2 bg-gray-200 text-gray-800 text-sm font-medium rounded-md hover:bg-gray-300 transition-colors"
            >
              Cancel
            </button>
            <button
              @click="deleteBooking"
              :disabled="isDeleting"
              class="px-4 py-2 bg-red-600 text-white text-sm font-medium rounded-md hover:bg-red-700 transition-colors disabled:opacity-50 disabled:cursor-not-allowed flex items-center space-x-2"
            >
              <svg v-if="isDeleting" class="animate-spin -ml-1 mr-2 h-4 w-4 text-white" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              <span>{{ isDeleting ? 'Deleting...' : 'Delete' }}</span>
            </button>
          </div>
        </div>
      </div>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { mdiEye, mdiPencil, mdiCancel, mdiDelete } from '@mdi/js'

// State
const activeTab = ref('bookings')
const showDatePicker = ref(false)
const currentDate = ref(new Date())

// Delete modal state
const showDeleteModal = ref(false)
const bookingToDelete = ref<any>(null)
const isDeleting = ref(false)

// Cancel modal state
const showCancelModal = ref(false)
const bookingToCancel = ref<any>(null)
const isCancelling = ref(false)

// Refund and file upload state
const refundOptions = ref({
  isRefundApplicable: false,
  refundAmount: 0,
  processingMethod: 'original-payment',
  adminNotes: ''
})
const uploadedFile = ref<File | null>(null)
const isDragging = ref(false)
const uploadProgress = ref(0)
const fileInput = ref<HTMLInputElement | null>(null)

// Cancellation reason state
const cancellationReason = ref({
  selectedReason: '',
  customMessage: '',
  sendViaEmail: true,
  sendViaSMS: false,
  customerEmail: '',
  customerPhone: ''
})

// Tabs
const tabs = [
  { id: 'bookings', name: 'Bookings' },
  { id: 'history', name: 'History' }
]

// Filters
const filters = ref({
  startDate: '',
  endDate: '',
  location: '',
  productType: '',
  userType: ''
})

// Pagination
const currentPage = ref(1)
const itemsPerPage = 10

// Computed property for date range display
const dateRangeDisplay = computed(() => {
  if (filters.value.startDate && filters.value.endDate) {
    // Parse YYYY-MM-DD format and create dates with explicit local timezone
    const startParts = filters.value.startDate.split('-')
    const endParts = filters.value.endDate.split('-')
    const startDate = new Date(parseInt(startParts[0]), parseInt(startParts[1]) - 1, parseInt(startParts[2]))
    const endDate = new Date(parseInt(endParts[0]), parseInt(endParts[1]) - 1, parseInt(endParts[2]))
    return `${startDate.toLocaleDateString()} - ${endDate.toLocaleDateString()}`
  } else if (filters.value.startDate) {
    const startParts = filters.value.startDate.split('-')
    const startDate = new Date(parseInt(startParts[0]), parseInt(startParts[1]) - 1, parseInt(startParts[2]))
    
    return `${startDate.toLocaleDateString()}`
  }
  return ''
})

// Calendar computed properties
const currentMonthYear = computed(() => {
  return currentDate.value.toLocaleDateString('en-US', { month: 'long', year: 'numeric' })
})

const calendarDates = computed(() => {
  const year = currentDate.value.getFullYear()
  const month = currentDate.value.getMonth()
  
  const firstDay = new Date(year, month, 1)
  const lastDay = new Date(year, month + 1, 0)
  const startCalendar = new Date(firstDay)
  startCalendar.setDate(startCalendar.getDate() - firstDay.getDay())
  
  const dates = []
  const current = new Date(startCalendar)
  
  for (let i = 0; i < 42; i++) {
    const currentDate = new Date(current)
    const year = currentDate.getFullYear()
    const monthStr = (currentDate.getMonth() + 1).toString().padStart(2, '0')
    const day = currentDate.getDate().toString().padStart(2, '0')
    const dateString = `${year}-${monthStr}-${day}`
    
    dates.push({
      day: currentDate.getDate(),
      dateString: dateString,
      isCurrentMonth: currentDate.getMonth() === month,
      date: currentDate
    })
    current.setDate(current.getDate() + 1)
  }
  
  return dates
})

const calculateNights = computed(() => {
  if (filters.value.startDate && filters.value.endDate) {
    const start = new Date(filters.value.startDate)
    const end = new Date(filters.value.endDate)
    const diffTime = Math.abs(end.getTime() - start.getTime())
    return Math.ceil(diffTime / (1000 * 60 * 60 * 24))
  }
  return 0
})

// Sample bookings data - Using actual product details
const allBookings = ref([
  // Confirmed Bookings (Active)
  {
    id: 'BR-2034',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=100&h=100&fit=crop&crop=center',
    customerName: 'John Doe',
    userType: 'registered',
    date: '2025-08-20',
    startTime: '10:00 AM',
    endTime: '12:00 PM',
    duration: '2 hours',
    totalPrice: 100, // $50/hour * 2 hours
    status: 'confirmed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.'
  },
  {
    id: 'BR-2035',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=100&h=100&fit=crop&crop=center',
    customerName: 'Jane Smith',
    userType: 'guest',
    date: '2025-08-21',
    startTime: '9:00 AM',
    endTime: '5:00 PM',
    duration: '8 hours',
    totalPrice: 64, // $8/hour * 8 hours
    status: 'confirmed',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.'
  },
  {
    id: 'BR-2036',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=100&h=100&fit=crop&crop=center',
    customerName: 'Robert Chen',
    userType: 'registered',
    date: '2025-08-22',
    startTime: '2:00 PM',
    endTime: '4:00 PM',
    duration: '2 hours',
    totalPrice: 100, // $50/hour * 2 hours
    status: 'confirmed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.'
  },
  {
    id: 'BR-2037',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=100&h=100&fit=crop&crop=center',
    customerName: 'Emily Rodriguez',
    userType: 'registered',
    date: '2025-08-23',
    startTime: '8:00 AM',
    endTime: '12:00 PM',
    duration: '4 hours',
    totalPrice: 32, // $8/hour * 4 hours
    status: 'confirmed',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.'
  },
  {
    id: 'BR-2038',
    productName: 'Private Dedicated Desk',
    productType: 'Dedicated Desk',
    productId: 'PROD003',
    productImage: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=100&h=100&fit=crop&crop=center',
    customerName: 'David Kim',
    userType: 'guest',
    date: '2025-08-24',
    startTime: '9:00 AM',
    endTime: '6:00 PM',
    duration: '9 hours',
    totalPrice: 450, // Daily rate calculation
    status: 'confirmed',
    location: 'business-center',
    locationName: 'Business Center',
    companyName: 'Global Solutions Inc.'
  },
  {
    id: 'BR-2039',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=100&h=100&fit=crop&crop=center',
    customerName: 'Sophie Brown',
    userType: 'registered',
    date: '2025-08-25',
    startTime: '1:00 PM',
    endTime: '3:00 PM',
    duration: '2 hours',
    totalPrice: 100, // $50/hour * 2 hours
    status: 'confirmed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.'
  },
  {
    id: 'BR-2040',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=100&h=100&fit=crop&crop=center',
    customerName: 'Alex Johnson',
    userType: 'registered',
    date: '2025-08-26',
    startTime: '10:00 AM',
    endTime: '6:00 PM',
    duration: '8 hours',
    totalPrice: 64, // $8/hour * 8 hours
    status: 'confirmed',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.'
  },
  {
    id: 'BR-2041',
    productName: 'Private Dedicated Desk',
    productType: 'Dedicated Desk',
    productId: 'PROD003',
    productImage: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=100&h=100&fit=crop&crop=center',
    customerName: 'Maria Garcia',
    userType: 'guest',
    date: '2025-08-27',
    startTime: '8:00 AM',
    endTime: '5:00 PM',
    duration: '9 hours',
    totalPrice: 450, // Daily rate calculation
    status: 'confirmed',
    location: 'business-center',
    locationName: 'Business Center',
    companyName: 'Global Solutions Inc.'
  },

  // Completed Bookings (History)
  {
    id: 'BR-2020',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=100&h=100&fit=crop&crop=center',
    customerName: 'Mike Johnson',
    userType: 'registered',
    date: '2025-08-15',
    startTime: '9:00 AM',
    endTime: '11:00 AM',
    duration: '2 hours',
    totalPrice: 100, // $50/hour * 2 hours
    status: 'completed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.'
  },
  {
    id: 'BR-2021',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=100&h=100&fit=crop&crop=center',
    customerName: 'Lisa Thompson',
    userType: 'registered',
    date: '2025-08-16',
    startTime: '8:00 AM',
    endTime: '4:00 PM',
    duration: '8 hours',
    totalPrice: 64, // $8/hour * 8 hours
    status: 'completed',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.'
  },
  {
    id: 'BR-2022',
    productName: 'Private Dedicated Desk',
    productType: 'Dedicated Desk',
    productId: 'PROD003',
    productImage: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=100&h=100&fit=crop&crop=center',
    customerName: 'Mark Anderson',
    userType: 'guest',
    date: '2025-08-17',
    startTime: '10:00 AM',
    endTime: '6:00 PM',
    duration: '8 hours',
    totalPrice: 400, // Daily rate calculation
    status: 'completed',
    location: 'business-center',
    locationName: 'Business Center',
    companyName: 'Global Solutions Inc.'
  },
  {
    id: 'BR-2023',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=100&h=100&fit=crop&crop=center',
    customerName: 'Anna Martinez',
    userType: 'registered',
    date: '2025-08-18',
    startTime: '1:00 PM',
    endTime: '4:00 PM',
    duration: '3 hours',
    totalPrice: 150, // $50/hour * 3 hours
    status: 'completed',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.'
  },
  {
    id: 'BR-2024',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=100&h=100&fit=crop&crop=center',
    customerName: 'Grace Lee',
    userType: 'guest',
    date: '2025-08-14',
    startTime: '9:00 AM',
    endTime: '1:00 PM',
    duration: '4 hours',
    totalPrice: 32, // $8/hour * 4 hours
    status: 'completed',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.'
  },
  {
    id: 'BR-2025',
    productName: 'Private Dedicated Desk',
    productType: 'Dedicated Desk',
    productId: 'PROD003',
    productImage: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=100&h=100&fit=crop&crop=center',
    customerName: 'Kevin Brown',
    userType: 'registered',
    date: '2025-08-13',
    startTime: '8:00 AM',
    endTime: '5:00 PM',
    duration: '9 hours',
    totalPrice: 450, // Daily rate calculation
    status: 'completed',
    location: 'business-center',
    locationName: 'Business Center',
    companyName: 'Global Solutions Inc.'
  },

  // Cancelled Bookings (History)
  {
    id: 'BR-2010',
    productName: 'Executive Meeting Room',
    productType: 'Meeting Room',
    productId: 'PROD001',
    productImage: 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=100&h=100&fit=crop&crop=center',
    customerName: 'Sarah Wilson',
    userType: 'guest',
    date: '2025-08-12',
    startTime: '3:00 PM',
    endTime: '5:00 PM',
    duration: '2 hours',
    totalPrice: 100, // $50/hour * 2 hours
    status: 'cancelled',
    location: 'main-branch',
    locationName: 'Main Branch',
    companyName: 'Premium Co-working Ltd.'
  },
  {
    id: 'BR-2011',
    productName: 'Flexible Hot Desk',
    productType: 'Hot Desk',
    productId: 'PROD002',
    productImage: 'https://images.unsplash.com/photo-1497366216548-37526070297c?w=100&h=100&fit=crop&crop=center',
    customerName: 'James Wilson',
    userType: 'guest',
    date: '2025-08-11',
    startTime: '10:00 AM',
    endTime: '2:00 PM',
    duration: '4 hours',
    totalPrice: 32, // $8/hour * 4 hours
    status: 'cancelled',
    location: 'tech-hub',
    locationName: 'Tech Hub',
    companyName: 'Tech Innovations Ltd.'
  },
  {
    id: 'BR-2012',
    productName: 'Private Dedicated Desk',
    productType: 'Dedicated Desk',
    productId: 'PROD003',
    productImage: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=100&h=100&fit=crop&crop=center',
    customerName: 'Linda Davis',
    userType: 'registered',
    date: '2025-08-10',
    startTime: '9:00 AM',
    endTime: '5:00 PM',
    duration: '8 hours',
    totalPrice: 400, // Daily rate calculation
    status: 'cancelled',
    location: 'business-center',
    locationName: 'Business Center',
    companyName: 'Global Solutions Inc.'
  }
])

// Computed properties
const filteredBookings = computed(() => {
  let bookings = allBookings.value

  // Filter by tab
  if (activeTab.value === 'bookings') {
    bookings = bookings.filter(b => b.status === 'confirmed')
  } else {
    bookings = bookings.filter(b => b.status === 'completed' || b.status === 'cancelled')
  }

  // Apply date range filter
  if (filters.value.startDate && filters.value.endDate) {
    bookings = bookings.filter(b => {
      const bookingDate = b.date
      return bookingDate >= filters.value.startDate && bookingDate <= filters.value.endDate
    })
  } else if (filters.value.startDate) {
    bookings = bookings.filter(b => b.date >= filters.value.startDate)
  }

  // Apply other filters
  if (filters.value.location) {
    bookings = bookings.filter(b => b.location === filters.value.location)
  }
  if (filters.value.productType) {
    bookings = bookings.filter(b => b.productType.toLowerCase().replace(' ', '-') === filters.value.productType)
  }
  if (filters.value.userType) {
    bookings = bookings.filter(b => b.userType === filters.value.userType)
  }

  return bookings
})

const totalPages = computed(() => Math.ceil(filteredBookings.value.length / itemsPerPage))

const paginatedBookings = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage
  const end = start + itemsPerPage
  return filteredBookings.value.slice(start, end)
})

const startItem = computed(() => {
  if (filteredBookings.value.length === 0) return 0
  return (currentPage.value - 1) * itemsPerPage + 1
})

const endItem = computed(() => {
  const end = currentPage.value * itemsPerPage
  return Math.min(end, filteredBookings.value.length)
})

const visiblePages = computed(() => {
  const pages = []
  const start = Math.max(1, currentPage.value - 2)
  const end = Math.min(totalPages.value, currentPage.value + 2)
  
  for (let i = start; i <= end; i++) {
    pages.push(i)
  }
  return pages
})

// Public function to view booking details
const viewBookingDetails = (booking: any) => {
  // This is a public function that can be called to view booking details
  console.log('Viewing booking details for:', booking.id, booking)
  
  // Log the view action for analytics/audit purposes
  const viewLogs = JSON.parse(localStorage.getItem('bookingViewLogs') || '[]')
  viewLogs.push({
    bookingId: booking.id,
    viewedAt: new Date().toISOString(),
    viewedBy: 'Admin', // In real app, get from auth context
    bookingDetails: {
      customerName: booking.customerName,
      productName: booking.productName,
      status: booking.status,
      date: booking.date
    }
  })
  localStorage.setItem('bookingViewLogs', JSON.stringify(viewLogs))
  
  // Return the route path for navigation
  return `/bookings/${booking.id}`
}

// Public function to get all booking details
const getAllBookingDetails = () => {
  // Return all bookings with full details for external access
  console.log('Getting all booking details, total bookings:', allBookings.value.length)
  return allBookings.value.map(booking => ({
    ...booking,
    // Add computed fields for better detail view
    formattedDate: new Date(booking.date).toLocaleDateString('en-US', { 
      weekday: 'long', 
      year: 'numeric', 
      month: 'long', 
      day: 'numeric' 
    }),
    timeSlot: `${booking.startTime} - ${booking.endTime}`,
    statusColor: getStatusClass(booking.status),
    isUpcoming: booking.status === 'confirmed' && new Date(booking.date) > new Date(),
    isPast: booking.status === 'completed' || booking.status === 'cancelled' || new Date(booking.date) < new Date()
  }))
}

// Public function to get specific booking details by ID
const getBookingById = (bookingId: string) => {
  const booking = allBookings.value.find(b => b.id === bookingId)
  if (!booking) {
    console.warn('Booking not found:', bookingId)
    return null
  }
  
  // Return booking with enhanced details
  return {
    ...booking,
    formattedDate: new Date(booking.date).toLocaleDateString('en-US', { 
      weekday: 'long', 
      year: 'numeric', 
      month: 'long', 
      day: 'numeric' 
    }),
    timeSlot: `${booking.startTime} - ${booking.endTime}`,
    statusColor: getStatusClass(booking.status),
    isUpcoming: booking.status === 'confirmed' && new Date(booking.date) > new Date(),
    isPast: booking.status === 'completed' || booking.status === 'cancelled' || new Date(booking.date) < new Date(),
    canBeCancelled: booking.status === 'confirmed' && new Date(booking.date) > new Date(),
    canBeDeleted: booking.status === 'completed' || booking.status === 'cancelled'
  }
}

// Public function to add new booking and ensure it's viewable
const addNewBooking = (newBookingData: any) => {
  // Generate new booking ID
  const maxId = Math.max(...allBookings.value.map(b => parseInt(b.id.split('-')[1])))
  const newId = `BR-${(maxId + 1).toString().padStart(4, '0')}`
  
  const newBooking = {
    id: newId,
    ...newBookingData,
    status: 'confirmed', // New bookings are confirmed by default
    // Ensure all required fields are present
    productImage: newBookingData.productImage || 'https://images.unsplash.com/photo-1560179707-f14e90ef3623?w=100&h=100&fit=crop&crop=center'
  }
  
  // Add to bookings array
  allBookings.value.unshift(newBooking) // Add at beginning for recent bookings to show first
  
  // Save to localStorage for persistence
  localStorage.setItem('allBookings', JSON.stringify(allBookings.value))
  
  // Log the addition
  const additionLogs = JSON.parse(localStorage.getItem('bookingAdditionLogs') || '[]')
  additionLogs.push({
    bookingId: newId,
    addedAt: new Date().toISOString(),
    addedBy: 'Admin',
    bookingData: newBooking
  })
  localStorage.setItem('bookingAdditionLogs', JSON.stringify(additionLogs))
  
  console.log('New booking added:', newId, newBooking)
  
  // Return the new booking details
  return getBookingById(newId)
}

// Methods
const getTabCount = (tabId: string) => {
  if (tabId === 'bookings') {
    return allBookings.value.filter(b => b.status === 'confirmed').length
  } else {
    return allBookings.value.filter(b => b.status === 'completed' || b.status === 'cancelled').length
  }
}

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

const applyFilters = () => {
  // Filters are automatically applied through computed property
  console.log('Filters applied:', filters.value)
}

const resetFilters = () => {
  filters.value = {
    startDate: '',
    endDate: '',
    location: '',
    productType: '',
    userType: ''
  }
  currentPage.value = 1
}

// Pagination methods
const goToPage = (page: number) => {
  currentPage.value = page
}

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++
  }
}

const previousPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--
  }
}

const clearDateRange = () => {
  filters.value.startDate = ''
  filters.value.endDate = ''
}

// Calendar methods
const previousMonth = () => {
  currentDate.value = new Date(currentDate.value.getFullYear(), currentDate.value.getMonth() - 1, 1)
}

const nextMonth = () => {
  currentDate.value = new Date(currentDate.value.getFullYear(), currentDate.value.getMonth() + 1, 1)
}

const selectDate = (date: any) => {
  if (!date.isCurrentMonth) return
  
  // Use the properly formatted date string to avoid timezone issues
  const selectedDate = date.dateString
  
  if (!filters.value.startDate || (filters.value.startDate && filters.value.endDate)) {
    // Start new selection
    filters.value.startDate = selectedDate
    filters.value.endDate = ''
  } else if (filters.value.startDate && !filters.value.endDate) {
    // Select end date - compare as strings since they're in YYYY-MM-DD format
    if (selectedDate >= filters.value.startDate) {
      filters.value.endDate = selectedDate
    } else {
      // If selected date is before start date, make it the new start date
      filters.value.endDate = filters.value.startDate
      filters.value.startDate = selectedDate
    }
  }
}

const isDateSelected = (date: any) => {
  return date.dateString === filters.value.startDate || date.dateString === filters.value.endDate
}

const isDateInRange = (date: any) => {
  if (!filters.value.startDate || !filters.value.endDate) return false
  
  // Compare date strings directly since they're in YYYY-MM-DD format
  const dateString = date.dateString
  
  return dateString > filters.value.startDate && dateString < filters.value.endDate
}

// Delete booking functions
const confirmDeleteBooking = (booking: any) => {
  bookingToDelete.value = booking
  showDeleteModal.value = true
}

const closeDeleteModal = () => {
  showDeleteModal.value = false
  bookingToDelete.value = null
  isDeleting.value = false
}

// Cancel booking functions
const confirmCancelBooking = (booking: any) => {
  bookingToCancel.value = booking
  
  // Pre-populate customer contact information (in a real app, this would come from customer database)
  cancellationReason.value = {
    selectedReason: '',
    customMessage: '',
    sendViaEmail: true,
    sendViaSMS: false,
    customerEmail: `${booking.customerName.toLowerCase().replace(' ', '.')}@example.com`, // Mock email
    customerPhone: '+1 (555) ' + Math.floor(Math.random() * 900 + 100) + '-' + Math.floor(Math.random() * 9000 + 1000) // Mock phone
  }
  
  showCancelModal.value = true
}

const closeCancelModal = () => {
  showCancelModal.value = false
  bookingToCancel.value = null
  isCancelling.value = false
  resetRefundForm()
}

const cancelBooking = async () => {
  if (!bookingToCancel.value) return
  
  isCancelling.value = true
  
  try {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // Update booking status to cancelled
    const booking = allBookings.value.find(b => b.id === bookingToCancel.value.id)
    if (booking) {
      booking.status = 'cancelled'
    }
    
    // Save to localStorage for persistence
    const bookingStatuses = JSON.parse(localStorage.getItem('bookingStatuses') || '{}')
    bookingStatuses[bookingToCancel.value.id] = 'cancelled'
    localStorage.setItem('bookingStatuses', JSON.stringify(bookingStatuses))
    
    // Send customer notifications with cancellation reason if configured
    const notificationResults = await sendCancellationNotifications()
    
    // Log the cancellation with refund information and reason
    const cancelledBookings = JSON.parse(localStorage.getItem('cancelledBookings') || '[]')
    const cancellationData = {
      ...bookingToCancel.value,
      cancelledAt: new Date().toISOString(),
      cancelledBy: 'Admin', // In real app, get from auth context
      cancellationReason: {
        reason: cancellationReason.value.selectedReason,
        customMessage: cancellationReason.value.customMessage,
        reasonSentToCustomer: cancellationReason.value.sendViaEmail || cancellationReason.value.sendViaSMS
      },
      notifications: notificationResults,
      refundData: refundOptions.value.isRefundApplicable ? {
        refundAmount: refundOptions.value.refundAmount,
        processingMethod: refundOptions.value.processingMethod,
        adminNotes: refundOptions.value.adminNotes,
        hasPaymentSlip: uploadedFile.value ? true : false,
        paymentSlipName: uploadedFile.value?.name || null,
        refundProcessedAt: new Date().toISOString()
      } : null
    }
    
    cancelledBookings.push(cancellationData)
    localStorage.setItem('cancelledBookings', JSON.stringify(cancelledBookings))
    
    // If refund is applicable, process the refund
    if (refundOptions.value.isRefundApplicable && refundOptions.value.refundAmount > 0) {
      console.log('Processing refund:', {
        bookingId: bookingToCancel.value.id,
        amount: refundOptions.value.refundAmount,
        method: refundOptions.value.processingMethod,
        paymentSlip: uploadedFile.value?.name
      })
      
      // In a real application, this would call the payment gateway's refund API
      // For now, we'll just log it and store the refund details
      const refunds = JSON.parse(localStorage.getItem('refunds') || '[]')
      refunds.push({
        id: Date.now().toString(),
        bookingId: bookingToCancel.value.id,
        customerId: bookingToCancel.value.customerId,
        customerName: bookingToCancel.value.customerName,
        amount: refundOptions.value.refundAmount,
        processingMethod: refundOptions.value.processingMethod,
        adminNotes: refundOptions.value.adminNotes,
        paymentSlipUploaded: uploadedFile.value ? true : false,
        status: 'pending',
        createdAt: new Date().toISOString(),
        processedBy: 'Admin'
      })
      localStorage.setItem('refunds', JSON.stringify(refunds))
    }
    
    closeCancelModal()
    
    // Show success message with notification and refund details
    const notificationSummary = []
    if (notificationResults.email?.success) notificationSummary.push('email')
    if (notificationResults.sms?.success) notificationSummary.push('SMS')
    
    const refundMessage = refundOptions.value.isRefundApplicable && refundOptions.value.refundAmount > 0 
      ? ` with $${refundOptions.value.refundAmount} refund via ${refundOptions.value.processingMethod}` 
      : ''
      
    const notificationMessage = notificationSummary.length > 0 
      ? `. Customer notified about cancellation reason via ${notificationSummary.join(' and ')}.`
      : '. No customer notification sent.'
    
    const message = `Booking cancelled successfully: ${bookingToCancel.value.id}${refundMessage}${notificationMessage}`
    console.log(message)
    
    // In a real application, you would show a toast notification here
    alert(message)
    
  } catch (error) {
    console.error('Error cancelling booking:', error)
    // Handle error (show toast notification, etc.)
  } finally {
    isCancelling.value = false
  }
}

const deleteBooking = async () => {
  if (!bookingToDelete.value) return
  
  isDeleting.value = true
  
  try {
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // Remove booking from the array
    const index = allBookings.value.findIndex(b => b.id === bookingToDelete.value.id)
    if (index > -1) {
      allBookings.value.splice(index, 1)
    }
    
    // Save to localStorage for persistence
    localStorage.setItem('bookings', JSON.stringify(allBookings.value))
    
    // Log the deletion
    const deletedBookings = JSON.parse(localStorage.getItem('deletedBookings') || '[]')
    deletedBookings.push({
      ...bookingToDelete.value,
      deletedAt: new Date().toISOString(),
      deletedBy: 'Admin' // In real app, get from auth context
    })
    localStorage.setItem('deletedBookings', JSON.stringify(deletedBookings))
    
    closeDeleteModal()
    
    // Show success message (you could replace this with a toast notification)
    console.log('Booking deleted successfully:', bookingToDelete.value.id)
    
  } catch (error) {
    console.error('Error deleting booking:', error)
    // Handle error (show toast notification, etc.)
  } finally {
    isDeleting.value = false
  }
}

// File upload methods for refund processing
const triggerFileInput = () => {
  fileInput.value?.click()
}

const handleFileSelect = (event: Event) => {
  const target = event.target as HTMLInputElement
  const file = target.files?.[0]
  if (file && validateFile(file)) {
    uploadedFile.value = file
  }
}

const handleFileDrop = (event: DragEvent) => {
  event.preventDefault()
  isDragging.value = false
  
  const files = event.dataTransfer?.files
  const file = files?.[0]
  
  if (file && validateFile(file)) {
    uploadedFile.value = file
    simulateFileUpload()
  }
}

const validateFile = (file: File): boolean => {
  const maxSize = 5 * 1024 * 1024 // 5MB
  const allowedTypes = ['image/png', 'image/jpeg', 'image/jpg', 'application/pdf']
  
  if (file.size > maxSize) {
    alert('File size must be less than 5MB')
    return false
  }
  
  if (!allowedTypes.includes(file.type)) {
    alert('Only PNG, JPG, and PDF files are allowed')
    return false
  }
  
  return true
}

const simulateFileUpload = () => {
  uploadProgress.value = 0
  const interval = setInterval(() => {
    uploadProgress.value += 10
    if (uploadProgress.value >= 100) {
      clearInterval(interval)
      setTimeout(() => {
        uploadProgress.value = 0
      }, 1000)
    }
  }, 100)
}

const removeFile = () => {
  uploadedFile.value = null
  uploadProgress.value = 0
  if (fileInput.value) {
    fileInput.value.value = ''
  }
}

const resetRefundForm = () => {
  refundOptions.value = {
    isRefundApplicable: false,
    refundAmount: 0,
    processingMethod: 'original-payment',
    adminNotes: ''
  }
  uploadedFile.value = null
  uploadProgress.value = 0
  isDragging.value = false
  if (fileInput.value) {
    fileInput.value.value = ''
  }
  
  // Reset cancellation reason
  cancellationReason.value = {
    selectedReason: '',
    customMessage: '',
    sendViaEmail: true,
    sendViaSMS: false,
    customerEmail: '',
    customerPhone: ''
  }
}

// Customer notification functions for cancellation reasons
const sendCancellationNotifications = async () => {
  const results: any = {}
  
  // Only send notifications if a reason is selected and customer wants notifications
  if (!cancellationReason.value.selectedReason) {
    return results
  }
  
  // Send email notification with reason
  if (cancellationReason.value.sendViaEmail && cancellationReason.value.customerEmail) {
    try {
      results.email = await sendCancellationEmailNotification()
    } catch (error) {
      console.error('Failed to send cancellation email notification:', error)
      results.email = { success: false, error: String(error) }
    }
  }
  
  // Send SMS notification with reason
  if (cancellationReason.value.sendViaSMS && cancellationReason.value.customerPhone) {
    try {
      results.sms = await sendCancellationSMSNotification()
    } catch (error) {
      console.error('Failed to send cancellation SMS notification:', error)
      results.sms = { success: false, error: String(error) }
    }
  }
  
  return results
}

const sendCancellationEmailNotification = async () => {
  // Simulate email API call
  const emailData = {
    to: cancellationReason.value.customerEmail,
    subject: `Booking Cancellation Notice - ${bookingToCancel.value?.id}`,
    html: generateCancellationEmailHTML(),
    from: 'noreply@coworking.com'
  }
  
  console.log('Sending cancellation email notification:', emailData)
  
  // Simulate API delay
  await new Promise(resolve => setTimeout(resolve, 800))
  
  return {
    success: true,
    messageId: `cancel_email_${Date.now()}`,
    sentAt: new Date().toISOString(),
    recipient: emailData.to,
    reason: cancellationReason.value.selectedReason
  }
}

const sendCancellationSMSNotification = async () => {
  // Simulate SMS API call
  const smsData = {
    to: cancellationReason.value.customerPhone,
    message: generateCancellationSMSMessage(),
    from: '+1-555-COWORK'
  }
  
  console.log('Sending cancellation SMS notification:', smsData)
  
  // Simulate API delay
  await new Promise(resolve => setTimeout(resolve, 600))
  
  return {
    success: true,
    messageId: `cancel_sms_${Date.now()}`,
    sentAt: new Date().toISOString(),
    recipient: smsData.to,
    reason: cancellationReason.value.selectedReason
  }
}

const generateCancellationEmailHTML = () => {
  const booking = bookingToCancel.value
  const reason = cancellationReason.value
  
  const reasonTexts: { [key: string]: string } = {
    'facility-maintenance': 'Due to scheduled facility maintenance and repairs',
    'equipment-failure': 'Due to unexpected equipment failure',
    'overbooking': 'Due to an overbooking situation',
    'emergency-closure': 'Due to emergency closure',
    'policy-violation': 'Due to policy violation',
    'payment-issue': 'Due to payment processing issues',
    'staff-shortage': 'Due to staff shortage',
    'weather-conditions': 'Due to severe weather conditions',
    'customer-request': 'As per your request',
    'other': 'Due to unforeseen circumstances'
  }
  
  const reasonText = reasonTexts[reason.selectedReason] || 'Due to unforeseen circumstances'
  const refundText = refundOptions.value.isRefundApplicable 
    ? `<p><strong>Refund Information:</strong> A refund of $${refundOptions.value.refundAmount} will be processed via ${refundOptions.value.processingMethod} within 3-5 business days.</p>`
    : ''
  
  return `
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8">
      <title>Booking Cancellation Notice</title>
      <style>
        body { font-family: Arial, sans-serif; line-height: 1.6; color: #333; }
        .container { max-width: 600px; margin: 0 auto; padding: 20px; }
        .header { background: #f8f9fa; padding: 20px; border-radius: 8px; margin-bottom: 20px; }
        .booking-details { background: #fff; border: 1px solid #dee2e6; padding: 15px; border-radius: 8px; margin: 20px 0; }
        .reason-box { background: #fff3cd; border: 1px solid #ffeaa7; padding: 15px; border-radius: 8px; margin: 20px 0; }
        .footer { margin-top: 30px; padding-top: 20px; border-top: 1px solid #dee2e6; color: #6c757d; font-size: 14px; }
        .alert { background: #f8d7da; color: #721c24; padding: 15px; border-radius: 8px; margin: 20px 0; }
      </style>
    </head>
    <body>
      <div class="container">
        <div class="header">
          <h2>Booking Cancellation Notice</h2>
        </div>
        
        <div class="alert">
          <strong>Your booking has been cancelled</strong>
        </div>
        
        <p>Dear ${booking?.customerName},</p>
        
        <p>We regret to inform you that your booking has been cancelled ${reasonText}.</p>
        
        <div class="reason-box">
          <h3>Cancellation Reason:</h3>
          <p><strong>${reason.selectedReason.replace('-', ' ').replace(/\b\w/g, l => l.toUpperCase())}</strong></p>
          ${reason.customMessage ? `<p>${reason.customMessage}</p>` : ''}
        </div>
        
        <div class="booking-details">
          <h3>Cancelled Booking Details:</h3>
          <ul style="list-style: none; padding: 0;">
            <li><strong>Booking ID:</strong> ${booking?.id}</li>
            <li><strong>Product:</strong> ${booking?.productName}</li>
            <li><strong>Date:</strong> ${booking?.date}</li>
            <li><strong>Time:</strong> ${booking?.startTime} - ${booking?.endTime}</li>
            <li><strong>Location:</strong> ${booking?.locationName}</li>
            <li><strong>Amount:</strong> $${booking?.totalPrice}</li>
          </ul>
        </div>
        
        ${refundText}
        
        <p>We sincerely apologize for any inconvenience this may cause. If you have any questions or would like to make a new booking, please don't hesitate to contact us.</p>
        
        <div class="footer">
          <p>Thank you for your understanding,<br>
          CoWorking Space Team<br>
          Email: support@coworking.com<br>
          Phone: +1 (555) 123-WORK</p>
        </div>
      </div>
    </body>
    </html>
  `
}

const generateCancellationSMSMessage = () => {
  const booking = bookingToCancel.value
  const reason = cancellationReason.value
  
  const reasonTexts: { [key: string]: string } = {
    'facility-maintenance': 'due to facility maintenance',
    'equipment-failure': 'due to equipment failure',
    'overbooking': 'due to overbooking',
    'emergency-closure': 'due to emergency closure',
    'policy-violation': 'due to policy violation',
    'payment-issue': 'due to payment issue',
    'staff-shortage': 'due to staff shortage',
    'weather-conditions': 'due to weather conditions',
    'customer-request': 'as requested',
    'other': 'due to unforeseen circumstances'
  }
  
  const reasonText = reasonTexts[reason.selectedReason] || 'due to unforeseen circumstances'
  let message = `Hi ${booking?.customerName}, your booking ${booking?.id} for ${booking?.productName} on ${booking?.date} has been cancelled ${reasonText}.`
  
  if (refundOptions.value.isRefundApplicable && refundOptions.value.refundAmount > 0) {
    message += ` Refund of $${refundOptions.value.refundAmount} will be processed within 3-5 days.`
  }
  
  if (reason.customMessage && reason.customMessage.length < 50) {
    message += ` ${reason.customMessage}`
  }
  
  message += ` For questions, call +1-555-123-WORK. We apologize for the inconvenience. -CoWorking Space`
  
  return message
}

// Click outside handler
const handleClickOutside = (event: MouseEvent) => {
  const target = event.target as HTMLElement
  const datePickerContainer = target.closest('.date-picker-container')
  const dateInput = target.closest('.date-input')
  
  if (!datePickerContainer && !dateInput && showDatePicker.value) {
    showDatePicker.value = false
  }
}

// Lifecycle hooks
onMounted(() => {
  document.addEventListener('click', handleClickOutside)
  
  // Load bookings from localStorage if available (for persistence across sessions)
  const savedBookings = localStorage.getItem('allBookings')
  if (savedBookings) {
    try {
      const parsedBookings = JSON.parse(savedBookings)
      if (Array.isArray(parsedBookings) && parsedBookings.length > 0) {
        allBookings.value = parsedBookings
        console.log('Loaded bookings from localStorage:', parsedBookings.length)
      }
    } catch (error) {
      console.warn('Error loading bookings from localStorage:', error)
    }
  }
  
  // Update booking statuses from localStorage (simulate real-time updates)
  const bookingStatuses = JSON.parse(localStorage.getItem('bookingStatuses') || '{}')
  allBookings.value.forEach(booking => {
    if (bookingStatuses[booking.id]) {
      booking.status = bookingStatuses[booking.id]
    }
  })
  
  console.log('BookingsView mounted with', allBookings.value.length, 'total bookings')
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})

// Expose public functions for external access
// These functions can be called from parent components or other parts of the application
defineExpose({
  viewBookingDetails,
  getAllBookingDetails,
  getBookingById,
  addNewBooking,
  // Expose filtered bookings for external components
  getCurrentBookings: () => filteredBookings.value,
  // Expose booking management functions
  confirmCancelBooking,
  confirmDeleteBooking
})
</script>
