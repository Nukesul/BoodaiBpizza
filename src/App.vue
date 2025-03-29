<script>
import { ref, computed, onMounted } from 'vue';
import axios from 'axios';
import { useToast } from 'vue-toastification';

// Компоненты
const LoadingScreen = () => import('./components/LoadingScreen.vue');
const AppHeader = () => import('./components/AppHeader.vue');
const AppFooter = () => import('./components/AppFooter.vue');
const CategoriesSidebar = () => import('./components/CategoriesSidebar.vue');
const BranchCard = () => import('./components/BranchCard.vue');
const ProductList = () => import('./components/ProductList.vue');
const CartModal = () => import('./components/CartModal.vue');

export default {
  name: 'App',
  components: {
    LoadingScreen,
    AppHeader,
    AppFooter,
    CategoriesSidebar,
    BranchCard,
    ProductList,
    CartModal
  },
  setup() {
    const toast = useToast();
    const isLoading = ref(true);
    const showCart = ref(false);
    const isDarkMode = ref(false);
    const cart = ref([]);
    const categories = ref([]);
    const branches = ref([]);
    const selectedBranch = ref(null);
    const selectedCategory = ref(null);
    const searchQuery = ref('');
    
    const apiBaseUrl = import.meta.env.VITE_API_BASE_URL || 'https://boodaipizza-api.example.com/api/';

    const cartCount = computed(() => {
      return cart.value.reduce((sum, item) => sum + (item.quantity || 1), 0);
    });

    const cartTotal = computed(() => {
      return cart.value.reduce((sum, item) => sum + (item.price * item.quantity), 0).toFixed(2);
    });

    const loadData = async () => {
      try {
        const [categoriesResponse, branchesResponse] = await Promise.all([
          axios.get(`${apiBaseUrl}categories/`),
          axios.get(`${apiBaseUrl}branches/`)
        ]);
        
        categories.value = categoriesResponse.data.map(category => ({
          ...category,
          emoji: getEmojiForCategory(category.name),
        }));
        
        branches.value = branchesResponse.data;
      } catch (error) {
        console.error('Ошибка загрузки данных:', error);
        toast.error('Не удалось загрузить данные. Используем демо-данные.');
        loadDemoData();
      } finally {
        isLoading.value = false;
      }
    };

    const loadDemoData = () => {
      categories.value = [
        { id: 1, name: 'Пицца', emoji: '🍕' },
        { id: 2, name: 'Бургеры', emoji: '🍔' },
        { id: 3, name: 'Напитки', emoji: '🥤' },
        { id: 4, name: 'Закуски', emoji: '🍟' },
      ];
      
      branches.value = [
        { id: 1, name: 'Центр', address: 'ул. Ленина, 10', image: '/img/branch1.jpg' },
        { id: 2, name: 'Север', address: 'пр. Победы, 25', image: '/img/branch2.jpg' },
      ];
    };

    const getEmojiForCategory = (name) => {
      const emojiMap = { 
        'Пицца': '🍕', 
        'Бургеры': '🍔', 
        'Напитки': '🥤', 
        'Закуски': '🍟',
        'Десерты': '🍰',
        'Салаты': '🥗'
      };
      return emojiMap[name] || '🍽️';
    };

    const toggleCart = () => { 
      showCart.value = !showCart.value;
      if (showCart.value) {
        document.body.style.overflow = 'hidden';
      } else {
        document.body.style.overflow = '';
      }
    };

    const toggleDarkMode = () => { 
      isDarkMode.value = !isDarkMode.value;
      localStorage.setItem('darkMode', isDarkMode.value);
    };

    const selectBranch = (branchId) => { 
      selectedBranch.value = branchId;
      selectedCategory.value = null;
    };

    const selectCategory = (categoryId) => { 
      selectedCategory.value = categoryId === selectedCategory.value ? null : categoryId;
    };

    const addToCart = (product) => {
      const existingItem = cart.value.find(item => item.id === product.id);
      
      if (existingItem) {
        existingItem.quantity = (existingItem.quantity || 1) + (product.quantity || 1);
      } else {
        cart.value.push({ 
          ...product, 
          quantity: product.quantity || 1,
          branchId: selectedBranch.value
        });
      }
      
      toast.success(`${product.name} добавлен в корзину`);
    };

    const checkout = async () => {
      try {
        const order = {
          branchId: selectedBranch.value,
          items: cart.value,
          total: cartTotal.value,
          timestamp: new Date().toISOString(),
        };
        
        await axios.post(`${apiBaseUrl}orders/`, order);
        toast.success('Заказ успешно оформлен!');
        cart.value = [];
        showCart.value = false;
      } catch (error) {
        console.error('Ошибка оформления заказа:', error);
        toast.error('Не удалось оформить заказ. Попробуйте позже.');
      }
    };

    onMounted(() => {
      // Проверяем предпочтения пользователя
      isDarkMode.value = localStorage.getItem('darkMode') === 'true' || 
                        (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches);
      
      loadData();
    });

    return {
      isLoading,
      showCart,
      isDarkMode,
      cart,
      categories,
      branches,
      selectedBranch,
      selectedCategory,
      searchQuery,
      cartCount,
      cartTotal,
      toggleCart,
      toggleDarkMode,
      selectBranch,
      selectCategory,
      addToCart,
      checkout
    };
  }
};
</script>