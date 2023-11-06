<template>
  <div id="app">
    <div class="container">
      <h1 class="title">GitHub Finder</h1>
      <input type="text" v-model="searchQuery" placeholder="Enter a GitHub username or repository">
      <button @click="search">Search</button>

      <div v-if="isLoading" class="loading">Loading...</div>

      <div v-if="user" class="user">
        <img :src="user.avatar_url" alt="User Avatar">
        <h2>{{ user.login }}</h2>
        <a :href="user.html_url" target="_blank">View Profile</a>
        <button @click="viewRepositories">View Repositories</button>
      </div>

      <div v-if="repositories" class="repositories">
      
        <ul>
          <li v-for="repo in repositories">
            <h4>{{ repo.name }}</h4>
            <p>{{ repo.description }}</p>
            <button @click="toggleBookmark(repo)" :class="{ 'bookmarked': isBookmarked(repo) }">
          {{ isBookmarked(repo) ? 'Bookmarked' : 'Bookmark' }}
        </button>
          </li>
        </ul>
      </div>
     </div>
  </div>
</template>

<script>
import BookmarkedRepositories from './components/BookmarkedRepositories.vue';

export default {
  data() {
    return {
      searchQuery: '',
      user: null,
      repositories: null,
      isLoading: false,
      repositories: [],
      bookmarkedRepositories: [],
    };
  },
  components: {
    BookmarkedRepositories,
  },
  methods: {
    search() {
      this.isLoading = true;
      this.user = null;
      this.repositories = null;

      fetch(`https://api.github.com/users/${this.searchQuery}`)
        .then((response) => response.json())
        .then((data) => {
          this.isLoading = false;
          this.user = data;
        })
        .catch((error) => {
          this.isLoading = false;
          console.error(error);
        });
    },
    viewRepositories() {
      this.isLoading = true;
      this.repositories = null;

      fetch(`https://api.github.com/users/${this.searchQuery}/repos`)
        .then((response) => response.json())
        .then((data) => {
          this.isLoading = false;
          this.repositories = data;
        })
        .catch((error) => {
          this.isLoading = false;
          console.error(error);
        });
    },
    toggleBookmark(repo) {
      const index = this.bookmarkedRepositories.findIndex((r) => r.id === repo.id);

      if (index >= 0) {
        this.bookmarkedRepositories.splice(index, 1);
      } else {
        this.bookmarkedRepositories.push(repo);
      }

      // Store bookmarked repositories in local storage
      localStorage.setItem('bookmarkedRepositories', JSON.stringify(this.bookmarkedRepositories));
    },

    isBookmarked(repo) {
      return this.bookmarkedRepositories.some((r) => r.id === repo.id);
    },
  },
  mounted() {
    this.repositories = // Fetch repositories from your data source

    // Retrieve bookmarked repositories from local storage
    this.bookmarkedRepositories = JSON.parse(localStorage.getItem('bookmarkedRepositories')) || [];
  },
};
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
}

.title {
  font-size: 2rem;
  margin-bottom: 1rem;
}

input {
  width: 300px;
  padding: 0.5rem;
  margin-right: 1rem;
}

button {
  padding: 0.5rem 1rem;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
}

.loading {
  margin-top: 2rem;
}

.repo {
  margin-bottom: 1rem;
}

.bookmarked {
  background-color: #28a745;
  color: white;
}

.user {
  margin-top: 2rem;
}

img {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  margin-bottom: 1rem;
}

a {
  text-decoration: none;
  color: #007bff;
}

.repositories {
  margin-top: 2rem;
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

h3 {
  margin-bottom: 1rem;
}

li {
  margin-bottom: 1rem;
}

h4 {
  margin-bottom: 0.5rem;
}
</style>