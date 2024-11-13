<template>
  <main>
    <!-- Music Header -->
    <section class="w-full mb-8 py-14 text-center text-white relative music-header">
      <div class="absolute inset-0 w-full h-full box-border bg-cover bg-center music-bg"
        style="background-image: url(/assets/img/song-header.png)"></div>
      <div class="container mx-auto flex items-center relative z-10">
        <!-- Play/Pause Button -->
        <button @click.prevent="newSong(song)" type="button"
          class="z-50 h-24 w-24 text-3xl bg-white text-black rounded-full focus:outline-none shadow-lg transform hover:scale-105 transition-transform">
          <i class="fas" :class="{ 'fa-pause': playing, 'fa-play': !playing }"></i>
        </button>
        <div class="z-50 text-left ml-8">
          <!-- Song Info -->
          <div class="text-3xl font-bold">{{ song.modified_name }}</div>
          <div>{{ song.genre }}</div>
        </div>
      </div>
    </section>

    <!-- Form -->
    <section class="container mx-auto mt-6">
      <div class="bg-white rounded-lg shadow-md overflow-hidden">
        <div class="px-6 pt-6 pb-5 font-bold border-b border-gray-200 flex items-center justify-between">
          <!-- Comment Count -->
          <span class="card-title">Comments ({{ song.comment_count }})</span>
          <i class="fa fa-comments text-green-500 text-2xl"></i>
        </div>
        <div class="p-6">
          <div class="text-white text-center font-bold p-4 mb-4" v-if="comment_show_alert"
            :class="comment_alert_variant">
            {{ comment_alert_message }}
          </div>
          <vee-form :validation-schema="schema" @submit="addComment" v-if="userLoggedIn">
            <vee-field as="textarea" name="comment"
              class="block w-full py-2 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded mb-4"
              placeholder="Your comment here..."></vee-field>
            <error-message class="text-red-500" name="comment" />
            <button :disabled="comment_in_submission" type="submit"
              class="py-2 px-4 rounded text-white bg-green-600 block w-full hover:bg-green-700 transition-colors">
              Submit
            </button>
          </vee-form>
          <!-- Sort Comments -->
          <select v-model="sort"
            class="block mt-4 py-2 px-3 text-gray-800 border border-gray-300 transition duration-500 focus:outline-none focus:border-black rounded w-full">
            <option value="asc">Latest</option>
            <option value="desc">Oldest</option>
          </select>
        </div>
      </div>
    </section>

    <!-- Comments -->
    <ul class="container mx-auto mt-6 space-y-4">
      <li class="p-6 bg-gray-50 border border-gray-200 rounded-lg shadow-sm" v-for="(comment, index) in sortedComments" :key="comment.docId">
        <!-- Comment Author -->
        <div class="mb-2 flex items-center justify-between">
          <div class="font-bold">{{ comment.name }}</div>
          <time class="text-gray-500 text-sm">{{ new Date(comment.datePosted).toLocaleString() }}</time>
        </div>
        <p class="text-gray-700" >
          {{ comment.content }}
        </p>
      </li>
    </ul>
  </main>
</template>

<script>
import { mapState, mapActions } from "pinia";
import useUserStore from "../stores/user";
import usePlayerStore from "../stores/player";

import {
  fetchSong,
  checkCurrentUser,
  createCommentDocument,
  customSnapshot,
  fetchComments,
  updateSong,
} from "../includes/firebase";
export default {
  name: "songView",
  data() {
    return {
      song: {},
      schema: {
        comment: "required|min:3",
      },
      sort: "asc",
      comments: [],
      comment_in_submission: false,
      comment_show_alert: false,
      comment_alert_variant: "bg-blue-500",
      comment_alert_message: "Please wait !!! your comment is being submitted",
    };
  },
  computed: {
    ...mapState(useUserStore, ["userLoggedIn", "playing"]),
    sortedComments() {
      return this.comments.slice().sort((a, b) => {
        if (this.sort === "asc") {
          return new Date(b.datePosted) - new Date(a.datePosted);
        }

        return new Date(a.datePosted) - new Date(b.datePosted);
      });
    },
  },
  async created() {
    const { sort } = this.$route.query;
    this.sort = sort === "asc" || sort === "desc" ? sort : "asc";

    const docSnap = await fetchSong(this.$route.params.id);

    if (docSnap.exists()) {
      console.log("Document data:", docSnap.data());
      this.song = docSnap.data();
    } else {
      // doc.data() will be undefined in this case
      console.log("No such document!");
      this.$router.push({ name: "home" });
      return;
    }

    this.getComments();
  },
  methods: {
    ...mapActions(usePlayerStore, ["newSong"]),
    async addComment(values, { resetForm }) {
      this.comment_show_alert = true;
      this.comment_in_submission = true;
      this.comment_alert_variant = "bg-blue-500";
      this.comment_alert_message =
        "Please wait !!! your comment is being submitted";

      const comment = {
        content: values.comment,
        datePosted: new Date().toString(),
        sid: this.$route.params.id,
        name: checkCurrentUser().displayName,
        uid: checkCurrentUser().uid,
      };

      const commentRef = await createCommentDocument(comment);
      await customSnapshot(commentRef);

      this.song.comment_count += 1;
      await updateSong(this.$route.params.id, {
        comment_count: Number(this.song.comment_count),
      });

      this.getComments();

      this.comment_in_submission = false;
      this.comment_alert_variant = "bg-green-500";
      this.comment_alert_message = "Comment added successfully";

      resetForm();
    },
    async getComments() {
      const snapShots = await fetchComments(this.$route.params.id);

      this.comments = [];

      snapShots.docs.forEach((doc) => {
        const comment = {
          ...doc.data(),
          docId: doc.id,
        };
        this.comments.push(comment);
      });
    },
  },
  watch: {
    sort(newValue) {
      if (newValue === this.$route.query.sort) return;

      this.$router.push({
        query: {
          sort: newValue,
        },
      });
    },
  },
};
</script>

<style scoped>
.music-header {
  background-size: cover;
  background-position: center;
  color: white;
  text-align: center;
  position: relative;
  overflow: hidden;
}

.music-header .music-bg {
  filter: brightness(0.5);
}

.music-header .container {
  position: relative;
  z-index: 1;
}

.music-header button {
  transition: transform 0.3s ease-in-out;
}

.music-header button:hover {
  transform: scale(1.1);
}

.bg-gray-50 {
  background-color: #f9fafb;
}

.shadow-md {
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.shadow-lg {
  box-shadow: 0 10px 15px rgba(0, 0, 0, 0.1);
}

.border-gray-200 {
  border-color: #e5e7eb;
}

.text-gray-700 {
  color: #4b5563;
}

.text-gray-800 {
  color: #1f2937;
}

.bg-green-500 {
  background-color: #10b981;
}

.bg-green-600 {
  background-color: #059669;
}

.bg-green-700 {
  background-color: #047857;
}

.bg-blue-500 {
  background-color: #3b82f6;
}
</style>
