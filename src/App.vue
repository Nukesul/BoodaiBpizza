<template>
  <div id="app" class="flex flex-col min-h-screen font-sans antialiased overflow-x-hidden" :class="isDarkMode ? 'bg-gray-900 text-gray-100' : 'bg-cream text-coffee'">
    <!-- Header -->
    <header class="fixed w-full top-0 z-50 shadow-md bg-coffee text-cream">
      <div class="container mx-auto flex justify-between items-center py-4 px-4 sm:px-6">
        <div class="flex items-center space-x-3 cursor-pointer" @click="$router.push('/')">
          <div class="relative group">
            <svg class="w-12 h-12 text-cream transition-transform duration-500 group-hover:scale-110" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-1 4h2v5h3l-4 4-4-4h3V6zm1 12c-2.76 0-5-2.24-5-5h2c0 1.66 1.34 3 3 3s3-1.34 3-3h2c0 2.76-2.24 5-5 5z"/>
            </svg>
            <span class="absolute -top-2 -right-2 bg-cream text-coffee text-sm font-bold rounded-full h-5 w-5 flex items-center justify-center shadow-md transition-all duration-300 group-hover:bg-coffee group-hover:text-cream">C</span>
          </div>
          <h1 class="text-2xl sm:text-3xl font-bold tracking-wide text-cream animate-fade-in">Coffee Haven</h1>
        </div>
        <div class="flex items-center space-x-4 sm:space-x-6">
          <a href="https://t.me/coffeehaven" target="_blank" class="hover:text-cream-light transition-all duration-300 group">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm4.64 6.8c-.15 1.58-.8 5.42-1.13 7.19-.14.75-.42 1-.68 1.03-.58.05-1.02-.38-1.58-.75-2.51-1.56-3.93-2.39-4.76-2.91-.2-.13-.08-.4.12-.53.5-.31 1.36-.73 2.22-1.09 2.42-.66 4.79-1.39 4.79-1.39s-.02-.01-.04-.02c-.07-.03-.17-.03-.26.03z"/>
            </svg>
            <span class="absolute hidden group-hover:block bg-cream text-coffee text-xs font-medium px-2 py-1 rounded mt-6 -ml-4 transition-all duration-200">Telegram</span>
          </a>
          <a href="https://vk.com/coffeehaven" target="_blank" class="hover:text-cream-light transition-all duration-300 group">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 2c5.52 0 10 4.48 10 10s-4.48 10-10 10S2 17.52 2 12 6.48 2 12 2zm3.88 15.47c-.71-.17-1.31-.54-1.88-.96-.65-.49-1.31-1.03-1.88-1.66-.31-.35-.57-.73-.73-1.15-.15-.41-.23-.85-.23-1.31 0-.73.25-1.42.73-2.03.47-.61 1.15-1.08 1.88-1.42.73-.34 1.54-.51 2.35-.51h.31c.23 0 .46.08.65.23.19.15.31.38.31.61s-.12.46-.31.61c-.19.15-.42.23-.65.23h-.31c-.61 0-1.19.15-1.73.42-.54.27-1 .65-1.27 1.15-.27.5-.42 1.04-.42 1.58 0 .42.08.81.23 1.15.15.35.38.65.69.88.88.65 1.88 1.15 3.04 1.42.38.08.77.12 1.15.12.61 0 1.15-.23 1.58-.65.42-.42.65-.96.65-1.58 0-.23-.08-.46-.23-.65-.15-.19-.38-.31-.61-.31s-.46.12-.65.31c-.19.15-.31.38-.31.61 0 .27.08.54.23.77-.42.27-.88.42-1.35.42z"/>
            </svg>
            <span class="absolute hidden group-hover:block bg-cream text-coffee text-xs font-medium px-2 py-1 rounded mt-6 -ml-2 transition-all duration-200">VK</span>
          </a>
          <div class="relative group">
            <span class="text-sm font-medium hover:text-cream-light transition-all duration-300 cursor-pointer">+7 (999) 123-45-67</span>
            <span class="absolute hidden group-hover:block bg-cream text-coffee text-xs font-medium px-2 py-1 rounded mt-6 -ml-12 transition-all duration-200">Звоните!</span>
          </div>
          <button @click="toggleSidebar" class="hover:text-cream-light transition-colors duration-300">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="showSidebar ? 'M6 18L18 6M6 6l12 12' : 'M4 6h16M4 12h16m-7 6h7'"></path>
            </svg>
          </button>
          <button @click="toggleCart" class="relative hover:text-cream-light">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"></path>
            </svg>
            <span v-if="cartCount > 0" class="absolute -top-1 -right-1 bg-cream text-coffee text-xs rounded-full h-5 w-5 flex items-center justify-center shadow-sm animate-pulse">{{ cartCount }}</span>
          </button>
          <button @click="toggleDarkMode" class="hover:text-cream-light transition-colors duration-300">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="isDarkMode ? 'M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z' : 'M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z'"></path>
            </svg>
          </button>
        </div>
      </div>
    </header>

    <div class="flex flex-grow pt-20">
      <transition name="slide">
        <aside v-if="showSidebar" class="w-72 fixed h-full z-40 p-6 top-20 bg-cream shadow-xl rounded-r-2xl overflow-y-auto border-r-4 border-coffee transition-all duration-300">
          <h2 class="text-2xl font-bold text-coffee mb-6 animate-fade-in">Coffee Haven</h2>
          <div class="space-y-6">
            <div>
              <h3 class="text-lg font-semibold text-coffee mb-3 flex items-center space-x-2">
                <span>☕</span>
                <span>Филиалы</span>
              </h3>
              <ul class="space-y-2">
                <li v-for="branch in branches" :key="branch.id" @click="selectBranch(branch.id)" class="py-2 px-4 rounded-lg bg-coffee-light hover:bg-coffee cursor-pointer transition-all duration-300 transform hover:scale-102" :class="{ 'bg-coffee text-cream': selectedBranch === branch.id }">
                  {{ branch.name }}
                </li>
              </ul>
            </div>
            <div>
              <h3 class="text-lg font-semibold text-coffee mb-3 flex items-center space-x-2">
                <span>🍵</span>
                <span>Меню</span>
              </h3>
              <ul class="space-y-2">
                <li v-for="(category, index) in categories" :key="category.id" @click="selectCategory(category.id)" class="py-2 px-4 rounded-lg bg-coffee-light hover:bg-coffee cursor-pointer transition-all duration-300 transform hover:scale-102 flex items-center space-x-2" :class="{ 'bg-coffee text-cream': selectedCategory === category.id }" :style="{ 'animation-delay': `${index * 0.1}s` }">
                  <span class="text-xl">{{ category.emoji }}</span>
                  <span>{{ category.name }}</span>
                </li>
              </ul>
            </div>
          </div>
        </aside>
      </transition>

      <main class="flex-grow container mx-auto py-10 px-4 sm:px-6 transition-all duration-300" :class="{ 'ml-72': showSidebar && !isMobile }">
        <h2 class="text-3xl sm:text-4xl font-bold mb-8 text-center text-coffee animate-fade-in">Добро пожаловать в Coffee Haven</h2>
        
        <section v-if="!selectedBranch" class="mb-10">
          <h3 class="text-xl sm:text-2xl font-semibold text-coffee mb-5 text-center">Наши кофейни</h3>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
            <div v-for="branch in branches" :key="branch.id" @click="selectBranch(branch.id)" class="p-5 bg-cream rounded-lg shadow-md hover:shadow-lg transition-all duration-300 cursor-pointer hover:bg-coffee-light transform hover:scale-105">
              <h4 class="text-lg font-semibold text-coffee">{{ branch.name }}</h4>
              <p class="text-sm text-coffee-dark mt-1">{{ branch.address }}</p>
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
          <div class="flex justify-center mt-8 space-x-4">
            <button @click="prevPage" :disabled="currentPage === 1" class="px-5 py-2 bg-coffee text-cream rounded-md hover:bg-coffee-dark transition-all duration-300 disabled:bg-gray-400">Назад</button>
            <span class="text-lg text-coffee-dark">Страница {{ currentPage }} из {{ totalPages }}</span>
            <button @click="nextPage" :disabled="currentPage === totalPages" class="px-5 py-2 bg-coffee text-cream rounded-md hover:bg-coffee-dark transition-all duration-300 disabled:bg-gray-400">Вперёд</button>
          </div>
        </section>
      </main>
    </div>

    <footer class="py-6 bg-coffee text-cream">
      <div class="container mx-auto text-center">
        <p class="text-sm font-medium">© 2025 Coffee Haven. С любовью к кофе.</p>
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
      priceFilter: 1000,
      inStockOnly: false,
      sortOption: 'price-asc',
      searchQuery: '',
      currentPage: 1,
      totalPages: 1,
      isMobile: window.innerWidth <= 640,
      apiBaseUrl: 'https://coffeehaven-api.example.com/api/', // Замените на ваш API
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
        console.error('Ошибка загрузки категорий:', error);
      }
    },
    async fetchBranches() {
      try {
        const response = await axios.get(`${this.apiBaseUrl}branches/`);
        this.branches = response.data;
      } catch (error) {
        console.error('Ошибка загрузки филиалов:', error);
      }
    },
    getEmojiForCategory(name) {
      const emojiMap = { 'Кофе': '☕', 'Чай': '🍵', 'Десерты': '🍰', 'Закуски': '🥐' };
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
/* Кастомные цвета для кафе */
.bg-cream { background-color: #FDF6E9; }
.text-cream { color: #FDF6E9; }
.bg-coffee { background-color: #4A2C2A; }
.text-coffee { color: #4A2C2A; }
.bg-coffee-light { background-color: #EAD9C6; }
.text-coffee-dark { color: #3C231F; }
.text-cream-light { color: #FFF8E7; }

/* Общие стили */
#app { background: #FDF6E9; }
button { transition: all 0.3s ease; }
button:hover:not(:disabled) { transform: scale(1.03); }
.shadow-md { box-shadow: 0 4px 12px rgba(74, 44, 42, 0.1); }
.shadow-lg { box-shadow: 0 8px 16px rgba(74, 44, 42, 0.15); }
.shadow-xl { box-shadow: 0 12px 24px rgba(74, 44, 42, 0.2); }

/* Анимации */
@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}
.animate-fade-in { animation: fade-in 1s ease-in; }
@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.05); }
}
.animate-pulse { animation: pulse 2s infinite; }
.slide-enter-active, .slide-leave-active { transition: transform 0.4s ease; }
.slide-enter-from, .slide-leave-to { transform: translateX(-100%); }

/* Адаптивность */
@media (max-width: 640px) {
  .container { padding-left: 1rem; padding-right: 1rem; }
  h1 { font-size: 1.75rem; }
  h2 { font-size: 2rem; }
  h3 { font-size: 1.25rem; }
  .grid { grid-template-columns: 1fr; }
  aside { width: 100%; border-radius: 0; }
}
</style>