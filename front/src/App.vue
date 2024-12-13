<template>
  <div id="app">
    <AppHeader />
    <main class="main-content">
      <div class="hero">
        <h1>Генератор подарков</h1>
        <p>
          Сервис генератора подарков — это инновационная онлайн-платформа, созданная для упрощения процесса выбора подарков для любых случаев жизни. Мы понимаем, что найти идеальный подарок может быть сложно, особенно когда хочется удивить близких и порадовать их чем-то особенным.
        </p>
        <SearchBar @search="handleSearch" />
      </div>

      <div v-if="loading" class="loading">
        <iframe
          src="https://giphy.com/embed/O8qpTtOB7f8CmxyqQY"
          width="480"
          height="480"
          frameborder="0"
          class="giphy-embed"
          allowfullscreen>
        </iframe>
        <p>Идёт поиск...</p>
      </div>

      <div class="search-results" v-if="!loading && searchResults.length > 0">
        <h2 class="results-title">Подобрали варианты для вас</h2>
        <div class="product-grid">
          <div class="product-card" v-for="result in searchResults" :key="result.id">
            <div class="gift-icon">
              <img src="../public/gift_box.png" alt="Gift Icon" />
            </div>
            <div class="card-header">
              <img :src="result.image_url" alt="Product Image" />
            </div>
            <div class="card-body">
              <h3>{{ result.name }}</h3>
              <p class="price">{{ result.price }} ₽</p>
              <p class="explanation">{{ result.explanation }}</p>
            </div>
            <div class="card-footer">
              <a :href="result.product_url" target="_blank">
                <button>Перейти к товару</button>
              </a>
            </div>
          </div>
        </div>
      </div>
    </main>

    <button class="chat-button" @click="toggleChat">💬</button>
    <div class="chat-modal" v-if="chatOpen">
      <div class="chat-header">
        <h3>Чат с ботом</h3>
        <button class="close-button" @click="toggleChat">×</button>
      </div>
      <div class="chat-body">
        <div class="chat-messages">
          <div v-for="(message, index) in chatMessages" :key="index" :class="message.sender">
            <p>{{ message.text }}</p>
          </div>
        </div>
        <div class="chat-options">
          <button v-for="(option, index) in currentOptions" :key="index" @click="handleChatResponse(option)">
            {{ option }}
          </button>
        </div>
      </div>
    </div>

    <AppFooter />
  </div>
</template>
<script>
import AppHeader from './components/AppHeader.vue';
import AppFooter from './components/AppFooter.vue';
import SearchBar from './components/SearchBar.vue';

export default {
  name: 'App',
  components: {
    AppHeader,
    AppFooter,
    SearchBar,
  },
  data() {
    return {
      searchResults: [],
      loading: false,
      chatOpen: false,
      chatMessages: [],
      botOptions: ['Электроника', 'Бытовая техника', 'Для дома', 'Гаджеты'],
      currentOptions: ['Электроника', 'Бытовая техника', 'Для дома', 'Гаджеты'],
      currentQuestion: 'Привет! Какой подарок вы ищете?',
    };
  },
  methods: {
    toggleChat() {
      this.chatOpen = !this.chatOpen;
      if (this.chatOpen && this.chatMessages.length === 0) {
        this.chatMessages.push({ sender: 'bot', text: this.currentQuestion });
      }
    },
    async handleChatResponse(option) {
      this.chatMessages.push({ sender: 'client', text: option });

      if (option === 'Электроника') {
        this.currentQuestion = 'Отлично! Какую категорию электроники вы хотите рассмотреть?';
        this.currentOptions = ['Смартфоны', 'Ноутбуки', 'Телевизоры', 'Аудио техника'];
      } else if (option === 'Бытовая техника') {
        this.currentQuestion = 'Какой тип бытовой техники вас интересует?';
        this.currentOptions = ['Пылесосы', 'Стиральные машины', 'Холодильники', 'Микроволновки'];
      } else if (option === 'Для дома') {
        this.currentQuestion = 'Что именно для дома вы ищете?';
        this.currentOptions = ['Мебель', 'Освещение', 'Декор', 'Текстиль'];
      } else if (option === 'Гаджеты') {
        this.currentQuestion = 'Какие гаджеты вы хотите посмотреть?';
        this.currentOptions = ['Смарт-часы', 'Фитнес-трекеры', 'Электросамокаты', 'VR-устройства'];
      } else {
        this.currentQuestion = 'Спасибо за ваш выбор! Мы подберем для вас лучшие варианты.';
        this.currentOptions = [];
      }

      this.chatMessages.push({ sender: 'bot', text: this.currentQuestion });
      await this.fetchChatProducts();
    },
    async fetchChatProducts() {
      if (this.loading) return;
      this.loading = true;
      this.searchResults = [];
      try {
        const chatQuery = this.chatMessages
          .map((message) =>
            message.sender === 'bot' ? `Вопрос: ${message.text}` : `Ответ: ${message.text}`
          )
          .join('; ');

        const response = await fetch(
          `http://127.0.0.1:5000/api/products?query=${encodeURIComponent(chatQuery)}`
        );
        if (!response.ok) {
          throw new Error('Ошибка при загрузке данных');
        }

        const data = await response.json();
        this.searchResults = data.products;
      } catch (error) {
        console.error('Ошибка API:', error);
        this.searchResults = [];
      } finally {
        this.loading = false; 
      }
    },
    async handleSearch(query) {
      if (!query.trim() || this.loading) return;
      this.loading = true; 
      this.searchResults = []; 
      try {
        const response = await fetch(
          `http://127.0.0.1:5000/api/products?query=${encodeURIComponent(query)}`
        );
        if (!response.ok) {
          throw new Error('Ошибка при загрузке данных');
        }

        const data = await response.json();
        this.searchResults = data.products;
      } catch (error) {
        console.error('Ошибка API:', error);
        this.searchResults = [];
      } finally {
        this.loading = false; 
      }
    },
  },
};
</script>



<style>
.chat-modal {
  position: fixed;
  bottom: 80px;
  right: 20px;
  width: 350px;
  max-height: 500px;
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  z-index: 1000;
  animation: slideIn 0.3s ease-out;
}

.chat-header {
  background-color: #f48230;
  color: #ffffff;
  padding: 10px 15px;
  font-size: 18px;
  font-weight: bold;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.chat-header h3 {
  margin: 0;
}

.close-button {
  background: none;
  border: none;
  color: #ffffff;
  font-size: 18px;
  cursor: pointer;
}

.chat-body {
  padding: 15px;
  flex-grow: 1;
  overflow-y: auto;
  background-color: #f9f9f9;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.chat-body::-webkit-scrollbar {
  width: 6px;
}

.chat-body::-webkit-scrollbar-thumb {
  background-color: #ddd;
  border-radius: 10px;
}

.chat-messages {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.chat-messages .bot {
  align-self: flex-start;
  background-color: #e1f5fe;
  color: #333333;
  padding: 10px 15px;
  border-radius: 12px 12px 12px 0;
  max-width: 80%;
  font-size: 14px;
  line-height: 1.5;
}

.chat-messages .client {
  align-self: flex-end;
  background-color: #f48230;
  color: #ffffff;
  padding: 10px 15px;
  border-radius: 12px 12px 0 12px;
  max-width: 80%;
  font-size: 14px;
  line-height: 1.5;
}

.chat-options {
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding-top: 10px;
  border-top: 1px solid #ddd;
  background-color: #ffffff;
}

.chat-options button {
  background-color: #f48230;
  color: #ffffff;
  border: none;
  border-radius: 8px;
  padding: 10px 15px;
  cursor: pointer;
  font-size: 14px;
  text-align: center;
  transition: background-color 0.2s;
}

.chat-options button:hover {
  background-color: #ff8800;
}

.chat-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background-color: #f48230;
  color: #ffffff;
  border: none;
  border-radius: 50%;
  width: 60px;
  height: 60px;
  font-size: 24px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
  z-index: 1001;
}

.chat-button:hover {
  background-color: #ff8800;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

body {
  margin: 0;
  font-family: 'Arial', sans-serif;
  background-color: #525252;
  color: #fff;
}

.main-content {
  text-align: center;
  padding: 2rem;
}

.hero {
  max-width: 800px;
  margin: auto;
}

.hero h1 {
  color: #F48230;
  font-size: 2.5rem;
}

.hero p {
  font-size: 1.2rem;
  line-height: 1.8;
}

.loading {
  text-align: center;
  margin: 2rem 0;
}

.search-results {
  padding: 2rem;
}

.results-title {
  font-size: 1.8rem;
  color: #F48230;
  margin-bottom: 1.5rem;
}

.product-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 2rem;
  justify-items: center;
}

.product-card {
  position: relative;
  background-color: #AAAAAA;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 1rem;
  text-align: center;
}

.gift-icon {
  position: absolute;
  top: -20px;
  left: 20px;
  width: 50px;
  height: 50px;
  z-index: 1;
}

.gift-icon img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.card-header img {
  width: 50%;
  object-fit: cover;
  border-radius: 8px 8px 0 0;
}

.card-body {
  padding: 1rem;
}

.card-body h3 {
  font-size: 1.3rem;
  color: #000000;
  margin-bottom: 0.5rem;
}

.card-body .price {
  font-size: 1.2rem;
  color: #333333;
  margin-bottom: 1rem;
}

.card-body .explanation {
  font-size: 0.9rem;
  color: #444444;
  line-height: 1.5;
}

.card-footer {
  margin-top: 1rem;
}

.card-footer button {
  background-color: #F48230;
  border: none;
  padding: 0.6rem 1.2rem;
  border-radius: 5px;
  color: #ffffff;
  cursor: pointer;
  font-size: 1rem;
}

.card-footer button:hover {
  background-color: #ff8800;
}
</style> 