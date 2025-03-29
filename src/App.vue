<template>
  <div id="app" class="flex flex-col min-h-screen font-sans antialiased overflow-x-hidden" :class="isDarkMode ? 'bg-dark text-white' : 'bg-light text-dark'">
    <!-- Загрузочный экран -->
    <transition name="fade">
      <div v-if="isLoading" class="fixed inset-0 bg-orange-light flex items-center justify-center z-50">
        <div class="flex flex-col items-center">
          <svg class="w-12 h-12 text-orange-dark animate-spin" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 12a8 8 0 018-8v8h8a8 8 0 11-16 0z"/>
          </svg>
          <p class="mt-2 text-orange-dark font-semibold">Загрузка...</p>
        </div>
      </div>
    </transition>

    <!-- Header -->
    <header class="fixed w-full top-0 z-40 shadow-md bg-orange-dark text-white">
      <div class="container mx-auto flex justify-between items-center py-3 px-4 sm:px-6">
        <div class="flex items-center space-x-2 cursor-pointer" @click="$router.push('/')">
          <svg class="w-10 h-10 text-white transition-transform duration-300 hover:scale-110" fill="currentColor" viewBox="0 0 24 24">
            <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm-1-12h2v4l2.5 1.5-1 1.73L11 13v-5z"/>
          </svg>
          <h1 class="text-xl sm:text-2xl font-bold tracking-tight">Boodai Pizza</h1>
        </div>
        <div class="flex items-center space-x-3 sm:space-x-4">
          <button @click="toggleCart" class="relative hover:text-orange-light">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"></path>
            </svg>
            <span v-if="cartCount > 0" class="absolute -top-1 -right-1 bg-orange-light text-orange-dark text-xs rounded-full h-5 w-5 flex items-center justify-center shadow-md animate-pulse">{{ cartCount }}</span>
          </button>
          <button @click="toggleDarkMode" class="hover:text-orange-light transition-colors duration-300">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="isDarkMode ? 'M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z' : 'M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z'"></path>
            </svg>
          </button>
        </div>
      </div>
    </header>

    <!-- Главный контент -->
    <div class="flex flex-grow pt-16">
      <!-- Фиксированные категории при скролле -->
      <aside class="fixed top-16 w-full sm:w-64 h-16 sm:h-full z-30 bg-light shadow-md sm:shadow-lg overflow-x-auto sm:overflow-y-auto transition-all duration-300">
        <div class="flex sm:flex-col p-2 sm:p-4 space-x-2 sm:space-x-0 sm:space-y-2">
          <div v-for="category in categories" :key="category.id" @click="selectCategory(category.id)" class="flex-shrink-0 py-2 px-3 rounded-lg bg-orange-light hover:bg-orange-dark hover:text-white cursor-pointer transition-all duration-300 sm:hover:scale-105 flex items-center space-x-2" :class="{ 'bg-orange-dark text-white': selectedCategory === category.id }">
            <span class="text-lg">{{ category.emoji }}</span>
            <span class="text-sm sm:text-base font-semibold hidden sm:block">{{ category.name }}</span>
          </div>
        </div>
      </aside>

      <main class="flex-grow container mx-auto py-8 px-4 sm:px-6 sm:ml-64 transition-all duration-300">
        <section v-if="!selectedBranch">
          <h2 class="text-2xl sm:text-3xl font-bold mb-6 text-center text-orange-dark">Выбери точку</h2>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4 sm:gap-6">
            <div v-for="branch in branches" :key="branch.id" @click="selectBranch(branch.id)" class="p-4 bg-white rounded-lg shadow-md hover:shadow-lg transition-all duration-300 cursor-pointer hover:bg-orange-light">
              <h3 class="text-base sm:text-lg font-semibold text-orange-dark">{{ branch.name }}</h3>
              <p class="text-xs sm:text-sm text-dark">{{ branch.address }}</p>
            </div>
          </div>
        </section>

        <section v-else>
          <h2 class="text-2xl sm:text-3xl font-bold mb-6 text-center text-orange-dark">Boodai Pizza</h2>
          <div class="mb-4 flex flex-col sm:flex-row justify-between items-center gap-4">
            <input v-model="searchQuery" type="text" placeholder="Найди пиццу или бургер..." class="w-full sm:w-64 p-2 rounded-lg border border-orange-dark focus:outline-none focus:ring-2 focus:ring-orange-dark text-dark">
            <select v-model="sortOption" class="p-2 rounded-lg border border-orange-dark focus:outline-none text-dark">
              <option value="price-asc">Цена: ↑</option>
              <option value="price-desc">Цена: ↓</option>
              <option value="popular">Популярное</option>
            </select>
          </div>
          <ProductList
            :cart="cart"
            :branches="branches"
            @add-to-cart="addToCart"
            :selected-category="selectedCategory"
            :selected-branch="selectedBranch"
            :sort-option="sortOption"
            :search-query="searchQuery"
            :current-page="currentPage"
            @update-page="updatePage"
            :is-dark-mode="isDarkMode"
          />
          <div class="flex justify-center mt-6 space-x-4">
            <button @click="prevPage" :disabled="currentPage === 1" class="px-4 py-2 bg-orange-dark text-white rounded-lg hover:bg-orange-light hover:text-dark transition-all duration-300 disabled:bg-gray-500">Назад</button>
            <span class="text-base font-medium text-dark">Страница {{ currentPage }} из {{ totalPages }}</span>
            <button @click="nextPage" :disabled="currentPage === totalPages" class="px-4 py-2 bg-orange-dark text-white rounded-lg hover:bg-orange-light hover:text-dark transition-all duration-300 disabled:bg-gray-500">Вперёд</button>
          </div>
        </section>
      </main>
    </div>

    <!-- Footer -->
    <footer class="py-4 bg-orange-dark text-white">
      <div class="container mx-auto text-center">
        <p class="text-xs sm:text-sm font-semibold">© 2025 Boodai Pizza. Быстро и вкусно!</p>
      </div>
    </footer>

    <!-- Корзина -->
    <transition name="fade">
      <div v-if="showCart" class="fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center p-4">
        <div class="bg-white p-4 rounded-lg shadow-lg w-full max-w-md">
          <h3 class="text-lg font-bold text-orange-dark mb-4">Корзина</h3>
          <ul v-if="cart.length" class="space-y-3 max-h-64 overflow-y-auto">
            <li v-for="item in cart" :key="item.id" class="flex justify-between text-sm">
              <span>{{ item.name }} (x{{ item.quantity }})</span>
              <span>{{ (item.price * item.quantity).toFixed(2) }} ₽</span>
            </li>
          </ul>
          <p v-else class="text-dark text-sm">Корзина пуста</p>
          <div class="mt-4 flex justify-between">
            <button @click="toggleCart" class="px-3 py-2 bg-gray-300 rounded-lg hover:bg-gray-400 transition-all duration-300 text-sm">Закрыть</button>
            <button v-if="cart.length" @click="checkout" class="px-3 py-2 bg-orange-dark text-white rounded-lg hover:bg-orange-light hover:text-dark transition-all duration-300 text-sm">Оформить</button>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import axios from 'axios';
import ProductList from './components/ProductList.vue';

export default {
  name: 'App',
  components: { ProductList },
  data() {
    return {
      isLoading: true,
      showCart: false,
      isDarkMode: false,
      cart: [],
      categories: [],
      branches: [],
      selectedBranch: null,
      selectedCategory: null,
      sortOption: 'price-asc',
      searchQuery: '',
      currentPage: 1,
      totalPages: 1,
      apiBaseUrl: 'https://boodaipizza-api.example.com/api/', // Замените на реальный API
    };
  },
  computed: {
    cartCount() {
      return this.cart.reduce((sum, item) => sum + (item.quantity || 1), 0);
    },
  },
  async mounted() {
    await this.loadData();
    this.isLoading = false;
  },
  methods: {
    async loadData() {
      try {
        const [categoriesResponse, branchesResponse] = await Promise.all([
          axios.get(`${this.apiBaseUrl}categories/`),
          axios.get(`${this.apiBaseUrl}branches/`)
        ]);
        this.categories = categoriesResponse.data.map(category => ({
          ...category,
          emoji: this.getEmojiForCategory(category.name),
        }));
        this.branches = branchesResponse.data;
      } catch (error) {
        console.error('Ошибка загрузки данных:', error);
        this.categories = [
          { id: 1, name: 'Пицца', emoji: '🍕' },
          { id: 2, name: 'Бургеры', emoji: '🍔' },
          { id: 3, name: 'Напитки', emoji: '🥤' },
          { id: 4, name: 'Закуски', emoji: '🍟' },
        ];
        this.branches = [
          { id: 1, name: 'Центр', address: 'ул. Ленина, 10' },
          { id: 2, name: 'Север', address: 'пр. Победы, 25' },
        ];
      }
    },
    getEmojiForCategory(name) {
      const emojiMap = { 'Пицца': '🍕', 'Бургеры': '🍔', 'Напитки': '🥤', 'Закуски': '🍟' };
      return emojiMap[name] || '🍽️';
    },
    toggleCart() { this.showCart = !this.showCart; },
    toggleDarkMode() { this.isDarkMode = !this.isDarkMode; },
    selectBranch(branchId) { this.selectedBranch = branchId; },
    selectCategory(categoryId) { this.selectedCategory = categoryId; this.currentPage = 1; },
    addToCart(product) {
      const existingItem = this.cart.find(item => item.id === product.id);
      if (existingItem) {
        existingItem.quantity = (existingItem.quantity || 1) + (product.quantity || 1);
      } else {
        this.cart.push({ ...product, quantity: product.quantity || 1 });
      }
    },
    updatePage(pageCount) { this.totalPages = pageCount; },
    prevPage() { if (this.currentPage > 1) this.currentPage--; },
    nextPage() { if (this.currentPage < this.totalPages) this.currentPage++; },
    checkout() { alert('Оформление заказа в разработке!'); }, // Замените на реальную логику
  },
};
</script>

<style scoped>
/* Цвета */
.bg-light { background-color: #F5F5F5; }
.text-light { color: #F5F5F5; }
.bg-dark { background-color: #1A1A1A; }
.text-dark { color: #1A1A1A; }
.bg-orange-dark { background-color: #F4511E; }
.text-orange-dark { color: #F4511E; }
.bg-orange-light { background-color: #FFF3E0; }
.text-orange-light { color: #FFF3E0; }

/* Общие стили */
#app { background: #F5F5F5; }
button { transition: all 0.3s ease; }
button:hover:not(:disabled) { transform: scale(1.05); }
.shadow-md { box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08); }
.shadow-lg { box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1); }

/* Анимации */
@keyframes fade { from { opacity: 0; } to { opacity: 1; } }
.fade-enter-active, .fade-leave-active { transition: opacity 0.3s ease; }
.fade-enter-from, .fade-leave-to { opacity: 0; }
@keyframes spin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
.animate-spin { animation: spin 1s linear infinite; }
@keyframes pulse { 0%, 100% { transform: scale(1); } 50% { transform: scale(1.05); } }
.animate-pulse { animation: pulse 2s infinite; }

/* Адаптивность */
@media (max-width: 640px) {
  .container { padding-left: 1rem; padding-right: 1rem; }
  h1 { font-size: 1.5rem; }
  h2 { font-size: 1.75rem; }
  h3 { font-size: 1.25rem; }
  .grid { grid-template-columns: 1fr; }
  aside { height: 4rem; overflow-x: auto; white-space: nowrap; top: 60px; }
  .pt-16 { padding-top: 6rem; }
  .sm\:ml-64 { margin-left: 0; }
}
@media (min-width: 641px) and (max-width: 1024px) {
  h1 { font-size: 1.75rem; }
  h2 { font-size: 2.25rem; }
  h3 { font-size: 1.5rem; }
  .grid { grid-template-columns: repeat(2, 1fr); }
}
</style>