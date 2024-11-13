<template>
  <main>
    <!-- Introduction -->
    <section
      class="mb-8 py-20 text-white text-center relative introduction-section"
    >
      <div
        class="absolute inset-0 w-full h-full bg-cover bg-center introduction-bg"
        style="background-image: url(assets/img/bg.jpg)"
      ></div>
      <div class="container mx-auto relative z-10">
        <div class="text-white main-header-content">
          <h1 class="font-bold text-5xl mb-5 drop-shadow-lg">
            Listen to Great Music!
          </h1>
          <p class="w-full md:w-8/12 mx-auto text-lg">
            Discover a world of fun and try with our advanced application. Join
            us now and start your musical journey!
          </p>
        </div>
      </div>
      <!-- <img class="relative block mx-auto mt-5 -mb-20 w-auto max-w-full introduction-image" src="../../public/assets/img/introduction-music.png" /> -->
    </section>

    <!-- Main Content -->
    <section class="container mx-auto">
      <div class="bg-white rounded-lg shadow-md overflow-hidden">
        <div
          class="px-6 pt-6 pb-5 font-bold border-b border-gray-200 flex items-center justify-between"
        >
          <span class="card-title text-xl">Songs</span>
          <i class="fa fa-headphones-alt text-green-500 text-2xl"></i>
        </div>
        <!-- Playlist -->
        <ol id="playlist">
          <app-song-item v-for="song in songs" :song="song" :key="song.docId" />
        </ol>
        <!-- .. end Playlist -->
      </div>
    </section>
  </main>
</template>

<script>
import {
  fetchSongsWithLimit,
  fetchSongsWithLimitLatVisible,
} from "../includes/firebase";
import AppSongItem from "../components/AppSongItem.vue";

export default {
  name: "HomeView",
  data() {
    return {
      songs: [],
      maxPerPage: 5,
      pendingRequest: false,
    };
  },
  components: {
    AppSongItem,
  },
  created() {
    this.getSongs();
    window.addEventListener("scroll", this.handleScroll);
  },
  beforeUnmount() {
    window.removeEventListener("scroll", this.handleScroll);
  },
  methods: {
    handleScroll() {
      const { scrollTop, offsetHeight } = document.documentElement;
      const { innerHeight } = window;
      const bottomOfWindow =
        Math.round(scrollTop) + innerHeight === offsetHeight;

      if (bottomOfWindow) {
        console.log("bottom");
        this.getSongs();
      }
    },

    async getSongs() {
      if (this.pendingRequest) {
        return;
      }

      this.pendingRequest = true;
      let songs;

      if (this.songs.length) {
        const lastVisible = true;
        songs = await fetchSongsWithLimitLatVisible(
          this.maxPerPage,
          this.songs,
          lastVisible
        );
      } else {
        songs = await fetchSongsWithLimit(this.maxPerPage);
      }

      songs.forEach((doc) => {
        const song = {
          ...doc.data(),
          docId: doc.id,
        };

        this.songs.push(song);
      });

      this.pendingRequest = false;
    },
  },
};
</script>

<style scoped>
.bg-cover {
  background-size: contain;
}
.introduction-section {
  position: relative;
  overflow: hidden;
  color: white;
}

.introduction-bg {
  background-position: center;
  filter: brightness(0.7);
  z-index: -1;
}

@media (min-width: 768px) {
  .introduction-bg {
    background-size: contain;
  }
}

@media (max-width: 767px) {
  .introduction-bg {
    background-size: cover;
  }
}

.introduction-image {
  transition: transform 0.3s ease-in-out;
}

.introduction-image:hover {
  transform: scale(1.05);
}

.main-header-content h1 {
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

.card-title {
  font-size: 1.25rem;
  font-weight: 700;
}

#playlist {
  list-style-type: none;
  padding: 0;
  margin: 0;
}
</style>
