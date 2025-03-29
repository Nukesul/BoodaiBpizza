<template>
  <div class="relative">
    <div v-if="isLoading" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="animate-spin rounded-full h-16 w-16 border-t-4 border-b-4 border-indigo-500"></div>
    </div>

    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
      <div v-if="!paginatedProducts || paginatedProducts.length === 0" class="col-span-full text-center py-12" :class="isDarkMode ? 'text-gray-400' : 'text-gray-500'">
        <p class="text-xl font-medium">Нет товаров для отображения</p>
        <p class="mt-2 text-sm">Попробуйте изменить фильтры или поисковый запрос</p>
      </div>
      <div
        v-else
        v-for="product in paginatedProducts"
        :key="product.id"
        class="product-card border rounded-2xl overflow-hidden transition-all duration-500 hover:shadow-2xl hover:-translate-y-2 transform"
        :class="isDarkMode ? 'bg-gray-800 border-gray-700' : 'bg-white border-gray-200'"
      >
        <div class="relative">
          <img
            :src="getImageUrl(product.image)"
            :alt="product.name"
            class="w-full h-64 object-cover transition-transform duration-500 hover:scale-105 cursor-pointer"
            @click="openPreview(product)"
            @error="handleImageError"
          >
          <div v-if="product.discount > 0" class="absolute top-3 left-3 bg-green-500 text-white text-xs font-semibold px-3 py-1 rounded-full shadow-md">
            Скидка {{ product.discount }}%
          </div>
          <div v-if="product.stock === 0" class="absolute top-3 right-3 bg-red-500 text-white text-xs font-semibold px-3 py-1 rounded-full shadow-md">
            Нет в наличии
          </div>
        </div>
        <div class="p-5">
          <h3 class="text-lg font-semibold mb-2 transition-colors duration-300 hover:text-indigo-500 line-clamp-1" :class="isDarkMode ? 'text-gray-100' : 'text-gray-800'">
            {{ product.name }}
          </h3>
          <div class="flex items-center mb-3">
            <svg v-for="star in 5" :key="star" class="w-4 h-4 mr-1" :class="star <= (product.rating || 0) ? 'text-yellow-400' : 'text-gray-300'" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
              <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3 .921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z"></path>
            </svg>
            <span class="text-sm ml-2" :class="isDarkMode ? 'text-gray-400' : 'text-gray-500'">({{ product.rating || 0 }})</span>
          </div>
          <p class="text-sm line-clamp-2 mb-4 leading-relaxed" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">{{ product.description }}</p>
          <div class="flex justify-between items-center mb-4">
            <div>
              <span v-if="product.discount > 0" class="text-gray-500 line-through text-sm mr-2">${{ product.base_price }}</span>
              <span class="text-indigo-600 font-bold text-xl">${{ calculatePrice(product) }}</span>
            </div>
            <span class="text-sm" :class="product.stock > 0 ? (isDarkMode ? 'text-gray-400' : 'text-gray-500') : 'text-red-500'">В наличии: {{ product.stock }}</span>
          </div>
          <div class="flex space-x-3">
            <button
              @click="$emit('add-to-cart', { ...product, quantity: product.tempQuantity || 1 }, $event)"
              :disabled="product.stock === 0"
              class="flex-1 bg-gradient-to-r from-indigo-500 to-purple-500 text-white py-2 rounded-lg hover:from-indigo-600 hover:to-purple-600 transition-all duration-300 disabled:bg-gray-400 disabled:cursor-not-allowed font-medium shadow-sm hover:shadow-md"
            >
              {{ product.stock > 0 ? 'В корзину' : 'Нет в наличии' }}
            </button>
            <button
              @click="$emit('toggle-compare', product)"
              class="p-2 border rounded-lg hover:bg-gradient-to-r hover:from-indigo-500 hover:to-purple-500 hover:text-white transition-all duration-300 shadow-sm hover:shadow-md"
              :class="isDarkMode ? (compareList.some(item => item && item.id === product.id) ? 'bg-gradient-to-r from-indigo-600 to-purple-600 text-white' : 'bg-gray-700 border-gray-600') : (compareList.some(item => item && item.id === product.id) ? 'bg-gradient-to-r from-indigo-100 to-purple-100 text-indigo-600' : 'bg-white border-gray-200')"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 4h18M3 12h18M3 20h18"></path>
              </svg>
            </button>
            <button
              @click="$emit('toggle-favorite', product)"
              class="p-2 border rounded-lg hover:bg-gradient-to-r hover:from-red-500 hover:to-pink-500 hover:text-white transition-all duration-300 shadow-sm hover:shadow-md"
              :class="isDarkMode ? (favorites.some(item => item && item.id === product.id) ? 'bg-gradient-to-r from-red-600 to-pink-600 text-white' : 'bg-gray-700 border-gray-600') : (favorites.some(item => item && item.id === product.id) ? 'bg-gradient-to-r from-red-100 to-pink-100 text-red-600' : 'bg-white border-gray-200')"
            >
              <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.318 6.318a4.5 4.5 0 000 6.364L12 20.364l7.682-7.682a4.5 4.5 0 00-6.364-6.364L12 7.636l-1.318-1.318a4.5 4.5 0 00-6.364 0z"></path>
              </svg>
            </button>
          </div>
        </div>
      </div>
    </div>

    <div v-if="totalPages > 1" class="mt-8 flex justify-center items-center space-x-2">
      <button
        @click="changePage(currentPage - 1)"
        :disabled="currentPage === 1"
        class="px-4 py-2 border rounded-lg transition-all duration-300"
        :class="currentPage === 1 ? 'bg-gray-200 text-gray-500 cursor-not-allowed' : 'bg-indigo-500 text-white hover:bg-indigo-600'"
      >
        Назад
      </button>
      <span class="text-sm" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">
        Страница {{ currentPage }} из {{ totalPages }}
      </span>
      <button
        @click="changePage(currentPage + 1)"
        :disabled="currentPage === totalPages"
        class="px-4 py-2 border rounded-lg transition-all duration-300"
        :class="currentPage === totalPages ? 'bg-gray-200 text-gray-500 cursor-not-allowed' : 'bg-indigo-500 text-white hover:bg-indigo-600'"
      >
        Вперед
      </button>
    </div>

    <transition name="fade">
      <div v-if="selectedProduct" class="fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center z-50" @click.self="closePreview">
        <div class="modal-container bg-white dark:bg-gray-900 rounded-3xl p-6 max-w-4xl w-full shadow-2xl transform transition-all duration-500">
          <div class="flex justify-between items-center mb-6">
            <h3 class="text-2xl font-bold tracking-tight" :class="isDarkMode ? 'text-gray-100' : 'text-gray-800'">{{ selectedProduct.name }}</h3>
            <button @click="closePreview" class="text-gray-500 hover:text-gray-700 dark:text-gray-300 dark:hover:text-gray-100 transition-colors duration-300">
              <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
              </svg>
            </button>
          </div>
          <div class="flex flex-col md:flex-row gap-6">
            <div class="w-full md:w-1/2">
              <img
                :src="getImageUrl(selectedProduct.image)"
                :alt="selectedProduct.name"
                class="w-full h-96 object-cover rounded-2xl shadow-md"
                @error="handleImageError"
              >
            </div>
            <div class="w-full md:w-1/2 space-y-6">
              <div class="p-5 bg-gradient-to-br from-gray-50 to-gray-100 dark:from-gray-800 dark:to-gray-700 rounded-2xl shadow-sm">
                <h4 class="text-lg font-semibold mb-3" :class="isDarkMode ? 'text-gray-100' : 'text-gray-800'">Описание</h4>
                <p class="text-sm leading-relaxed" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">{{ selectedProduct.description }}</p>
              </div>
              <div class="p-5 bg-gradient-to-br from-indigo-50 to-purple-50 dark:from-indigo-900 dark:to-purple-900 rounded-2xl shadow-sm">
                <h4 class="text-lg font-semibold mb-4" :class="isDarkMode ? 'text-gray-100' : 'text-gray-800'">Оформить заказ</h4>
                <div class="space-y-4">
                  <div>
                    <label class="block text-sm font-medium mb-2" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">Тип доставки</label>
                    <select
                      v-model="orderForm.delivery"
                      @change="updateDeliveryOptions"
                      class="w-full p-3 border rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500/50 transition-all duration-300 shadow-sm bg-white dark:bg-gray-800 dark:border-gray-600 dark:text-gray-100 text-gray-700"
                    >
                      <option value="standard">Стандартная доставка</option>
                      <option value="express">Экспресс-доставка</option>
                      <option value="pickup">Самовывоз</option>
                    </select>
                  </div>
                  <div v-if="orderForm.delivery === 'pickup'">
                    <label class="block text-sm font-medium mb-2" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">Выберите филиал</label>
                    <select
                      v-model="orderForm.branch"
                      class="w-full p-3 border rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500/50 transition-all duration-300 shadow-sm bg-white dark:bg-gray-800 dark:border-gray-600 dark:text-gray-100 text-gray-700"
                    >
                      <option v-for="branch in branches" :key="branch.id" :value="branch.id">
                        {{ branch.name }} ({{ branch.address }})
                      </option>
                    </select>
                  </div>
                  <div v-if="orderForm.delivery !== 'pickup'">
                    <label class="block text-sm font-medium mb-2" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">Адрес доставки</label>
                    <textarea
                      v-model="orderForm.address"
                      class="w-full p-3 border rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500/50 transition-all duration-300 shadow-sm bg-white dark:bg-gray-800 dark:border-gray-600 dark:text-gray-100 text-gray-700"
                      rows="3"
                    ></textarea>
                  </div>
                  <div>
                    <label class="block text-sm font-medium mb-2" :class="isDarkMode ? 'text-gray-300' : 'text-gray-600'">Промокод</label>
                    <input
                      v-model="orderForm.promo_code"
                      type="text"
                      placeholder="Введите промокод"
                      class="w-full p-3 border rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500/50 transition-all duration-300 shadow-sm bg-white dark:bg-gray-800 dark:border-gray-600 dark:text-gray-100 text-gray-700"
                    >
                  </div>
                  <div class="flex justify-between items-center">
                    <span class="text-xl font-bold text-indigo-600 dark:text-indigo-400">Итого: ${{ calculatePrice(selectedProduct) }}</span>
                    <button
                      @click="submitOrder"
                      :disabled="selectedProduct.stock === 0 || (orderForm.delivery !== 'pickup' && !orderForm.address)"
                      class="bg-gradient-to-r from-indigo-500 to-purple-500 text-white py-2 px-6 rounded-lg hover:from-indigo-600 hover:to-purple-600 transition-all duration-300 disabled:bg-gray-400 disabled:cursor-not-allowed shadow-md hover:shadow-lg transform hover:-translate-y-1"
                    >
                      Оформить заказ
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    cart: { type: Array, default: () => [] },
    compareList: { type: Array, default: () => [] },
    favorites: { type: Array, default: () => [] },
    branches: { type: Array, default: () => [] },
    selectedCategory: { type: Number, default: null },
    selectedBranch: { type: Number, default: null },
    priceFilter: { type: Number, default: 5000 },
    inStockOnly: { type: Boolean, default: false },
    sortOption: { type: String, default: 'price-asc' },
    searchQuery: { type: String, default: '' },
    currentPage: { type: Number, default: 1 },
    isDarkMode: { type: Boolean, default: false },
  },
  emits: ['add-to-cart', 'toggle-compare', 'toggle-favorite', 'update-page'],
  data() {
    return {
      products: [],
      isLoading: false,
      selectedProduct: null,
      orderForm: {
        delivery: 'standard',
        branch: null,
        address: '',
        promo_code: '',
      },
      baseUrl: 'http://localhost:8000/api/',
    };
  },
  computed: {
    filteredProducts() {
      let filtered = this.products || [];
      if (this.selectedCategory) {
        filtered = filtered.filter(product => product && product.category === this.selectedCategory);
      }
      if (this.selectedBranch) {
        filtered = filtered.filter(product => product && product.branch === this.selectedBranch);
      }
      filtered = filtered.filter(product => product && parseFloat(this.calculatePrice(product) || 0) <= this.priceFilter);
      if (this.inStockOnly) {
        filtered = filtered.filter(product => product && product.stock > 0);
      }
      if (this.searchQuery) {
        filtered = filtered.filter(product =>
          product &&
          ((product.name || '').toLowerCase().includes(this.searchQuery.toLowerCase()) ||
           (product.description || '').toLowerCase().includes(this.searchQuery.toLowerCase()))
        );
      }
      switch (this.sortOption) {
        case 'price-asc':
          return filtered.sort((a, b) => parseFloat(this.calculatePrice(a) || 0) - parseFloat(this.calculatePrice(b) || 0));
        case 'price-desc':
          return filtered.sort((a, b) => parseFloat(this.calculatePrice(b) || 0) - parseFloat(this.calculatePrice(a) || 0));
        case 'name':
          return filtered.sort((a, b) => (a.name || '').localeCompare(b.name || ''));
        default:
          return filtered;
      }
    },
    paginatedProducts() {
      const start = (this.currentPage - 1) * 9;
      const end = start + 9;
      return (this.filteredProducts || [])
        .filter(product => product && product.id)
        .slice(start, end)
        .map(product => ({
          ...product,
          tempQuantity: product.tempQuantity || 1,
        }));
    },
    totalPages() {
      const total = Math.ceil((this.filteredProducts || []).length / 9);
      this.$emit('update-page', total);
      return total;
    },
  },
  watch: {
    selectedCategory: 'fetchProducts',
    selectedBranch: 'fetchProducts',
    currentPage() {
      window.scrollTo({ top: 0, behavior: 'smooth' });
    },
  },
  mounted() {
    this.fetchProducts();
  },
  methods: {
    async fetchProducts() {
      this.isLoading = true;
      try {
        const response = await axios.get(`${this.baseUrl}pizzas/by_category/`, {
          params: {
            category_id: this.selectedCategory,
            branch_id: this.selectedBranch,
            page: this.currentPage,
          },
        });
        this.products = response.data.results.map(product => ({
          ...product,
          price: this.calculatePrice(product),
        }));
      } catch (error) {
        console.error('Error fetching products:', error);
        this.products = [];
      } finally {
        this.isLoading = false;
      }
    },
    calculatePrice(product) {
      const basePrice = parseFloat(product.base_price || 0);
      const discount = parseFloat(product.discount || 0);
      return discount > 0 ? (basePrice * (100 - discount) / 100).toFixed(2) : basePrice.toFixed(2);
    },
    getImageUrl(image) {
      if (!image) return 'https://via.placeholder.com/300x200?text=No+Image';
      return image.startsWith('http') ? image : `${this.baseUrl}${image}`;
    },
    handleImageError(event) {
      event.target.src = 'https://via.placeholder.com/300x200?text=Image+Not+Found';
    },
    openPreview(product) {
      if (product) {
        this.selectedProduct = { ...product, tempQuantity: product.tempQuantity || 1 };
        this.orderForm.branch = this.branches.length > 0 ? this.branches[0].id : null;
      }
    },
    closePreview() {
      this.selectedProduct = null;
      this.orderForm = { delivery: 'standard', branch: null, address: '', promo_code: '' };
    },
    updateDeliveryOptions() {
      if (this.orderForm.delivery === 'pickup') {
        this.orderForm.address = '';
      } else {
        this.orderForm.branch = null;
      }
    },
    async submitOrder() {
      try {
        const orderData = {
          customer_name: 'Клиент', // Можно добавить поле для имени в форму
          address: this.orderForm.delivery === 'pickup' ? '' : this.orderForm.address,
          branch: this.orderForm.delivery === 'pickup' ? this.orderForm.branch : null,
          delivery: this.orderForm.delivery,
          comment: '',
          total: this.calculatePrice(this.selectedProduct),
          items: JSON.stringify([{ name: this.selectedProduct.name, quantity: this.selectedProduct.tempQuantity }]),
          status: 'Pending',
          promo_code: this.orderForm.promo_code || null,
        };
        const response = await axios.post(`${this.baseUrl}orders/`, orderData);
        console.log('Order submitted:', response.data);
        alert('Заказ успешно оформлен!');
        this.closePreview();
      } catch (error) {
        console.error('Error submitting order:', error);
        alert('Ошибка при оформлении заказа.');
      }
    },
    changePage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.$emit('update-current-page', page);
        this.fetchProducts();
      }
    },
  },
};
</script>

<style scoped>
.product-card { transition: all 0.4s ease; }
.product-card:hover { transform: translateY(-8px); box-shadow: 0 20px 40px rgba(0, 0, 0, 0.15); }
.modal-container { max-height: 90vh; overflow-y: auto; scrollbar-width: thin; scrollbar-color: #a0aec0 #f1f5f9; }
.modal-container::-webkit-scrollbar { width: 8px; }
.modal-container::-webkit-scrollbar-track { background: #f1f5f9; border-radius: 10px; }
.modal-container::-webkit-scrollbar-thumb { background: #a0aec0; border-radius: 10px; }
.modal-container::-webkit-scrollbar-thumb:hover { background: #718096; }
button { transition: all 0.3s ease; }
button:hover:not(:disabled) { transform: scale(1.03); }
button:active:not(:disabled) { transform: scale(0.97); }
.fade-enter-active, .fade-leave-active { transition: opacity 0.5s ease, transform 0.5s ease; }
.fade-enter-from, .fade-leave-to { opacity: 0; transform: scale(0.98); }
select { appearance: none; background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 24 24' stroke='currentColor'%3E%3Cpath stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='M19 9l-7 7-7-7'/%3E%3C/svg%3E"); background-repeat: no-repeat; background-position: right 1rem center; background-size: 1.2em; padding-right: 2.5rem; transition: all 0.3s ease; }
select:hover { border-color: #6366f1; }
@media (max-width: 1024px) { .grid { grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); } }
@media (max-width: 768px) { .modal-container { padding: 1.5rem; max-width: 95%; } .modal-container > div { flex-direction: column; } .modal-container .w-full.md\:w-1\/2 { width: 100%; } .modal-container .h-96 { height: 280px; } .product-card { margin: 0 auto; max-width: 90%; } }
@media (max-width: 480px) { .product-card { max-width: 100%; } .grid { grid-template-columns: 1fr; } }
h3, h4 { font-family: 'Inter', sans-serif; }
p { font-family: 'Inter', sans-serif; }
</style>