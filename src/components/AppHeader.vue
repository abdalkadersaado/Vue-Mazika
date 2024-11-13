<template>
  <!-- Header -->
  <header id="header" class="header-background shadow-md">
    <nav class="container mx-auto flex items-center justify-between py-4 px-6 relative">
      <!-- Mobile Menu Button -->
      <button @click="toggleMobileMenu" class="md:hidden text-white text-2xl">
        <i class="fas fa-bars"></i>
      </button>

      <!-- App Name -->
      <router-link class="text-white font-bold uppercase text-2xl flex items-center" :to="{ name: 'home' }" exact-active-class="active">
        <i class="fas fa-music mr-2"></i>
        Mazika
      </router-link>

      <!-- Navigation Links (About, Manage) -->
      <div :class="{'block': isMobileMenuOpen, 'hidden': !isMobileMenuOpen}" style="z-index: 21;" class="md:flex flex-grow items-center justify-center space-x-4 absolute md:relative top-full left-0 w-full md:w-auto mt-4 md:mt-0">
        <router-link class="nav-link flex items-center p-2" :to="{ name: 'about' }" exact-active-class="active" @click="closeMobileMenu">
          <i class="fas fa-info-circle mr-1"></i>
          About
        </router-link>
        <template v-if="userLoggedIn">
          <router-link class="nav-link flex items-center p-2" :to="{ name: 'manage' }" exact-active-class="active" @click="closeMobileMenu">
            <i class="fas fa-cogs mr-1"></i>
            Manage
          </router-link>
        </template>
      </div>

      <!-- Auth Links (Login/Register or Logout) -->
      <div class="flex items-center space-x-4">
        <template v-if="!userLoggedIn">
          <a class="text-white text-lg hover:text-gray-300 transition duration-300 cursor-pointer flex items-center" @click.prevent="toggleAuthModal">
            <i class="fas fa-user-plus mr-1"></i>
            Login / Register
          </a>
        </template>
        <template v-else>
          <a class="text-white text-lg hover:text-gray-300 transition duration-300 cursor-pointer flex items-center" @click.prevent="logOut">
            <i class="fas fa-sign-out-alt mr-1"></i>
            LogOut
          </a>
        </template>
      </div>
    </nav>
  </header>
</template>

<script>
import { mapState, mapWritableState, mapActions } from "pinia";
import useModalStore from "@/stores/modal";
import useUserStore from "@/stores/user";

export default {
  name: "AppHeader",
  data() {
    return {
      isMobileMenuOpen: false,
    };
  },
  computed: {
    ...mapWritableState(useModalStore, ["isOpen"]),
    ...mapState(useUserStore, ["userLoggedIn"]),
  },
  methods: {
    toggleAuthModal() {
      if (this.isOpen !== undefined) {
        this.isOpen = !this.isOpen;
      } else {
        console.warn("isOpen is undefined");
      }
    },
    toggleMobileMenu() {
      this.isMobileMenuOpen = !this.isMobileMenuOpen;
    },
    closeMobileMenu() {
      this.isMobileMenuOpen = false;
    },
    ...mapActions(useUserStore, ["signOut"]),
    async logOut() {
      try {
        await this.signOut();

        if (this.$route.meta.requiresAuth) {
          this.$router.push({ name: "home" });
        }
      } catch (error) {
        console.log({ error });
      }
    },
  },
};
</script>

<style scoped>
/* Default background for small screens */
.header-background {
  background: linear-gradient(to right, #00bcd4, #2196f3);
}

/* Override background for medium screens and larger */
@media (min-width: 768px) {
  .header-background {
    background: linear-gradient(to right, #00bcd4, #2196f3);
  }
}

.nav-link {
  color: white;
  font-size: 1.125rem; /* 18px */
  transition: color 0.3s;
  display: flex;
  align-items: center;
}

.nav-link:hover {
  color: rgb(240, 237, 237);
}

.nav-link.active {
  color: rgb(255, 145, 0); /* اللون الذي تريده لتمييز العنصر النشط */
  font-weight: bold;
}

.nav-link i {
  font-size: 1.25rem; /* Adjust icon size if necessary */
}

@media (max-width: 767px) {
  .nav-link {
    padding: 1rem;
    border-bottom: 1px solid rgba(255, 255, 255, 0.2);
    background: linear-gradient(to right, #00bcd4, #2196f3);
  }
}
</style>
