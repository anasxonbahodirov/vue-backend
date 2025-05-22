<script setup>
import { reactive, ref, onMounted } from 'vue'

const isLoading = ref(false)
const isSubmitting = ref(false)

let user = reactive({
  name: '',
  username: '',
  email: '',
  phone: '',
  website: '',
  companyName: '',
  street: '',
  city: '',
  zipcode: '',
})

const users = ref([])
const showToast = ref(false)
const toastMessage = ref('')
const toastType = ref('success')

const showNotification = (message, type = 'success') => {
  toastMessage.value = message
  toastType.value = type
  showToast.value = true
  setTimeout(() => (showToast.value = false), 3000)
}

const addUser = async (e) => {
  e.preventDefault()
  isSubmitting.value = true

  try {
    const response = await fetch('http://localhost:3000/users', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(user),
    })

    if (!response.ok) throw new Error('Tarmoq javobi yaxshi emas')

    const newUser = await response.json()
    users.value.push(newUser)
    resetForm()
    showNotification("Foydalanuvchi muvaffaqiyatli qo'shildi!")
  } catch (error) {
    console.error('Error:', error)
    showNotification('Foydalanuvchi qo‘shib bo‘lmadi: ' + error.message, 'error')
  } finally {
    isSubmitting.value = false
  }
}

const resetForm = () => {
  Object.keys(user).forEach((key) => {
    user[key] = ''
  })
}

const deleteUser = async (id, index) => {
  try {
    const response = await fetch(`http://localhost:3000/users/${id}`, {
      method: 'DELETE',
    })

    if (!response.ok) throw new Error('Tarmoq javobi yaxshi emas')

    users.value.splice(index, 1)
    showNotification("Foydalanuvchi muvaffaqiyatli o'chirildi!")
  } catch (error) {
    console.error('Error:', error)
    showNotification('Foydalanuvchi qo‘shib bo‘lmadi: ' + error.message, 'error')
  }
}

const restoreUser = (userData) => {
  Object.keys(userData).forEach((key) => {
    if (key in user) {
      user[key] = userData[key]
    }
  })
  showNotification("Foydalanuvchi ma'lumotlari shaklga qaytarildi!")
}

const fetchUsers = async () => {
  isLoading.value = true
  try {
    const response = await fetch('http://localhost:3000/users')
    if (!response.ok) throw new Error('Tarmoq javobi yaxshi emas')
    users.value = await response.json()
  } catch (error) {
    console.error('Error:', error)
    showNotification('Foydalanuvchi qo‘shib bo‘lmadi: ' + error.message, 'error')
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  fetchUsers()
})
</script>

<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-900 to-slate-800 w-full p-6 md:p-10">
    <transition name="fade">
      <div
        v-if="showToast"
        :class="`fixed top-6 right-6 z-50 px-6 py-3 rounded-lg shadow-lg text-white font-medium flex items-center 
                   ${toastType === 'success' ? 'bg-emerald-500' : 'bg-rose-500'}`"
      >
        <svg
          class="w-5 h-5 mr-2"
          fill="none"
          stroke="currentColor"
          viewBox="0 0 24 24"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            v-if="toastType === 'success'"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M5 13l4 4L19 7"
          ></path>
          <path
            v-else
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M6 18L18 6M6 6l12 12"
          ></path>
        </svg>
        {{ toastMessage }}
      </div>
    </transition>

    <div class="max-w-7xl mx-auto mb-10 text-center">
      <h1 class="text-4xl md:text-5xl font-bold text-white mb-2">___________Users___________</h1>
      <p class="text-slate-300 max-w-2xl mx-auto">
        _________________Special for you_________________
      </p>
    </div>

    <div class="max-w-6xl mx-auto mb-16">
      <form
        @submit="addUser"
        class="bg-white/10 backdrop-blur-lg rounded-xl p-6 shadow-2xl border border-white/10"
      >
        <h2 class="text-2xl font-semibold text-white mb-6">Add New User</h2>

        <div class="grid md:grid-cols-2 gap-6 mb-6">
          <div class="space-y-4">
            <h3 class="text-lg font-medium text-emerald-300 mb-3">Personal Information</h3>

            <div class="relative">
              <input
                type="text"
                v-model="user.name"
                id="name"
                class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:border-transparent peer"
                placeholder=" "
                required
              />
              <label
                for="name"
                class="absolute left-4 top-3 text-white/70 peer-focus:text-emerald-300 peer-focus:text-sm peer-focus:-top-2 peer-focus:bg-slate-900 peer-focus:px-2 transition-all duration-200 pointer-events-none peer-placeholder-shown:text-base peer-placeholder-shown:top-3"
                >Full Name</label
              >
            </div>

            <div class="relative">
              <input
                type="text"
                v-model="user.username"
                id="username"
                class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:border-transparent peer"
                placeholder=" "
                required
              />
              <label
                for="username"
                class="absolute left-4 top-3 text-white/70 peer-focus:text-emerald-300 peer-focus:text-sm peer-focus:-top-2 peer-focus:bg-slate-900 peer-focus:px-2 transition-all duration-200 pointer-events-none peer-placeholder-shown:text-base peer-placeholder-shown:top-3"
                >Username</label
              >
            </div>
          </div>

          <div class="space-y-4">
            <h3 class="text-lg font-medium text-emerald-300 mb-3">Contact Information</h3>

            <div class="relative">
              <input
                type="email"
                v-model="user.email"
                id="email"
                class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:border-transparent peer"
                placeholder=" "
                required
              />
              <label
                for="email"
                class="absolute left-4 top-3 text-white/70 peer-focus:text-emerald-300 peer-focus:text-sm peer-focus:-top-2 peer-focus:bg-slate-900 peer-focus:px-2 transition-all duration-200 pointer-events-none peer-placeholder-shown:text-base peer-placeholder-shown:top-3"
                >Email Address</label
              >
            </div>

            <div class="relative">
              <input
                type="tel"
                v-model="user.phone"
                id="phone"
                class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:border-transparent peer"
                placeholder=" "
                required
              />
              <label
                for="phone"
                class="absolute left-4 top-3 text-white/70 peer-focus:text-emerald-300 peer-focus:text-sm peer-focus:-top-2 peer-focus:bg-slate-900 peer-focus:px-2 transition-all duration-200 pointer-events-none peer-placeholder-shown:text-base peer-placeholder-shown:top-3"
                >Phone Number</label
              >
            </div>
          </div>
        </div>

        <div class="grid md:grid-cols-2 gap-6 mb-6">
          <div class="space-y-4">
            <h3 class="text-lg font-medium text-emerald-300 mb-3">Company Information</h3>

            <div class="relative">
              <input
                type="text"
                v-model="user.companyName"
                id="company"
                class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:border-transparent peer"
                placeholder=" "
                required
              />
              <label
                for="company"
                class="absolute left-4 top-3 text-white/70 peer-focus:text-emerald-300 peer-focus:text-sm peer-focus:-top-2 peer-focus:bg-slate-900 peer-focus:px-2 transition-all duration-200 pointer-events-none peer-placeholder-shown:text-base peer-placeholder-shown:top-3"
                >Company Name</label
              >
            </div>

            <div class="relative">
              <input
                type="text"
                v-model="user.website"
                id="website"
                class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:border-transparent peer"
                placeholder=" "
                required
              />
              <label
                for="website"
                class="absolute left-4 top-3 text-white/70 peer-focus:text-emerald-300 peer-focus:text-sm peer-focus:-top-2 peer-focus:bg-slate-900 peer-focus:px-2 transition-all duration-200 pointer-events-none peer-placeholder-shown:text-base peer-placeholder-shown:top-3"
                >Website</label
              >
            </div>
          </div>

          <div class="space-y-4">
            <h3 class="text-lg font-medium text-emerald-300 mb-3">Address Information</h3>

            <div class="grid grid-cols-2 gap-4">
              <div class="relative">
                <input
                  type="text"
                  v-model="user.street"
                  id="street"
                  class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:border-transparent peer"
                  placeholder=" "
                  required
                />
                <label
                  for="street"
                  class="absolute left-4 top-3 text-white/70 peer-focus:text-emerald-300 peer-focus:text-sm peer-focus:-top-2 peer-focus:bg-slate-900 peer-focus:px-2 transition-all duration-200 pointer-events-none peer-placeholder-shown:text-base peer-placeholder-shown:top-3"
                  >Street</label
                >
              </div>

              <div class="relative">
                <input
                  type="text"
                  v-model="user.city"
                  id="city"
                  class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:border-transparent peer"
                  placeholder=" "
                  required
                />
                <label
                  for="city"
                  class="absolute left-4 top-3 text-white/70 peer-focus:text-emerald-300 peer-focus:text-sm peer-focus:-top-2 peer-focus:bg-slate-900 peer-focus:px-2 transition-all duration-200 pointer-events-none peer-placeholder-shown:text-base peer-placeholder-shown:top-3"
                  >City</label
                >
              </div>
            </div>

            <div class="relative">
              <input
                type="text"
                v-model="user.zipcode"
                id="zipcode"
                class="w-full px-4 py-3 bg-white/5 border border-white/10 rounded-lg text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:border-transparent peer"
                placeholder=" "
                required
              />
              <label
                for="zipcode"
                class="absolute left-4 top-3 text-white/70 peer-focus:text-emerald-300 peer-focus:text-sm peer-focus:-top-2 peer-focus:bg-slate-900 peer-focus:px-2 transition-all duration-200 pointer-events-none peer-placeholder-shown:text-base peer-placeholder-shown:top-3"
                >Zip Code</label
              >
            </div>
          </div>
        </div>

        <div class="flex justify-end space-x-4">
          <button
            type="button"
            @click="resetForm"
            class="px-6 py-2.5 border border-white/20 rounded-lg text-white hover:bg-white/10 transition-colors duration-200"
          >
            Reset
          </button>
          <button
            type="submit"
            :disabled="isSubmitting"
            class="px-6 py-2.5 bg-emerald-500 rounded-lg text-white font-medium hover:bg-emerald-600 transition-colors duration-200 flex items-center disabled:opacity-70 disabled:cursor-not-allowed"
          >
            <svg
              v-if="isSubmitting"
              class="animate-spin -ml-1 mr-2 h-4 w-4 text-white"
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
            >
              <circle
                class="opacity-25"
                cx="12"
                cy="12"
                r="10"
                stroke="currentColor"
                stroke-width="4"
              ></circle>
              <path
                class="opacity-75"
                fill="currentColor"
                d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
              ></path>
            </svg>
            {{ isSubmitting ? 'Adding...' : 'Add User' }}
          </button>
        </div>
      </form>
    </div>

    <div class="max-w-7xl mx-auto">
      <div class="flex justify-between items-center mb-6">
        <h2 class="text-2xl font-semibold text-white">User Directory</h2>
        <button
          @click="fetchUsers"
          class="flex items-center text-sm text-emerald-300 hover:text-emerald-400 transition-colors"
          :disabled="isLoading"
        >
          <svg
            v-if="isLoading"
            class="animate-spin mr-2 h-4 w-4 text-white"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
          >
            <circle
              class="opacity-25"
              cx="12"
              cy="12"
              r="10"
              stroke="currentColor"
              stroke-width="4"
            ></circle>
            <path
              class="opacity-75"
              fill="currentColor"
              d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
            ></path>
          </svg>
          {{ isLoading ? 'Refreshing...' : 'Refresh Data' }}
        </button>
      </div>

      <div v-if="isLoading && users.length === 0" class="grid place-items-center py-20">
        <div class="animate-pulse flex flex-col items-center">
          <div class="h-12 w-12 bg-emerald-400/20 rounded-full mb-4"></div>
          <div class="h-4 w-32 bg-white/10 rounded mb-2"></div>
          <div class="h-4 w-48 bg-white/10 rounded"></div>
        </div>
      </div>

      <div v-else-if="users.length === 0" class="text-center py-16 text-white/50">
        No users found. Add your first user above.
      </div>

      <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
        <div
          v-for="(userItem, index) in users"
          :key="userItem.id"
          class="bg-white/5 backdrop-blur-sm rounded-xl overflow-hidden shadow-lg border border-white/10 hover:border-emerald-400/30 transition-all duration-300 hover:-translate-y-1"
        >
          <div class="relative h-48 overflow-hidden">
            <div
              class="absolute inset-0 bg-gradient-to-br from-emerald-400/10 to-purple-500/10"
            ></div>
            <img
              class="w-full h-full object-cover mix-blend-overlay opacity-80"
              src="https://images.unsplash.com/photo-1535713875002-d1d0cf377fde?fm=jpg&q=60&w=3000&ixlib=rb-4.1.0&ixid=M3wxMjA3fDB8MHxzZWFyY2h8M3x8dXNlcnxlbnwwfHwwfHx8MA%3D%3D"
              alt="User avatar"
            />
            <div class="absolute bottom-4 left-4">
              <span
                class="bg-emerald-400/90 text-slate-900 text-xs font-semibold px-2.5 py-1 rounded-full"
              >
                ID: {{ userItem.id }}
              </span>
            </div>
          </div>

          <div class="p-6">
            <div class="flex justify-between items-start mb-4">
              <div>
                <h3 class="text-xl font-bold text-white">{{ userItem.name }}</h3>
                <p class="text-emerald-300 text-sm">@{{ userItem.username }}</p>
              </div>
              <div class="flex space-x-2">
                <button
                  @click="restoreUser(userItem)"
                  class="p-2 rounded-full bg-white/5 hover:bg-emerald-400/20 text-emerald-300 hover:text-emerald-400 transition-colors"
                  title="Restore to form"
                >
                  <svg
                    class="w-4 h-4"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
                    ></path>
                  </svg>
                </button>
                <button
                  @click="deleteUser(userItem.id, index)"
                  class="p-2 rounded-full bg-white/5 hover:bg-rose-500/20 text-rose-400 hover:text-rose-500 transition-colors"
                  title="Delete user"
                >
                  <svg
                    class="w-4 h-4"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                    xmlns="http://www.w3.org/2000/svg"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
                    ></path>
                  </svg>
                </button>
              </div>
            </div>

            <div class="space-y-4 text-sm text-white/80">
              <div class="flex items-start">
                <svg
                  class="flex-shrink-0 w-4 h-4 mt-0.5 text-emerald-300"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"
                  ></path>
                </svg>
                <span class="ml-2">{{ userItem.email }}</span>
              </div>

              <div class="flex items-start">
                <svg
                  class="flex-shrink-0 w-4 h-4 mt-0.5 text-emerald-300"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z"
                  ></path>
                </svg>
                <span class="ml-2">{{ userItem.phone || 'Not provided' }}</span>
              </div>

              <div class="flex items-start">
                <svg
                  class="flex-shrink-0 w-4 h-4 mt-0.5 text-emerald-300"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M21 12a9 9 0 01-9 9m9-9a9 9 0 00-9-9m9 9H3m9 9a9 9 0 01-9-9m9 9c1.657 0 3-4.03 3-9s-1.343-9-3-9m0 18c-1.657 0-3-4.03-3-9s1.343-9 3-9m-9 9a9 9 0 019-9"
                  ></path>
                </svg>
                <a
                  :href="'https://' + userItem.website"
                  target="_blank"
                  class="ml-2 hover:text-emerald-300 transition-colors"
                  >{{ userItem.website }}</a
                >
              </div>

              <div class="flex items-start">
                <svg
                  class="flex-shrink-0 w-4 h-4 mt-0.5 text-emerald-300"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"
                  ></path>
                </svg>
                <span class="ml-2">{{ userItem.companyName }}</span>
              </div>

              <div class="flex items-start">
                <svg
                  class="flex-shrink-0 w-4 h-4 mt-0.5 text-emerald-300"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"
                  ></path>
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"
                  ></path>
                </svg>
                <span class="ml-2"
                  >{{ userItem.street }}, {{ userItem.city }}, {{ userItem.zipcode }}</span
                >
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');

html {
  font-family: 'Inter', sans-serif;
  scroll-behavior: smooth;
}

html::-webkit-scrollbar {
  width: 8px;
}

html::-webkit-scrollbar-track {
  background: #0f172a;
}

html::-webkit-scrollbar-thumb {
  background-color: #334155;
  border-radius: 4px;
}

.fade-enter-active,
.fade-leave-active {
  transition:
    opacity 0.3s ease,
    transform 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}
</style>
