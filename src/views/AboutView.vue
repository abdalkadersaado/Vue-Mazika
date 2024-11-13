<template>
  <div class="about">
    <div class="container">
      <h1><i class="fas fa-music"></i> About Our Project</h1>
      <p>
        Welcome to our fantastic music playlist project! Here, we offer you a unique musical experience where you can explore and listen to a wide variety of music.
      </p>
      <p>
        This project was established with the goal of providing an easy-to-use platform for sharing music playlists, allowing users to discover new songs and share them with friends.
      </p>
      <h2><i class="fas fa-star"></i> Project Features</h2>
      <ul>
        <li><i class="fas fa-check"></i> Display custom music playlists.</li>
        <li><i class="fas fa-search"></i> Search for songs and artists.</li>
        <li><i class="fas fa-list"></i> Create personalized playlists.</li>
        <li><i class="fas fa-share-alt"></i> Share music playlists with others.</li>
      </ul>
      <h2><i class="fas fa-users"></i> The Team</h2>
      <p>
        This project was developed by a team of music enthusiasts. We are working hard to provide a great experience and welcome any feedback or suggestions.
      </p>
      <h2><i class="fas fa-envelope"></i> Contact Us</h2>
      <p>
        If you have any questions or need support, feel free to reach out to us at <a href="mailto:support@yourproject.com">support@yourproject.com</a>.
      </p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'About'
}
</script>

<style scoped>
.about {
  background: linear-gradient(135deg, #f3f4f6, #dbe2e8);
  padding: 40px 20px;
  font-family: 'Arial', sans-serif;
  color: #333;
  animation: fadeIn 1s ease-out;
}

.container {
  max-width: 900px;
  margin: 0 auto;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  padding: 20px;
  animation: slideIn 0.5s ease-out;
}

h1, h2 {
  display: flex;
  align-items: center;
  font-size: 2em;
  margin-bottom: 20px;
  position: relative;
}

h1 i, h2 i {
  margin-right: 10px;
  color: #4a90e2;
  transition: transform 0.3s ease;
}

h1:hover i, h2:hover i {
  transform: rotate(20deg);
}

h1 {
  color: #4a90e2;
  border-bottom: 2px solid #4a90e2;
  padding-bottom: 10px;
}

h2 {
  color: #333;
  margin-top: 30px;
  border-bottom: 1px solid #eee;
  padding-bottom: 5px;
}

p {
  line-height: 1.7;
  margin-bottom: 20px;
}

ul {
  list-style-type: none;
  padding: 0;
}

ul li {
  background: #f4f4f4;
  border-radius: 5px;
  margin-bottom: 10px;
  padding: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
  display: flex;
  align-items: center;
}

ul li i {
  color: #4a90e2;
  margin-right: 10px;
}

a {
  color: #4a90e2;
  text-decoration: none;
  transition: color 0.3s ease;
}

a:hover {
  color: #0056b3;
  text-decoration: underline;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slideIn {
  from {
    transform: translateY(-20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>
