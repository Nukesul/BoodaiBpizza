<template>
  <div id="app" class="flex flex-col min-h-screen font-sans antialiased overflow-x-hidden" :class="isDarkMode ? 'bg-gray-900 text-gray-100' : 'bg-white text-gray-900'">
    <!-- Header -->
    <header class="fixed w-full top-0 z-50 shadow-lg bg-orange-500 text-white">
      <div class="container mx-auto flex justify-between items-center py-4 px-4 sm:px-6">
        <div class="flex items-center space-x-3 cursor-pointer" @click="$router.push('/')">
          <div class="relative group">
            <svg class="w-14 h-14 text-white transition-transform duration-500 group-hover:rotate-12" fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm-1-13h2v6h-2zm0 8h2v2h-2z"/>
            </svg>
            <span class="absolute -top-3 -right-3 bg-white text-orange-500 text-sm font-bold rounded-full h-6 w-6 flex items-center justify-center shadow-md transition-all duration-500 group-hover:bg-orange-600 group-hover:text-white group-hover:scale-125">T</span>
          </div>
          <h1 class="text-2xl sm:text-4xl font-extrabold tracking-tight text-shadow animate-pulse">TechHub</h1>
        </div>
        <div class="flex items-center space-x-4 sm:space-x-6">
          <a href="https://t.me/techhub" target="_blank" class="hover:text-orange-200 transition-all duration-300 group">
            <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm4.64 6.8c-.15 1.58-.8 5.42-1.13 7.19-.14.75-.42 1-.68 1.03-.58.05-1.02-.38-1.58-.75-2.51-1.56-3.93-2.39-4.76-2.91-.2-.13-.08-.4.12-.53.5-.31 1.36-.73 2.22-1.09 2.42-.66 4.79-1.39 4.79-1.39s-.02-.01-.04-.02c-.07-.03-.17-.03-.26.03z"/>
            </svg>
            <span class="absolute hidden group-hover:block bg-white text-orange-500 text-xs font-semibold px-2 py-1 rounded mt-8 -ml-4 transform translate-y-1 transition-all duration-200">Telegram</span>
          </a>
          <a href="https://vk.com/techhub" target="_blank" class="hover:text-orange-200 transition-all duration-300 group">
            <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path d="M12 2c5.52 0 10 4.48 10 10s-4.48 10-10 10S2 17.52 2 12 6.48 2 12 2zm3.88 15.47c-.71-.17-1.31-.54-1.88-.96-.65-.49-1.31-1.03-1.88-1.66-.31-.35-.57-.73-.73-1.15-.15-.41-.23-.85-.23-1.31 0-.73.25-1.42.73-2.03.47-.61 1.15-1.08 1.88-1.42.73-.34 1.54-.51 2.35-.51h.31c.23 0 .46.08.65.23.19.15.31.38.31.61s-.12.46-.31.61c-.19.15-.42.23-.65.23h-.31c-.61 0-1.19.15-1.73.42-.54.27-1 .65-1.27 1.15-.27.5-.42 1.04-.42 1.58 0 .42.08.81.23 1.15.15.35.38.65.69.88.88.65 1.88 1.15 3.04 1.42.38.08.77.12 1.15.12.61 0 1.15-.23 1.58-.65.42-.42.65-.96.65-1.58 0-.23-.08-.46-.23-.65-.15-.19-.38-.31-.61-.31s-.46.12-.65.31c-.19.15-.31.38-.31.61 0 .27.08.54.23.77-.42.27-.88.42-1.35.42z"/>
            </svg>
            <span class="absolute hidden group-hover:block bg-white text-orange-500 text-xs font-semibold px-2 py-1 rounded mt-8 -ml-2 transform translate-y-1 transition-all duration-200">VK</span>
          </a>
          <div class="relative group">
            <span class="text-sm sm:text-base font-semibold hover:text-orange-200 transition-all duration-300 cursor-pointer">+7 (999) 123-45-67</span>
            <span class="absolute hidden group-hover:block bg-white text-orange-500 text-xs font-semibold px-2 py-1 rounded mt-8 -ml-12 transform translate-y-1 transition-all duration-200">Позвони нам!</span>
          </div>
          <button @click="toggleSidebar" class="hover:text-orange-200 transition-colors duration-300">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="showSidebar ? 'M6 18L18 6M6 6l12 12' : 'M4 6h16M4 12h16m-7 6h7'"></path>
            </svg>
          </button>
          <button @click="toggleCart" class="relative hover:text-orange-200">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"></path>
            </svg>
            <span v-if="cartCount > 0" class="absolute -top-1 -right-1 bg-white text-orange-500 text-xs rounded-full h-5 w-5 flex items-center justify-center shadow-sm animate-bounce">{{ cartCount }}</span>
          </button>
          <button @click="toggleDarkMode" class="hover:text-orange-200 transition-colors duration-300">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="isDarkMode ? 'M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z' : 'M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z'"></path>
            </svg>
          </button>
        </div>
      </div>
    </header>

    <div class="flex flex-grow pt-20">
      <transition name="slide">
        <aside v-if="showSidebar" class="w-72 fixed h-full z-40 p-6 top-20 bg-white shadow-2xl rounded-r-3xl overflow-y-auto border-r-4 border-orange-500 transition-all duration-300">
          <h2 class="text-3xl font-extrabold text-orange-500 mb-8 text-shadow animate-bounce">TechHub</h2>
          <div class="space-y-8">
            <div>
              <h3 class="text-xl font-bold text-gray-800 mb-4 flex items-center space-x-2">
                <span>🏪</span>
                <span>Филиалы</span>
              </h3>
              <ul class="space-y-3">
                <li v-for="branch in branches" :key="branch.id" @click="selectBranch(branch.id)" class="py-3 px-4 rounded-xl bg-orange-50 hover:bg-orange-200 cursor-pointer transition-all duration-300 transform hover:scale-105 shadow-md" :class="{ 'bg-orange-300': selectedBranch === branch.id }">
                  {{ branch.name }}
                </li>
              </ul>
            </div>
            <div>
              <h3 class="text-xl font-bold text-gray-800 mb-4 flex items-center space-x-2">
                <span>🍽️</span>
                <span>Категории</span>
              </h3>
              <ul class="space-y-3">
                <li v-for="(category, index) in categories" :key="category.id" @click="selectCategory(category.id)" class="py-3 px-4 rounded-xl bg-orange-50 hover:bg-orange-200 cursor-pointer transition-all duration-300 transform hover:scale-105 shadow-md flex items-center space-x-3" :class="{ 'bg-orange-300': selectedCategory === category.id }" :style="{ 'animation-delay': `${index * 0.1}s` }">
                  <span class="text-2xl">{{ category.emoji }}</span>
                  <span class="text-gray-800 font-semibold">{{ category.name }}</span>
                </li>
              </ul>
            </div>
          </div>
        </aside>
      </transition>

      <main class="flex-grow container mx-auto py-10 px-4 sm:px-6 transition-all duration-300" :class="{ 'ml-72': showSidebar && !isMobile }">
        <h2 class="text-4xl sm:text-5xl font-extrabold mb-10 text-center text-orange-500 text-shadow animate-pulse">TechHub</h2>
        
        <section v-if="!selectedBranch" class="mb-12">
          <h3 class="text-2xl sm:text-3xl font-bold text-gray-800 mb-6 text-center">Наши филиалы</h3>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
            <div v-for="branch in branches" :key="branch.id" @click="selectBranch(branch.id)" class="p-6 bg-white rounded-xl shadow-lg hover:shadow-2xl transition-all duration-300 cursor-pointer hover:bg-orange-50 transform hover:scale-105">
              <h4 class="text-xl font-semibold text-orange-500">{{ branch.name }}</h4>
              <p class="text-sm text-gray-600 mt-2">{{ branch.address }}</p>
            </div>
          </div>
        </section>

        <section v-else>
          <ProductList
            :cart="cart"
            :compare-list="compareList"
            :favorites="favorites"
            :branches="branches"
            @add-to-cart="addToCart"
            @toggle-compare="toggleCompareItem"
            @toggle-favorite="toggleFavorite"
            :selected-category="selectedCategory"
            :selected-branch="selectedBranch"
            :price-filter="priceFilter"
            :in-stock-only="inStockOnly"
            :sort-option="sortOption"
            :search-query="searchQuery"
            :current-page="currentPage"
            @update-page="updatePage"
            :is-dark-mode="isDarkMode"
          />
          <div class="flex justify-center mt-10 space-x-4">
            <button @click="prevPage" :disabled="currentPage === 1" class="px-6 py-3 bg-orange-500 text-white rounded-lg hover:bg-orange-600 transition-all duration-300 disabled:bg-gray-400 shadow-md hover:shadow-lg transform hover:scale-105">Назад</button>
            <span class="text-lg font-medium text-gray-700">Страница {{ currentPage }} из {{ totalPages }}</span>
            <button @click="nextPage" :disabled="currentPage === totalPages" class="px-6 py-3 bg-orange-500 text-white rounded-lg hover:bg-orange-600 transition-all duration-300 disabled:bg-gray-400 shadow-md hover:shadow-lg transform hover:scale-105">Вперёд</button>
          </div>
        </section>
      </main>
    </div>

    <footer class="py-8 bg-orange-500 text-white">
      <div class="container mx-auto text-center">
        <p class="text-base font-semibold text-shadow">© 2025 TechHub. Все права защищены.</p>
      </div>
    </footer>
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
      showSidebar: false,
      showCart: false,
      isDarkMode: false,
      cart: [],
      compareList: [],
      favorites: [],
      categories: [],
      branches: [],
      selectedBranch: null,
      selectedCategory: null,
      priceFilter: 5000,
      inStockOnly: false,
      sortOption: 'price-asc',
      searchQuery: '',
      currentPage: 1,
      totalPages: 1,
      isMobile: window.innerWidth <= 640,
      apiBaseUrl: 'http://localhost:8000/api/',
    };
  },
  computed: {
    cartCount() {
      return this.cart.reduce((sum, item) => sum + (item.quantity || 1), 0);
    },
  },
  mounted() {
    this.fetchCategories();
    this.fetchBranches();
    window.addEventListener('resize', this.handleResize);
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.handleResize);
  },
  methods: {
    async fetchCategories() {
      try {
        const response = await axios.get(`${this.apiBaseUrl}categories/`);
        this.categories = response.data.map(category => ({
          ...category,
          emoji: this.getEmojiForCategory(category.name),
        }));
      } catch (error) {
        console.error('Ошибка при загрузке категорий:', error);
      }
    },
    async fetchBranches() {
      try {
        const response = await axios.get(`${this.apiBaseUrl}branches/`);
        this.branches = response.data;
      } catch (error) {
        console.error('Ошибка при загрузке филиалов:', error);
      }
    },
    getEmojiForCategory(name) {
      const emojiMap = { 'Пицца': '🍕', 'Напитки': '🥤', 'Десерты': '🍰' };
      return emojiMap[name] || '🍽️';
    },
    toggleSidebar() { this.showSidebar = !this.showSidebar; },
    toggleCart() { this.showCart = !this.showCart; },
    toggleDarkMode() { this.isDarkMode = !this.isDarkMode; },
    selectBranch(branchId) {
      this.selectedBranch = branchId;
      if (this.isMobile) this.showSidebar = false;
    },
    selectCategory(categoryId) {
      this.selectedCategory = categoryId;
      this.currentPage = 1;
      if (this.isMobile) this.showSidebar = false;
    },
    addToCart(product) {
      const existingItem = this.cart.find(item => item.id === product.id);
      if (existingItem) {
        existingItem.quantity = (existingItem.quantity || 1) + (product.quantity || 1);
      } else {
        this.cart.push({ ...product, quantity: product.quantity || 1 });
      }
    },
    toggleCompareItem(product) {
      const index = this.compareList.findIndex(item => item.id === product.id);
      if (index === -1) {
        this.compareList.push(product);
      } else {
        this.compareList.splice(index, 1);
      }
    },
    toggleFavorite(product) {
      const index = this.favorites.findIndex(item => item.id === product.id);
      if (index === -1) {
        this.favorites.push(product);
      } else {
        this.favorites.splice(index, 1);
      }
    },
    updatePage(pageCount) {
      this.totalPages = pageCount;
    },
    prevPage() {
      if (this.currentPage > 1) this.currentPage--;
    },
    nextPage() {
      if (this.currentPage < this.totalPages) this.currentPage++;
    },
    handleResize() {
      this.isMobile = window.innerWidth <= 640;
    },
  },
};
</script>

<style scoped>
#app { background: #fff; }
.bg-orange-500 { background: linear-gradient(to right, #f97316, #ea580c); }
button { transition: all 0.3s ease; }
button:hover:not(:disabled) { transform: scale(1.05); }
.shadow-lg { box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1); }
.shadow-2xl { box-shadow: 0 12px 24px rgba(0, 0, 0, 0.2); }
.shadow-md { box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08); }
.text-shadow { text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3); }
.slide-enter-active, .slide-leave-active { transition: transform 0.5s ease; }
.slide-enter-from, .slide-leave-to { transform: translateX(-100%); }
@keyframes bounce { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-10px); } }
.animate-bounce { animation: bounce 1s infinite; }
@keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.8; } }
.animate-pulse { animation: pulse 2s infinite; }
@media (max-width: 640px) {
  .container { padding-left: 1rem; padding-right: 1rem; }
  h1 { font-size: 1.5rem; }
  h2 { font-size: 2rem; }
  h3 { font-size: 1.25rem; }
  .grid { grid-template-columns: 1fr; }
  aside { width: 100%; border-radius: 0; }
}
</style>