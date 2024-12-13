<template>
    <footer class="footer">
      <p>ООО "хихи-хахатон"</p>
      <div class="socials">
        <a href="#">VK</a>
        <a href="#">Telegram</a>
        <a href="#">WhatsApp</a>
      </div>
    </footer>
  </template>
  <style scoped>
  .footer {
    background-color: #525252;
    color: #fff;
    text-align: center;
    padding: 1.5rem 0;
    margin-top: 2rem;
  }
  
  .footer p {
    margin: 0.5rem 0;
  }
  
  .socials {
    display: flex;
    justify-content: center;
    gap: 1rem;
    margin-top: 0.5rem;
  }
  
  .socials a {
    text-decoration: none;
    color: #fff;
    font-size: 1rem;
    transition: color 0.3s ease;
  }
  
  .socials a:hover {
    color: orange;
  }
  </style>
  
  <script>
  export default {
    name: 'AppFooter',
  };
  </script>
  