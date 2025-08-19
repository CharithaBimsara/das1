<template>
  <AdminLayout>
    <div class="space-y-6">
      <!-- Header with Back Button -->
      <div class="flex items-center justify-between">
        <div class="flex items-center space-x-4">
          <router-link to="/locations" class="p-2 rounded-lg border border-gray-300 hover:bg-gray-50">
            <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
            </svg>
          </router-link>
          <div>
            <h1 class="text-xl font-bold text-gray-900">Add New Location</h1>
          </div>
        </div>
      </div>

      <!-- Form Content -->
      <div class="max-w-6xl mx-auto">
        <form @submit.prevent="saveLocation" class="space-y-6">
          <!-- Single Card with All Information -->
          <div class="bg-white rounded-xl shadow-card overflow-hidden">
            <div class="p-8 space-y-16">
              
              <!-- Company Selection Section -->
              <div>
                <h2 class="text-xl font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-3" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiOfficeBuilding" />
                  </svg>
                  Select Company
                </h2>
                
                <div class="grid grid-cols-1 gap-6">
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Company <span class="text-red-500">*</span>
                    </label>
                    <select v-model="form.companyId" required @change="onCompanyChange"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900">
                      <option value="">Select a company</option>
                      <option v-for="company in companies" :key="company.id" :value="company.id">
                        {{ company.name }}
                      </option>
                    </select>
                  </div>
                </div>
              </div>

              <!-- Location Details Section -->
              <div>
                <h2 class="text-xl font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-3" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiMapMarker" />
                  </svg>
                  Location Details
                </h2>
                
                <!-- Form Fields -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Location Name <span class="text-red-500">*</span>
                    </label>
                    <input type="text" v-model="form.name" required
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                      placeholder="Enter location name" />
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Address <span class="text-red-500">*</span>
                    </label>
                    <input type="text" v-model="form.address" required
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                      placeholder="Enter full address" />
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Map URL
                    </label>
                    <input type="url" v-model="form.mapUrl"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                      placeholder="https://maps.google.com/..." />
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Location
                    </label>
                    <input type="text" v-model="form.location"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900"
                      placeholder="City, State/Province" />
                  </div>
                </div>
              </div>

              <!-- Contact Information Section -->
              <div>
                <h2 class="text-xl font-semibold text-gray-900 mb-6 flex items-center">
                  <svg class="w-6 h-6 mr-3" fill="currentColor" viewBox="0 0 24 24">
                    <path :d="mdiAccount" />
                  </svg>
                  Contact Information
                </h2>
                
                <!-- Same as Company Checkbox -->
                <div class="mb-6">
                  <label class="flex items-center">
                    <input type="checkbox" v-model="form.sameAsCompany" @change="onSameAsCompanyChange"
                      class="rounded border-gray-300 text-primary-600 shadow-sm focus:border-primary-300 focus:ring focus:ring-primary-200 focus:ring-opacity-50" />
                    <span class="ml-2 text-sm text-gray-900">Same as company contact</span>
                  </label>
                </div>

                <!-- Contact Fields -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Contact Person Name <span class="text-red-500">*</span>
                    </label>
                    <input type="text" v-model="form.contactPersonName" required :disabled="form.sameAsCompany"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900 disabled:bg-gray-50 disabled:cursor-not-allowed"
                      placeholder="Enter contact person name" />
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Contact Phone Number <span class="text-red-500">*</span>
                    </label>
                    <input type="tel" v-model="form.contactPhone" required :disabled="form.sameAsCompany"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900 disabled:bg-gray-50 disabled:cursor-not-allowed"
                      placeholder="+1-555-0123" />
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                      Email <span class="text-red-500">*</span>
                    </label>
                    <input type="email" v-model="form.contactEmail" required :disabled="form.sameAsCompany"
                      class="w-full border border-gray-300 rounded-lg px-4 py-3 focus:ring-2 focus:ring-primary-500 focus:border-transparent text-gray-900 disabled:bg-gray-50 disabled:cursor-not-allowed"
                      placeholder="contact@company.com" />
                  </div>
                </div>
              </div>

              <!-- Action Buttons - Bottom Right -->
              <div class="flex justify-end space-x-4 pt-6 border-t border-gray-200">
                <router-link to="/locations"
                  class="px-6 py-3 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors">
                  Cancel
                </router-link>
                <button type="submit" :disabled="!isFormValid"
                  class="px-6 py-3 bg-primary-600 text-white rounded-lg hover:bg-primary-700 transition-colors disabled:opacity-50 disabled:cursor-not-allowed flex items-center space-x-2">
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
                  </svg>
                  <span>Create Location</span>
                </button>
              </div>
            </div>
          </div>
        </form>
      </div>
    </div>
  </AdminLayout>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import AdminLayout from '@/components/layout/AdminLayout.vue'
import { mdiOfficeBuilding, mdiMapMarker, mdiAccount } from '@mdi/js'

const router = useRouter()

// Sample companies data
const companies = ref([
  {
    id: 'COMP-001',
    name: 'WorkHub Co.',
    contactPersonName: 'John Smith',
    contactPhone: '+1-555-0100',
    contactEmail: 'contact@workhub.com'
  },
  {
    id: 'COMP-002',
    name: 'FlexSpace Inc.',
    contactPersonName: 'Sarah Johnson',
    contactPhone: '+1-555-0200',
    contactEmail: 'info@flexspace.com'
  },
  {
    id: 'COMP-003',
    name: 'Creative Spaces LLC',
    contactPersonName: 'Mike Wilson',
    contactPhone: '+1-555-0300',
    contactEmail: 'hello@creativespaces.com'
  }
])

// Form data
const form = ref({
  companyId: '',
  name: '',
  address: '',
  mapUrl: '',
  location: '',
  contactPersonName: '',
  contactPhone: '',
  contactEmail: '',
  sameAsCompany: false
})

// Form validation
const isFormValid = computed(() => {
  return form.value.companyId !== '' &&
         form.value.name.trim() !== '' &&
         form.value.address.trim() !== '' &&
         form.value.contactPersonName.trim() !== '' &&
         form.value.contactPhone.trim() !== '' &&
         form.value.contactEmail.trim() !== ''
})

// Methods
const onCompanyChange = () => {
  if (form.value.sameAsCompany) {
    fillCompanyContactInfo()
  }
}

const onSameAsCompanyChange = () => {
  if (form.value.sameAsCompany) {
    fillCompanyContactInfo()
  } else {
    // Clear contact fields when unchecked
    form.value.contactPersonName = ''
    form.value.contactPhone = ''
    form.value.contactEmail = ''
  }
}

const fillCompanyContactInfo = () => {
  const selectedCompany = companies.value.find(c => c.id === form.value.companyId)
  if (selectedCompany) {
    form.value.contactPersonName = selectedCompany.contactPersonName
    form.value.contactPhone = selectedCompany.contactPhone
    form.value.contactEmail = selectedCompany.contactEmail
  }
}

const saveLocation = () => {
  if (!isFormValid.value) {
    alert('Please fill in all required fields')
    return
  }

  // Here you would typically send the data to your API
  console.log('Creating location:', form.value)
  
  // Show success message
  alert('Location created successfully!')
  
  // Navigate back to locations list
  router.push('/locations')
}
</script>
