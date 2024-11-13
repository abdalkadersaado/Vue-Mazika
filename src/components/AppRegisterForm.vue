<template>
  <!-- Registration Form -->
  <div class="text-white text-center font-bold p-4 rounded mb-4" v-if="reg.reg_show_alert"
    :class="reg.reg_alert_variant">
    {{ reg.reg_alert_msg }}
  </div>
  <vee-form :validation-schema="regSchema" @submit="register" :initial-values="userData">
    <!-- Name -->
    <div class="mb-3">
      <label class="inline-block mb-2">Name</label>
      <vee-field type="text" name="name"
        class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
        placeholder="Enter Name" />
      <error-message class="text-red-600" name="name" />
    </div>
    <!-- Email -->
    <div class="mb-3">
      <label class="inline-block mb-2">Email</label>
      <vee-field type="email" name="email"
        class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
        placeholder="Enter Email" />
      <error-message class="text-red-600" name="email" />
    </div>
    <!-- Age -->
    <div class="mb-3">
      <label class="inline-block mb-2">Age</label>
      <vee-field type="number" name="age"
        class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded" />
      <error-message class="text-red-600" name="age" />
    </div>
    <!-- Password -->
    <div class="mb-3">
      <label class="inline-block mb-2">Password</label>
      <vee-field name="password" :bails="false" v-slot="{ field, errors }">
        <input
          class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
          placeholder="Password" type="password" v-bind="field" />
        <div class="text-red-600" v-for="error in errors" :key="error">
          {{ error }}
        </div>
      </vee-field>
      <error-message class="text-red-600" name="password" />
    </div>
    <!-- Confirm Password -->
    <div class="mb-3">
      <label class="inline-block mb-2">Confirm Password</label>
      <vee-field type="password" name="confirm_password"
        class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded"
        placeholder="Confirm Password" />
      <error-message class="text-red-600" name="confirm_password" />
    </div>
    <!-- Role -->
    <div class="mb-3">
      <label class="inline-block mb-2">Role</label>
      <vee-field as="select" name="role"
        class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded">
        <option value="artist">Artist</option>
        <option value="Producer">Producer</option>
        <option value="Manager">Manager</option>
      </vee-field>
      <error-message class="text-red-600" name="role" />
    </div>
    <!-- Country -->
    <div class="mb-3">
      <label class="inline-block mb-2">Country</label>
      <vee-field as="select" name="country"
        class="block w-full py-1.5 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded">
        <option value="USA">USA</option>
        <option value="Mexico">Mexico</option>
        <option value="Germany">Germany</option>
        <option value="Antartica">Antartica</option>
      </vee-field>
      <error-message class="text-red-600" name="country" />
    </div>
    <!-- TOS -->
    <div class="mb-3 pl-6">
      <vee-field type="checkbox" name="tos" value="1" class="w-4 h-4 float-left -ml-6 mt-1 rounded" />
      <error-message class="text-red-600 block" name="tos" />
      <label class="inline-block">Accept terms of service</label>
    </div>
    <button type="submit" :disabled="reg.reg_in_submission"
      class="block w-full bg-purple-600 text-white py-1.5 px-3 rounded transition hover:bg-purple-700">
      Submit
    </button>
  </vee-form>
</template>

<script>
import { mapActions } from "pinia";
import useUserStore from "../stores/user";

export default {
  name: "AppRegisterForm",
  data() {
    return {
      userData: {
        country: "USA",
      },
      regSchema: {
        name: "required|min:3|max:100|alpha_spaces",
        email: "required|min:3|max:100|email",
        age: "required|min_value:18|max_value:100",
        password: "required|min:9|max:100|excluded:password",
        confirm_password: "passwords_mismatch:@password",
        role: "required",
        country: "required|country_excluded:Antartica",
        tos: "tos",
      },
      reg: {
        reg_in_submission: false,
        reg_show_alert: false,
        reg_alert_variant: "bg-blue-500",
        reg_alert_msg: "Please wait! Your account is being created.",
      },
    };
  },
  methods: {
    ...mapActions(useUserStore, {
      createUser: "register", // used alias, cause we have a function named register in our methods object already
    }),
    async register(values) {
      this.reg.reg_show_alert = true;
      this.reg.reg_in_submission = true;
      this.reg.reg_alert_variant = "bg-blue-500";
      this.reg.reg_alert_msg = "Please wait! Your account is being created.";

      try {
        await this.createUser(values);

        this.reg.reg_in_submission = false;
        this.reg.reg_alert_variant = "bg-green-500";
        this.reg.reg_alert_msg =
          "Success... Your account has being created successfully.";
      } catch (error) {
        this.reg.reg_in_submission = false;
        this.reg.reg_alert_variant = "bg-red-500";

        if (error && error.code === "auth/email-already-in-use") {
          this.reg.reg_alert_msg = "Email Already Exist";
        } else {
          this.reg.reg_alert_msg =
            "An unexpected error occured please try again later";
        }

        console.log({ error });
      }

      setTimeout(() => {
        this.reg.reg_show_alert = false;
        this.reg.reg_alert_variant = null;
        this.reg.reg_alert_msg = null;
      }, 5000);
    },
  },
};
</script>
<style scoped>
/* Container for the alert message */
.text-white {
  color: #ffffff;
}

/* Alert Box */
.bg-blue-500 {
  background-color: #3b82f6;
}

.bg-green-500 {
  background-color: #10b981;
}

.bg-red-500 {
  background-color: #ef4444;
}

/* Form Inputs */
.vee-field {
  position: relative;
}

input,
select {
  width: 100%;
  padding: 12px 16px;
  border-radius: 8px;
  border: 1px solid #d1d5db;
  background-color: #f9fafb;
  font-size: 16px;
  color: #333;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: border-color 0.3s, box-shadow 0.3s;
}

input:focus,
select:focus {
  border-color: #6366f1;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  outline: none;
}

/* Form Labels */
label {
  display: block;
  font-size: 14px;
  margin-bottom: 6px;
  color: #333;
}

/* Error Messages */
.error-message {
  color: #ef4444;
  font-size: 12px;
  margin-top: 4px;
}

/* Terms of Service Checkbox */
.w-4 {
  width: 16px;
}

.h-4 {
  height: 16px;
}

/* Submit Button */
button {
  background: linear-gradient(to right, #6366f1, #3b82f6);
  color: #ffffff;
  padding: 12px 24px;
  border-radius: 8px;
  border: none;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s, transform 0.3s;
}

button:hover {
  background: linear-gradient(to right, #4f46e5, #3b82f6);
  transform: scale(1.05);
}

button:disabled {
  background: #d1d5db;
  cursor: not-allowed;
}

/* Animation for the input fields and button */
@keyframes shake {
  0% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  50% { transform: translateX(0); }
  75% { transform: translateX(5px); }
  100% { transform: translateX(0); }
}

input:invalid,
select:invalid,
button:disabled {
  animation: shake 0.3s ease;
}
</style>
