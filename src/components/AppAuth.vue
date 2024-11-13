<template>
  <!-- Auth Modal -->
  <div class="fixed z-50 inset-0 overflow-y-auto" id="modal" :class="hiddenClass">
    <div class="flex items-center justify-center min-h-screen px-4 py-6">
      <div class="fixed inset-0 transition-opacity">
        <div class="absolute inset-0 bg-gray-900 opacity-80"></div>
      </div>

      <!-- This element is to trick the browser into centering the modal contents. -->
      <span class="hidden sm:inline-block sm:align-middle sm:h-screen">&#8203;</span>

      <div class="inline-block align-bottom bg-gradient-to-br from-gray-100 via-gray-300 to-gray-400 text-left overflow-hidden shadow-2xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full animate-modal">
        <div class="relative py-6 px-8">
          <!--Title-->
          <div class="flex justify-between items-center pb-6 border-b border-gray-600">
            <p class="text-3xl font-bold text-gray-900 flex items-center space-x-2">
              <i class="fas fa-music"></i>
              <span>Your Account</span>
            </p>
            <!-- Modal Close Button -->
            <button class="absolute top-3 right-3 text-gray-900 hover:text-gray-600 transition" @click.prevent="toggleAuthModal">
              <i class="fas fa-times fa-lg"></i>
            </button>
          </div>

          <!-- Tabs -->
          <ul class="flex border-b border-gray-600 mb-6">
            <li class="flex-auto">
              <a class="block py-3 px-4 text-center cursor-pointer transition rounded" href="#" :class="{
                'bg-gray-800 text-white': tab === 'login',
                'text-gray-900': tab === 'register',
                'hover:bg-gray-200 hover:text-gray-900': tab !== 'login',
              }" @click.prevent="tab = 'login'">Login</a>
            </li>
            <li class="flex-auto">
              <a class="block py-3 px-4 text-center cursor-pointer transition rounded" href="#" @click.prevent="tab = 'register'" :class="{
                'bg-gray-800 text-white': tab === 'register',
                'text-gray-900': tab === 'login',
                'hover:bg-gray-200 hover:text-gray-900': tab !== 'register',
              }">Register</a>
            </li>
          </ul>

          <!-- Forms -->
          <div class="space-y-4">
            <app-login-form v-if="tab === 'login'" />
            <app-register-form v-else />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import AppRegisterForm from "./AppRegisterForm.vue";
import AppLoginForm from "./AppLoginForm.vue";

import { mapState, mapWritableState } from "pinia";
import useModalStore from "@/stores/modal";

export default {
  name: "AppAuth",
  components: {
    AppRegisterForm,
    AppLoginForm,
  },
  data() {
    return {
      tab: "login",
    };
  },
  computed: {
    ...mapState(useModalStore, ["hiddenClass"]),
    ...mapWritableState(useModalStore, ["isOpen"]),
  },
  methods: {
    toggleAuthModal() {
      this.isOpen = !this.isOpen;
    },
  },
};
</script>

<style scoped>
/* Add background animation for modal */
@keyframes slideIn {
  from {
    transform: scale(0.8);
    opacity: 0;
  }
  to {
    transform: scale(1);
    opacity: 1;
  }
}

#modal {
  background: rgba(0, 0, 0, 0.6);
}

.animate-modal {
  animation: slideIn 0.4s ease-out;
}

/* Modal close button style */
button {
  border: none;
  background: none;
  cursor: pointer;
}

/* Tabs styles */
a {
  transition: background-color 0.3s, color 0.3s;
  font-weight: 500;
}

/* Tab active state */
.bg-gray-800 {
  background-color: #2d3748;
}

.text-gray-900 {
  color: #1a202c;
}

.text-gray-600 {
  color: #4a5568;
}

.bg-gray-200 {
  background-color: #edf2f7;
}

/* Additional styles for gradient background */
.bg-gradient-to-br {
  background: linear-gradient(to bottom right, #f7fafc, #e2e8f0, #cbd5e0);
}

/* Icon color transition */
.fas {
  transition: color 0.3s;
}

.fas.fa-music {
  color: #1a202c;
}

/* Form inputs */
input[type="text"],
input[type="email"],
input[type="password"],
textarea {
  width: 100%;
  padding: 12px 16px;
  border-radius: 8px;
  border: 1px solid #d1d5db;
  background-color: #fff;
  font-size: 16px;
  color: #333;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: border-color 0.3s, box-shadow 0.3s;
}

input[type="text"]:focus,
input[type="email"]:focus,
input[type="password"]:focus,
textarea:focus {
  border-color: #4a5568;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  outline: none;
}

/* Form labels */
label {
  display: block;
  font-size: 14px;
  margin-bottom: 4px;
  color: #555;
}

/* Buttons */
button.primary-button {
  background: #2d3748;
  color: #fff;
  padding: 12px 24px;
  border-radius: 8px;
  border: none;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s, transform 0.3s;
}

button.primary-button:hover {
  background: #1a202c;
  transform: scale(1.05);
}

button.primary-button:active {
  background: #1a202c;
}

/* Secondary button */
button.secondary-button {
  background: transparent;
  color: #2d3748;
  padding: 12px 24px;
  border-radius: 8px;
  border: 2px solid #2d3748;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s, color 0.3s, transform 0.3s;
}

button.secondary-button:hover {
  background: #2d3748;
  color: #fff;
  transform: scale(1.05);
}

button.secondary-button:active {
  background: #1a202c;
  color: #fff;
}
</style>

