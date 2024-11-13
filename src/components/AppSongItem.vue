<template>
  <li class="flex justify-between items-center p-3 pl-6 cursor-pointer transition duration-300 hover:bg-gray-100 rounded-md shadow-sm hover:shadow-md mb-3">
    <div>
      <router-link :to="{ name: 'song', params: { id: song.docId } }" class="font-bold block text-gray-800 hover:text-blue-600 transition duration-300">{{
        song.modified_name
      }}</router-link>
      <span class="text-gray-500 text-sm">{{ song.display_name }}</span>
    </div>

    <div class="text-gray-600 text-lg flex items-center">
      <router-link custom :to="{ name: 'song', params: { id: song.docId }, hash: '#comments' }" v-slot="{ navigate }">
        <span class="comments flex items-center" @click="navigate">
          <i class="fa fa-comments text-gray-600 mr-1"></i>
          {{ song.comment_count }}
        </span>
      </router-link>
    </div>
  </li>
</template>

<script>
export default {
  name: "AppSongItem",
  props: {
    song: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style scoped>
li {
  background-color: #ffffff;
  border: 1px solid #e5e7eb;
  transition: all 0.3s ease-in-out;
}

li:hover {
  background-color: #f9fafb;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.router-link-active {
  color: rgb(233 158 11); 
}

.comments i {
  color: #6b7280; /* Gray color */
}

.comments:hover i {
  color: rgb(233 158 11);
}
</style>
