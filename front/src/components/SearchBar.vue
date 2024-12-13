<template>
    <div class="search-bar">
      <input
        type="text"
        placeholder="Введите запрос..."
        v-model="searchQuery"
      />
      <button @click="search">🔍</button>
    </div>
  </template>
  <style scoped>
  .search-bar {
    margin: 2rem auto;
    text-align: center;
  }
  
  input[type="text"] {
    width: 60%;
    padding: 0.8rem;
    font-size: 1rem;
    border-radius: 20px;
    background-color: #D9D9D9;
    border: none;
    outline: none;
    margin-right: 0.5rem;
  }
  
  button {
    background-color: #ccc;
    border: none;
    padding: 0.8rem 1.2rem;
    font-size: 1rem;
    border-radius: 20px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  button:hover {
    background-color: orange;
    color: #fff;
  }
  
  .suggestions {
    margin-top: 1rem;
  }
  
  .suggestions button {
    background-color: #555;
    color: #fff;
    margin: 0.3rem;
    padding: 0.5rem 1rem;
    border-radius: 15px;
    border: none;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  .suggestions button:hover {
    background-color: orange;
    color: #fff;
  }
  </style>
  
  <script>
  export default {
    name: 'SearchBar',
    data() {
      return {
        searchQuery: '',
      };
    },
    methods: {
      search() {
        this.$emit('search', this.searchQuery);
      },
      setQuery(query) {
        this.searchQuery = query;
        this.search();
      },
    },
  };
  </script>
  