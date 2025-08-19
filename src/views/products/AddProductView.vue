<template>
  <AdminLayout>
    <div class="space-y-6">
      <!-- Header with Back Button -->
      <div class="flex items-center justify-between">
        <div class="flex items-center space-x-4">
          <router-link to="/products" class="p-2 rounded-lg border border-gray-300 hover:bg-gray-50">
            <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
            </svg>
          </router-link>
          <div>
            <h1 class="text-xl font-bold text-gray-900">Add New Product</h1>
          </div>
        </div>
      </div>

      <!-- Form Content -->
      <div class="max-w-6xl mx-auto">
        <form @submit.prevent="saveProduct" class="space-y-6">
          <!-- Single Card with All Sections -->
          <div class="bg-white rounded-xl shadow-card overflow-hidden">
            <div class="p-8 space-y-8">
              
              <!-- Company and Location Selection -->
              <div>
                <h2 class="text-lg font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-3" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiOfficeBuilding" />
                  </svg>
                  Company & Location
                </h2>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Select Company <span class="text-red-500">*</span>
                    </label>
                    <select v-model="form.companyId" required
                      @change="onCompanyChange"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900">
                      <option value="">Choose a company</option>
                      <option v-for="company in companies" :key="company.id" :value="company.id">
                        {{ company.name }}
                      </option>
                    </select>
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Select Location <span class="text-red-500">*</span>
                    </label>
                    <select v-model="form.locationId" required
                      :disabled="!form.companyId"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900 disabled:bg-gray-100 disabled:cursor-not-allowed">
                      <option value="">Choose a location</option>
                      <option v-for="location in availableLocations" :key="location.id" :value="location.id">
                        {{ location.name }} - {{ location.address }}
                      </option>
                    </select>
                  </div>
                </div>
              </div>

              <!-- Product Details -->
              <div>
                <h2 class="text-lg font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-3" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiPackageVariant" />
                  </svg>
                  Product Details
                </h2>
                
                <div class="space-y-6">
                  <!-- Product Type -->
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Product Type <span class="text-red-500">*</span>
                    </label>
                    <select v-model="form.type" required
                      @change="onProductTypeChange"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900">
                      <option value="">Select product type</option>
                      <option value="Meeting Room">Meeting Room</option>
                      <option value="Hot Desk">Hot Desk</option>
                      <option value="Dedicated Desk">Dedicated Desk</option>
                    </select>
                  </div>

                  <!-- Images Upload -->
                  <div v-if="form.type">
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Product Images
                    </label>
                    <div class="border-2 border-dashed border-gray-300 rounded-lg p-6 text-center hover:border-gray-400 transition-colors">
                      <svg class="mx-auto h-12 w-12 text-gray-400" stroke="currentColor" fill="none" viewBox="0 0 48 48">
                        <path d="M28 8H12a4 4 0 00-4 4v20m32-12v8m0 0v8a4 4 0 01-4 4H12a4 4 0 01-4-4v-4m32-4l-3.172-3.172a4 4 0 00-5.656 0L28 28M8 32l9.172-9.172a4 4 0 015.656 0L28 28m0 0l4 4m4-24h8m-4-4v8m-12 4h.02" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
                      </svg>
                      <div class="mt-4">
                        <label for="file-upload" class="cursor-pointer">
                          <span class="mt-2 block text-sm font-medium text-gray-900">Upload product images</span>
                          <span class="mt-1 block text-sm text-gray-500">PNG, JPG, GIF up to 10MB</span>
                        </label>
                        <input id="file-upload" name="file-upload" type="file" multiple accept="image/*" class="sr-only" @change="handleImageUpload" />
                      </div>
                    </div>
                    <div v-if="form.images.length > 0" class="mt-4 grid grid-cols-2 md:grid-cols-4 gap-4">
                      <div v-for="(image, index) in form.images" :key="index" class="relative">
                        <img :src="image" :alt="`Product image ${index + 1}`" class="w-full h-32 object-cover rounded-lg border border-gray-200">
                        <button type="button" @click="removeImage(index)" class="absolute -top-2 -right-2 bg-red-500 text-white rounded-full w-6 h-6 flex items-center justify-center text-xs hover:bg-red-600">
                          ×
                        </button>
                      </div>
                    </div>
                  </div>

                  <!-- Name and Description -->
                  <div v-if="form.type" class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">
                        Product Name <span class="text-red-500">*</span>
                      </label>
                      <input type="text" v-model="form.name" required
                        class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                        placeholder="Enter product name" />
                    </div>
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">
                        Max Seating Capacity <span class="text-red-500">*</span>
                      </label>
                      <input type="number" v-model.number="form.maxSeatingCapacity" required min="1"
                        class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                        placeholder="Enter capacity" />
                    </div>
                  </div>

                  <div v-if="form.type">
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Description
                    </label>
                    <textarea v-model="form.description" rows="3"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                      placeholder="Enter product description"></textarea>
                  </div>
                </div>
              </div>

              <!-- Pricing Section - Dynamic based on product type -->
              <div v-if="form.type">
                <h2 class="text-lg font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-3" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiCurrencyUsd" />
                  </svg>
                  Pricing
                </h2>
                
                <!-- Meeting Room Pricing -->
                <div v-if="form.type === 'Meeting Room'" class="space-y-4">
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Price per Hour <span class="text-red-500">*</span>
                    </label>
                    <div class="relative">
                      <span class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-500">$</span>
                      <input type="number" v-model.number="form.pricePerHour" required step="0.01" min="0"
                        class="w-full border border-gray-300 rounded-lg pl-8 pr-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                        placeholder="0.00" />
                    </div>
                  </div>
                </div>

                <!-- Hot Desk Pricing -->
                <div v-if="form.type === 'Hot Desk'" class="space-y-4">
                  <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">
                        Price per Hour <span class="text-red-500">*</span>
                      </label>
                      <div class="relative">
                        <span class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-500">$</span>
                        <input type="number" v-model.number="form.pricePerHour" required step="0.01" min="0"
                          class="w-full border border-gray-300 rounded-lg pl-8 pr-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                          placeholder="0.00" />
                      </div>
                    </div>
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">
                        Price per Day <span class="text-red-500">*</span>
                      </label>
                      <div class="relative">
                        <span class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-500">$</span>
                        <input type="number" v-model.number="form.pricePerDay" required step="0.01" min="0"
                          class="w-full border border-gray-300 rounded-lg pl-8 pr-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                          placeholder="0.00" />
                      </div>
                    </div>
                  </div>
                </div>

                <!-- Dedicated Desk Pricing -->
                <div v-if="form.type === 'Dedicated Desk'" class="space-y-4">
                  <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">
                        Price per Month <span class="text-red-500">*</span>
                      </label>
                      <div class="relative">
                        <span class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-500">$</span>
                        <input type="number" v-model.number="form.pricePerMonth" required step="0.01" min="0"
                          class="w-full border border-gray-300 rounded-lg pl-8 pr-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                          placeholder="0.00" />
                      </div>
                    </div>
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">
                        Price per Year <span class="text-red-500">*</span>
                      </label>
                      <div class="relative">
                        <span class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-500">$</span>
                        <input type="number" v-model.number="form.pricePerYear" required step="0.01" min="0"
                          class="w-full border border-gray-300 rounded-lg pl-8 pr-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                          placeholder="0.00" />
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Operating Hours -->
              <div v-if="form.type">
                <h2 class="text-lg font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-3" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiClockOutline" />
                  </svg>
                  Open Days & Hours
                </h2>
                
                <div class="space-y-6">
                  <!-- Open Days -->
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Open Days <span class="text-red-500">*</span>
                    </label>
                    <div class="grid grid-cols-3 md:grid-cols-7 gap-2">
                      <label v-for="day in daysOfWeek" :key="day" class="flex items-center space-x-2 p-2 border rounded-lg hover:bg-gray-50 cursor-pointer">
                        <input type="checkbox" :value="day" v-model="form.openDays" class="rounded border-gray-300 text-primary-600 focus:ring-primary-500">
                        <span class="text-sm text-gray-700">{{ day.substring(0, 3) }}</span>
                      </label>
                    </div>
                  </div>

                  <!-- Open Hours -->
                  <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">
                        Opening Time <span class="text-red-500">*</span>
                      </label>
                      <input type="time" v-model="form.openHours.start" required
                        class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900" />
                    </div>
                    <div>
                      <label class="block text-sm font-medium text-gray-700 mb-2">
                        Closing Time <span class="text-red-500">*</span>
                      </label>
                      <input type="time" v-model="form.openHours.end" required
                        class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900" />
                    </div>
                  </div>
                </div>
              </div>

              <!-- Facilities -->
              <div v-if="form.type">
                <h2 class="text-lg font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-3" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiCog" />
                  </svg>
                  Facilities
                </h2>
                
                <div class="space-y-6">
                  <!-- Default Included Facilities -->
                  <div>
                    <div class="flex items-center justify-between mb-3">
                      <label class="block text-sm font-medium text-gray-700">
                        Default Included Facilities (Free)
                      </label>
                      <button type="button" @click="addDefaultFacility"
                        class="px-3 py-1 text-sm bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors">
                        + Add Facility
                      </button>
                    </div>
                    
                    <div class="space-y-3">
                      <div v-for="(selectedFacility, index) in form.defaultFacilities" :key="index"
                           class="grid grid-cols-1 md:grid-cols-2 gap-4 p-4 border border-gray-200 rounded-lg">
                        <div>
                          <label class="block text-sm font-medium text-gray-700 mb-1">Facility</label>
                          <select v-model="form.defaultFacilities[index]" required
                            class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900">
                            <option value="">Select facility</option>
                            <option v-for="facility in availableFacilities" :key="facility.id" :value="facility.name">
                              {{ facility.name }}
                            </option>
                          </select>
                        </div>
                        <div class="flex items-end">
                          <button type="button" @click="removeDefaultFacility(index)"
                            class="w-full px-3 py-2 text-sm text-red-600 border border-red-300 rounded-lg hover:bg-red-50 transition-colors">
                            Remove
                          </button>
                        </div>
                      </div>
                      
                      <div v-if="form.defaultFacilities.length === 0" class="text-center py-8 text-gray-500">
                        <svg class="mx-auto h-12 w-12 text-gray-400 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" />
                        </svg>
                        <p>No default facilities added yet.</p>
                        <p class="text-sm mt-1">Click "Add Facility" to include free facilities.</p>
                      </div>
                    </div>
                  </div>

                  <!-- Additional Facilities with Pricing -->
                  <div>
                    <div class="flex items-center justify-between mb-3">
                      <label class="block text-sm font-medium text-gray-700">
                        Additional Facilities (Paid)
                      </label>
                      <button type="button" @click="addAdditionalFacility"
                        class="px-3 py-1 text-sm bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors">
                        + Add Facility
                      </button>
                    </div>
                    
                    <div class="space-y-3">
                      <div v-for="(additionalFacility, index) in form.additionalFacilities" :key="index"
                           class="grid grid-cols-1 md:grid-cols-3 gap-4 p-4 border border-gray-200 rounded-lg">
                        <div>
                          <label class="block text-sm font-medium text-gray-700 mb-1">Facility</label>
                          <select v-model="additionalFacility.name" required
                            class="w-full border border-gray-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900">
                            <option value="">Select facility</option>
                            <option v-for="facility in availableFacilities" :key="facility.id" :value="facility.name">
                              {{ facility.name }}
                            </option>
                          </select>
                        </div>
                        <div>
                          <label class="block text-sm font-medium text-gray-700 mb-1">Price per Hour</label>
                          <div class="relative">
                            <span class="absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-500">$</span>
                            <input type="number" v-model.number="additionalFacility.pricePerHour" required step="0.01" min="0"
                              class="w-full border border-gray-300 rounded-lg pl-8 pr-4 py-2 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                              placeholder="0.00" />
                          </div>
                        </div>
                        <div class="flex items-end">
                          <button type="button" @click="removeAdditionalFacility(index)"
                            class="w-full px-3 py-2 text-sm text-red-600 border border-red-300 rounded-lg hover:bg-red-50 transition-colors">
                            Remove
                          </button>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Action Buttons -->
          <div class="flex justify-between items-center pt-6">
            <button type="button" @click="addAnotherProduct"
              class="px-6 py-3 border border-primary-600 text-primary-600 rounded-lg hover:bg-primary-50 transition-colors flex items-center space-x-2">
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
              </svg>
              <span>Add Another Product</span>
            </button>
            
            <div class="flex space-x-4">
              <router-link to="/products"
                class="px-6 py-3 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors">
                Cancel
              </router-link>
              <button type="submit" :disabled="!isFormValid"
                class="px-6 py-3 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors disabled:opacity-50 disabled:cursor-not-allowed flex items-center space-x-2">
                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
                </svg>
                <span>Create Product</span>
              </button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import { useRouter } from 'vue-router'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { 
  mdiOfficeBuilding, 
  mdiPackageVariant, 
  mdiCurrencyUsd, 
  mdiClockOutline, 
  mdiCog 
} from '@mdi/js'

const router = useRouter()

// Days of the week
const daysOfWeek = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

// Sample companies data
const companies = ref([
  { id: 'COMP001', name: 'ABC Corporation' },
  { id: 'COMP002', name: 'XYZ Industries' },
  { id: 'COMP003', name: 'Global Solutions Inc.' },
  { id: 'COMP004', name: 'Tech Innovations Ltd.' },
  { id: 'COMP005', name: 'Future Systems' }
])

// Sample locations data (filtered by company)
const locations = ref([
  { id: 'LOC001', companyId: 'COMP001', name: 'Downtown Office', address: '123 Business St' },
  { id: 'LOC002', companyId: 'COMP001', name: 'Tech Hub', address: '456 Innovation Ave' },
  { id: 'LOC003', companyId: 'COMP002', name: 'Business Center', address: '789 Corporate Blvd' },
  { id: 'LOC004', companyId: 'COMP003', name: 'Innovation Park', address: '321 Future Dr' },
  { id: 'LOC005', companyId: 'COMP004', name: 'Startup Hub', address: '654 Enterprise St' },
  { id: 'LOC006', companyId: 'COMP005', name: 'Creative Space', address: '987 Design Ave' }
])

// Available facilities from FacilitiesView
const availableFacilities = ref([
  { id: 'FC-001', name: 'High-Speed WiFi' },
  { id: 'FC-002', name: 'Projector & Screen' },
  { id: 'FC-003', name: 'Whiteboard' },
  { id: 'FC-004', name: 'Video Conferencing Setup' },
  { id: 'FC-005', name: 'Coffee & Refreshments' },
  { id: 'FC-006', name: 'Printing Services' },
  { id: 'FC-007', name: 'Climate Control' },
  { id: 'FC-008', name: 'Parking Space' }
])

// Form data
const form = ref({
  companyId: '',
  locationId: '',
  type: '',
  images: [] as string[],
  name: '',
  description: '',
  maxSeatingCapacity: 1,
  pricePerHour: 0,
  pricePerDay: 0,
  pricePerMonth: 0,
  pricePerYear: 0,
  openDays: [] as string[],
  openHours: {
    start: '09:00',
    end: '17:00'
  },
  defaultFacilities: [] as string[],
  additionalFacilities: [] as Array<{
    name: string
    pricePerHour: number
  }>,
  status: 'active'
})

// Computed properties
const availableLocations = computed(() => {
  return locations.value.filter(location => location.companyId === form.value.companyId)
})

const isFormValid = computed(() => {
  const basicValidation = form.value.companyId && 
                         form.value.locationId && 
                         form.value.type && 
                         form.value.name.trim() && 
                         form.value.maxSeatingCapacity > 0 &&
                         form.value.openDays.length > 0

  if (!basicValidation) return false

  // Type-specific pricing validation
  switch (form.value.type) {
    case 'Meeting Room':
      return form.value.pricePerHour > 0
    case 'Hot Desk':
      return form.value.pricePerHour > 0 && form.value.pricePerDay > 0
    case 'Dedicated Desk':
      return form.value.pricePerMonth > 0 && form.value.pricePerYear > 0
    default:
      return false
  }
})

// Watchers
watch(() => form.value.companyId, (newCompanyId) => {
  if (newCompanyId) {
    form.value.locationId = ''
  }
})

// Methods
const onCompanyChange = () => {
  form.value.locationId = ''
}

const onProductTypeChange = () => {
  // Reset pricing fields when product type changes
  form.value.pricePerHour = 0
  form.value.pricePerDay = 0
  form.value.pricePerMonth = 0
  form.value.pricePerYear = 0
}

const handleImageUpload = (event: any) => {
  const files = event.target.files
  if (files) {
    for (let i = 0; i < files.length; i++) {
      const file = files[i]
      const reader = new FileReader()
      reader.onload = (e) => {
        if (e.target?.result) {
          form.value.images.push(e.target.result as string)
        }
      }
      reader.readAsDataURL(file)
    }
  }
}

const removeImage = (index: number) => {
  form.value.images.splice(index, 1)
}

const addAdditionalFacility = () => {
  form.value.additionalFacilities.push({
    name: '',
    pricePerHour: 0
  })
}

const removeAdditionalFacility = (index: number) => {
  form.value.additionalFacilities.splice(index, 1)
}

const addDefaultFacility = () => {
  form.value.defaultFacilities.push('')
}

const removeDefaultFacility = (index: number) => {
  form.value.defaultFacilities.splice(index, 1)
}

const resetForm = () => {
  form.value = {
    companyId: '',
    locationId: '',
    type: '',
    images: [],
    name: '',
    description: '',
    maxSeatingCapacity: 1,
    pricePerHour: 0,
    pricePerDay: 0,
    pricePerMonth: 0,
    pricePerYear: 0,
    openDays: [],
    openHours: {
      start: '09:00',
      end: '17:00'
    },
    defaultFacilities: [],
    additionalFacilities: [],
    status: 'active'
  }
}

const addAnotherProduct = () => {
  if (confirm('This will clear the current form. Are you sure you want to add another product?')) {
    resetForm()
    window.scrollTo({ top: 0, behavior: 'smooth' })
  }
}

const saveProduct = () => {
  if (!isFormValid.value) {
    alert('Please fill in all required fields')
    return
  }

  // Here you would typically send the data to your API
  console.log('Creating product:', form.value)
  
  // Show success message
  alert('Product created successfully!')
  
  // Navigate back to products list
  router.push('/products')
}
</script>

<style scoped>
.shadow-card {
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06);
}
</style>
