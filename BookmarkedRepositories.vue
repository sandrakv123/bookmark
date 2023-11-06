<template>
    <div>
      <h1>Bookmarked Repositories</h1>
      <ul>
        <li v-for="repo in bookmarkedRepositories" :key="repo.id">
          {{ repo.name }}
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      bookmarkedRepositories: {
        type: Array,
        required: true,
      },
    },
  };
  </script>