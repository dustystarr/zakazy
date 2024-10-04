<template>
  <div class="product-list">
    <h1>Список товаров</h1>

    <div class="filters">

      <div class="filter-group">
        <label for="search">Поиск по имени:</label>
        <input
            type="text"
            id="search"
            v-model="searchQuery"
            placeholder="Введите название товара"
            class="input-field"
        />
      </div>

      <!-- Фильтрация по категориям (чекбоксы) -->
      <div class="filter-group">
        <label>Категории:</label>
        <div v-for="category in availableCategories" :key="category">
          <input
              type="checkbox"
              :id="`category-${category}`"
              :value="category"
              v-model="selectedCategories"
          />
          <label :for="`category-${category}`">{{ category }}</label>
        </div>
      </div>


      <div class="filter-group">
        <label for="sort">Сортировка по:</label>
        <select id="sort" v-model="sortOption" class="input-field">
          <option value="">-- Выберите --</option>
          <option value="price-asc">Цена: по возрастанию</option>
          <option value="price-desc">Цена: по убыванию</option>
          <option value="name-asc">Название: А-Я</option>
          <option value="name-desc">Название: Я-А</option>
        </select>
      </div>
    </div>


    <div class="products-container">
      <div v-for="product in filteredAndSortedProducts" :key="product.id" class="product">
        <img :src="product.imageUrl" :alt="`Изображение ${product.name}`" class="product-image" />
        <h2>{{ product.name }}</h2>
        <p>Категория: {{ product.category }}</p>
        <p>Цена: {{ product.price }} ₽</p>
        <button @click="addToCart(product)" class="btn-add">Добавить в корзину</button>
      </div>
    </div>


    <div class="cart">
      <h2>Корзина</h2>
      <div v-if="cart.length > 0">
        <ul>
          <li v-for="item in cart" :key="item.product.id">
            {{ item.product.name }} - {{ item.product.price }} ₽
            <div class="quantity-controls">
              <button @click="decreaseQuantity(item)">-</button>
              <span>{{ item.quantity }}</span>
              <button @click="increaseQuantity(item)">+</button>
            </div>
            <button @click="removeFromCart(item)" class="btn-remove">Удалить</button>
          </li>
        </ul>
        <p>Сумма: {{ totalPrice }} ₽</p>
        <button @click="checkout" class="btn-checkout">Оформить заказ</button>
      </div>
      <p v-else>Корзина пуста.</p>
    </div>


    <OrderForm v-if="showOrderForm" />
  </div>
</template>

<script>
import OrderForm from './OrderForm.vue';

export default {
  name: 'ProductList',
  components: {
    OrderForm
  },
  data() {
    return {
      products: [
        {
          id: 1,
          name: 'Apple iPhone 13',
          price: 30000,
          category: 'Смартфоны',
          imageUrl: require('@/assets/download11.jpeg')
        },
        {
          id: 2,
          name: 'Apple iPhone 14',
          price: 39999,
          category: 'Смартфоны',
          imageUrl: require('@/assets/apple-iphone-16-128-gb-ultramarin.jpeg')
        },
        {
          id: 3,
          name: 'Apple iPhone 15',
          price: 49999,
          category: 'Смартфоны',
          imageUrl: require('@/assets/apple-iphone-16-512-gb-birjuzovyj.jpeg')
        },
        {
          id: 4,
          name: 'Samsung Galaxy S21',
          price: 35000,
          category: 'Смартфоны',
          imageUrl: require('@/assets/samsung-galaxy-s21.jpeg')
        },
        {
          id: 5,
          name: 'MacBook Pro 16"',
          price: 120000,
          category: 'Ноутбуки',
          imageUrl: require('@/assets/macbook-pro-16.jpeg')
        }
      ],
      cart: [],
      showOrderForm: false,

      searchQuery: '',
      selectedCategories: [],
      sortOption: ''
    };
  },
  computed: {
    availableCategories() {
      const categories = this.products.map(product => product.category);
      return [...new Set(categories)];
    },
    filteredProducts() {
      let filtered = this.products;

      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase();
        filtered = filtered.filter(product =>
            product.name.toLowerCase().includes(query)
        );
      }

      if (this.selectedCategories.length > 0) {
        filtered = filtered.filter(product =>
            this.selectedCategories.includes(product.category)
        );
      }

      return filtered;
    },
    sortedProducts() {
      let sorted = [...this.filteredProducts];

      switch (this.sortOption) {
        case 'price-asc':
          sorted.sort((a, b) => a.price - b.price);
          break;
        case 'price-desc':
          sorted.sort((a, b) => b.price - a.price);
          break;
        case 'name-asc':
          sorted.sort((a, b) => a.name.localeCompare(b.name));
          break;
        case 'name-desc':
          sorted.sort((a, b) => b.name.localeCompare(a.name));
          break;
        default:
          break;
      }

      return sorted;
    },
    filteredAndSortedProducts() {
      return this.sortedProducts;
    },
    totalPrice() {
      return this.cart.reduce((sum, item) => sum + item.product.price * item.quantity, 0);
    }
  },
  methods: {
    addToCart(product) {
      const cartItem = this.cart.find(item => item.product.id === product.id);
      if (cartItem) {
        cartItem.quantity += 1;
      } else {
        this.cart.push({ product, quantity: 1 });
      }
    },
    increaseQuantity(item) {
      item.quantity += 1;
    },
    decreaseQuantity(item) {
      if (item.quantity > 1) {
        item.quantity -= 1;
      } else {
        this.removeFromCart(item);
      }
    },
    removeFromCart(item) {
      this.cart = this.cart.filter(cartItem => cartItem.product.id !== item.product.id);
    },
    checkout() {
      this.showOrderForm = true;
    }
  },
  watch: {
    searchQuery() {
    },
    selectedCategories() {
    },
    sortOption(newVal, oldVal) {
      console.log(`Сортировка изменена с ${oldVal} на ${newVal}`);
    }
  }
};
</script>

<style scoped>
.product-list {
  padding: 20px;
  font-family: Arial, sans-serif;
}

.filters {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  margin-bottom: 20px;
  background-color: #f9f9f9;
  padding: 15px;
  border-radius: 8px;
}

.filter-group {
  display: flex;
  flex-direction: column;
}

.filter-group label {
  font-weight: bold;
  margin-bottom: 5px;
}

.input-field {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.filters .filter-group input[type="checkbox"] {
  margin-right: 5px;
}

.products-container {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
}

.product {
  text-align: center;
  margin: 10px;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #fff;
  width: 200px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.product-image {
  width: 100%;
  height: auto;
  margin-bottom: 10px;
}

.btn-add {
  background-color: #4caf50;
  color: white;
  border: none;
  padding: 8px 12px;
  cursor: pointer;
  border-radius: 4px;
}

.btn-add:hover {
  background-color: #45a049;
}

.cart {
  margin-top: 30px;
  border-top: 2px solid #ccc;
  padding-top: 20px;
}

.quantity-controls {
  display: inline-flex;
  align-items: center;
  margin-left: 10px;
}

.quantity-controls button {
  padding: 4px 8px;
  margin: 0 5px;
  border: none;
  background-color: #e0e0e0;
  cursor: pointer;
  border-radius: 3px;
}

.quantity-controls button:hover {
  background-color: #d5d5d5;
}

.btn-remove {
  background-color: #f44336;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 4px;
  margin-left: 10px;
}

.btn-remove:hover {
  background-color: #e53935;
}

.btn-checkout {
  background-color: #2196f3;
  color: white;
  border: none;
  padding: 10px 15px;
  cursor: pointer;
  border-radius: 4px;
}

.btn-checkout:hover {
  background-color: #1e88e5;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin-bottom: 15px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.filters .filter-group input[type="checkbox"] {
  margin-right: 5px;
}

@media (max-width: 768px) {
  .filters {
    flex-direction: column;
  }

  .product {
    width: 100%;
  }
}
</style>
