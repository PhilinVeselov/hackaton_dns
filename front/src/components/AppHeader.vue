<template>
  <header class="header">
    <div class="logo">DNS</div>
  </header>
</template>
<style scoped>
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  background-color: #525252;
  color: #fff;
}

.logo {
  font-size: 1.5rem;
  font-weight: bold;
  color: #F48230;
}

nav ul {
  list-style: none;
  display: flex;
  margin: 0;
  padding: 0;
}

nav ul li {
  margin: 0 1rem;
  cursor: pointer;
  color: #fff;
  font-size: 1rem;
}

nav ul li:hover {
  color: orange;
}
</style>

<script>
export default {
  name: 'AppHeader',
};
</script>
