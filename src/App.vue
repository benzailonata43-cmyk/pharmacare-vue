<script setup>
import { ref, computed, watch } from 'vue'
import ProductCard from './components/ProductCard.vue'
import CartItem from './components/CartItem.vue'
import CheckoutForm from './components/CheckoutForm.vue'
import { products } from './data/products.js'

// Пошук, фільтрація та сортування
const search = ref('')
const selectedCategory = ref('Усі')
const sortOrder = ref('default')

// Відкриття кошика
const isCartOpen = ref(false)
const isCheckoutOpen = ref(false)
const isOrderSuccess = ref(false)
const orderNumber = ref('')

// Завантаження кошика з localStorage
function loadCart() {
  try {
    const saved = localStorage.getItem('pharmacare-cart')
    if (!saved) return []

    const parsed = JSON.parse(saved)
    if (!Array.isArray(parsed)) return []

    return parsed
      .filter(item =>
        Number.isInteger(item.id) &&
        Number.isInteger(item.quantity) &&
        item.quantity > 0 &&
        products.some(product => product.id === item.id)
      )
      .map(item => {
        const product = products.find(p => p.id === item.id)
        return { ...product, quantity: item.quantity }
      })
  } catch (error) {
    console.error('Помилка завантаження кошика:', error)
    return []
  }
}

const cart = ref(loadCart())

// Категорії
const categories = computed(() => [
  'Усі',
  ...new Set(products.map(product => product.category))
])

// Реактивний пошук, фільтрація, сортування
const filteredProducts = computed(() => {
  const query = search.value.trim().toLocaleLowerCase('uk-UA')

  const result = products.filter(product => {
    const matchesSearch = product.name
      .toLocaleLowerCase('uk-UA')
      .includes(query)

    const matchesCategory =
      selectedCategory.value === 'Усі' ||
      product.category === selectedCategory.value

    return matchesSearch && matchesCategory
  })

  if (sortOrder.value === 'price-asc') {
    return result.sort((a, b) => a.price - b.price)
  }

  if (sortOrder.value === 'price-desc') {
    return result.sort((a, b) => b.price - a.price)
  }

  return result
})

// Кількість одиниць товарів у кошику
const cartCount = computed(() =>
  cart.value.reduce((sum, item) => sum + item.quantity, 0)
)

// Загальна сума
const cartTotal = computed(() =>
  cart.value.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  )
)

// Додавання товару
function addToCart(product) {
  const existing = cart.value.find(item => item.id === product.id)

  if (existing) {
    existing.quantity++
  } else {
    cart.value.push({ ...product, quantity: 1 })
  }
}

// Збільшення кількості
function increaseQuantity(id) {
  const item = cart.value.find(item => item.id === id)
  if (item) item.quantity++
}

// Зменшення кількості
function decreaseQuantity(id) {
  const item = cart.value.find(item => item.id === id)
  if (!item) return

  if (item.quantity > 1) {
    item.quantity--
  } else {
    removeFromCart(id)
  }
}

// Видалення товару
function removeFromCart(id) {
  cart.value = cart.value.filter(item => item.id !== id)
}

function openCheckout() {
  if (cart.value.length === 0) return

  isCartOpen.value = false
  isCheckoutOpen.value = true
}

function submitOrder(order) {
  if (cart.value.length === 0) return

  // Демонстраційне замовлення без надсилання даних
  orderNumber.value = `PC-${Date.now().toString().slice(-8)}`

  console.log('Демо-замовлення:', {
    number: orderNumber.value,
    delivery: order.delivery,
    items: cart.value.map(item => ({
      id: item.id,
      quantity: item.quantity
    })),
    total: cartTotal.value
  })

  cart.value = []
  isCheckoutOpen.value = false
  isOrderSuccess.value = true
}

// Збереження після кожної зміни
watch(
  cart,
  newCart => {
    try {
      localStorage.setItem(
        'pharmacare-cart',
        JSON.stringify(newCart)
      )
    } catch (error) {
      console.error('Помилка збереження кошика:', error)
    }
  },
  { deep: true }
)
</script>

<template>
  <div class="site">
    <div class="top-bar">
      <div class="container top-content">
        <span>💚 Турбота про здоров'я щодня</span>
        <span>Навчальний інтернет-каталог</span>
      </div>
    </div>

    <header class="header">
      <div class="container header-content">
        <a href="#" class="logo">
          <span class="logo-icon">✚</span>
          <span>Pharma<span class="logo-accent">Care</span></span>
        </a>

        <nav class="navigation" aria-label="Головне меню">
          <a href="#home">Головна</a>
          <a href="#catalog">Каталог</a>
          <a href="#about">Про нас</a>
        </nav>

        
<button
  class="cart-button"
  type="button"
  @click="isCartOpen = true"
>
  🛒 Кошик <span class="cart-count">{{ cartCount }}</span>
</button>

      </div>
    </header>

    <main>
      <section id="home" class="hero">
        <div class="container hero-content">
          <div class="hero-text">
            <span class="eyebrow">ВАШЕ ЗДОРОВ'Я — НАШ ПРІОРИТЕТ</span>

            <h1>Піклуємося про вас і ваших близьких</h1>

            <p>
              Відкрийте для себе зручний каталог товарів для
              здоров'я, краси та щоденного догляду.
            </p>

            <a href="#catalog" class="primary-button">
              Переглянути каталог →
            </a>

            <div class="hero-benefits">
              <span>✓ Зручний вибір</span>
              <span>✓ Турбота щодня</span>
            </div>
          </div>

          <div class="hero-art" aria-hidden="true">
            <div class="art-circle">
              <span class="art-cross">✚</span>
              <span class="art-leaf">🌿</span>
            </div>
            <div class="floating-card">
              💚 Здоров'я починається з турботи
            </div>
          </div>
        </div>
      </section>

      <section id="about" class="benefits-section">
        <div class="container benefits-grid">
          <div class="benefit">
            <span class="benefit-icon">♡</span>
            <div>
              <h3>Турбота про вас</h3>
              <p>Товари для щоденного догляду</p>
            </div>
          </div>

          <div class="benefit">
            <span class="benefit-icon">✦</span>
            <div>
              <h3>Зручний каталог</h3>
              <p>Усе необхідне в одному місці</p>
            </div>
          </div>

          <div class="benefit">
            <span class="benefit-icon">✓</span>
            <div>
              <h3>Просте замовлення</h3>
              <p>Швидке додавання товарів у кошик</p>
            </div>
          </div>
        </div>
      </section>

      <section id="catalog" class="catalog-section">
        <div class="container">
          <div class="section-heading">
            <div>
              <span class="eyebrow">PHARMACARE SHOP</span>
              <h2>Каталог товарів</h2>
              <p>Знайдіть те, що потрібно саме вам</p>
            </div>
          </div>

          
<div class="catalog-controls">
  <div class="control-field">
    <label for="product-search">Пошук товарів</label>
    <input
      id="product-search"
      v-model="search"
      type="search"
      placeholder="Наприклад, вітамін C..."
    />
  </div>

  <div class="control-field">
    <label for="category-filter">Категорія</label>
    <select id="category-filter" v-model="selectedCategory">
      <option
        v-for="category in categories"
        :key="category"
        :value="category"
      >
        {{ category }}
      </option>
    </select>
  </div>

  <div class="control-field">
    <label for="sort-order">Сортування</label>
    <select id="sort-order" v-model="sortOrder">
      <option value="default">За замовчуванням</option>
      <option value="price-asc">Від дешевих до дорогих</option>
      <option value="price-desc">Від дорогих до дешевих</option>
    </select>
  </div>
</div>

<p class="results-count">
  Знайдено товарів: {{ filteredProducts.length }}
</p>

<div v-if="filteredProducts.length" class="products-grid">
  <ProductCard
    v-for="product in filteredProducts"
    :key="product.id"
    :product="product"
    @add-to-cart="addToCart"
  />
</div>

<div v-else class="catalog-placeholder">
  <span>🔎</span>
  <h3>Нічого не знайдено</h3>
  <p>Спробуйте змінити пошуковий запит або категорію.</p>
</div>

        </div>
      </section>
    </main>

<div
  v-if="isCartOpen"
  class="cart-overlay"
  @click.self="isCartOpen = false"
>
  <section
    class="cart-panel"
    role="dialog"
    aria-modal="true"
    aria-labelledby="cart-title"
  >
    <div class="cart-header">
      <div>
        <span class="eyebrow">ВАШІ ПОКУПКИ</span>
        <h2 id="cart-title">Кошик</h2>
      </div>

      <button
        type="button"
        class="close-cart"
        aria-label="Закрити кошик"
        @click="isCartOpen = false"
      >
        ✕
      </button>
    </div>

    <div v-if="cart.length === 0" class="empty-cart">
      <span>🛒</span>
      <h3>Ваш кошик порожній</h3>
      <p>Додайте товари з каталогу, щоб почати покупки.</p>

      <button
        type="button"
        class="continue-shopping"
        @click="isCartOpen = false"
      >
        Перейти до покупок
      </button>
    </div>

    <template v-else>
      <div class="cart-items">
        <CartItem
          v-for="item in cart"
          :key="item.id"
          :item="item"
          @increase="increaseQuantity"
          @decrease="decreaseQuantity"
          @remove="removeFromCart"
        />
      </div>

      <div class="cart-summary">
        <div class="summary-row">
          <span>Кількість товарів:</span>
          <strong>{{ cartCount }}</strong>
        </div>

        <div class="summary-row total-row">
          <span>Разом:</span>
          <strong>{{ cartTotal.toLocaleString('uk-UA') }} ₴</strong>
        </div>

        
<button
  type="button"
  class="checkout-button"
  @click="openCheckout"
>
  Оформити замовлення →
</button>

      </div>
    </template>
  </section>
</div>

<!-- Форма оформлення замовлення -->
<div
  v-if="isCheckoutOpen"
  class="cart-overlay"
>
  <section
    class="cart-panel"
    role="dialog"
    aria-modal="true"
    aria-label="Оформлення замовлення"
  >
    <CheckoutForm
      :total="cartTotal"
      :count="cartCount"
      @back="
        isCheckoutOpen = false;
        isCartOpen = true
      "
      @submit-order="submitOrder"
    />
  </section>
</div>

<!-- Повідомлення про успішне демо-замовлення -->
<div
  v-if="isOrderSuccess"
  class="cart-overlay"
>
  <section
    class="cart-panel success-panel"
    role="dialog"
    aria-modal="true"
    aria-labelledby="success-title"
  >
    <div class="success-content">
      <span class="success-icon">✓</span>

      <h2 id="success-title">Замовлення оформлено!</h2>

      <p>
        Дякуємо за вибір PharmaCare.
        Ваше демонстраційне замовлення створено.
      </p>

      <div class="order-number">
        Номер замовлення:
        <strong>{{ orderNumber }}</strong>
      </div>

      <p class="success-disclaimer">
        Це навчальний сайт. Замовлення не передано
        до аптеки, доставка не здійснюватиметься.
      </p>

      <button
        type="button"
        class="checkout-button"
        @click="isOrderSuccess = false"
      >
        Повернутися до магазину
      </button>
    </div>
  </section>
</div>

    <footer class="footer">
      <div class="container footer-content">
        <strong>✚ PharmaCare</strong>
        <span>© 2026 PharmaCare · Навчальний проєкт на Vue.js 3</span>
      </div>
    </footer>
  </div>
</template>

<style>
@media (max-width: 560px) {
  .container {
    width: calc(100% - 28px);
  }

  .header-content {
    flex-wrap: wrap;
    justify-content: space-between;
    gap: 12px;
  }

  .navigation {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 12px;
    width: 100%;
    order: 3;
  }

  .hero-content {
    grid-template-columns: minmax(0, 1fr);
  }

  .hero-text,
  .hero-art {
    min-width: 0;
  }

  .products-grid {
    grid-template-columns: minmax(0, 1fr);
  }

  .catalog-controls {
    grid-template-columns: minmax(0, 1fr);
  }
}
* {
  box-sizing: border-box;
}

html {
  width: 100%;
  margin: 0;
  padding: 0;
}

body {
  width: 100%;
  min-width: 0;
  margin: 0;
  padding: 0;
  overflow-x: hidden;
}

#app {
  width: 100%;
  max-width: none;
  min-height: 100vh;
  margin: 0;
  padding: 0;
}

.site {
  width: 100%;
  min-height: 100vh;
}

.container {
  width: min(1200px, calc(100% - 40px));
  margin-inline: auto;
}
:root {
  font-family: 'Segoe UI', Arial, sans-serif;
  color: #203b37;
  background: #f7faf8;
  font-synthesis: none;
}

* {
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
}

body {
  margin: 0;
  min-width: 320px;
}

button,
input {
  font: inherit;
}

button,
a {
  -webkit-tap-highlight-color: transparent;
}

a {
  text-decoration: none;
  color: inherit;
}

.container {
  width: min(1180px, calc(100% - 36px));
  margin-inline: auto;
}

.top-bar {
  background: #164c43;
  color: #e7f8f1;
  padding: 10px 0;
  font-size: 12px;
}

.top-content,
.header-content,
.footer-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
}

.header {
  background: white;
  border-bottom: 1px solid #e5eee9;
}

.header-content {
  min-height: 86px;
}

.logo {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 25px;
  font-weight: 800;
  letter-spacing: -1px;
}

.logo-icon {
  display: grid;
  place-items: center;
  width: 43px;
  height: 43px;
  border-radius: 13px;
  background: #168b6c;
  color: white;
  font-size: 27px;
}

.logo-accent {
  color: #168b6c;
}

.navigation {
  display: flex;
  align-items: center;
  gap: 32px;
  font-size: 14px;
  font-weight: 600;
}

.navigation a:hover {
  color: #168b6c;
}

.cart-button {
  border: 0;
  border-radius: 12px;
  background: #e5f5ed;
  color: #176c54;
  padding: 13px 17px;
  font-weight: 700;
}

.cart-count {
  display: inline-grid;
  place-items: center;
  min-width: 24px;
  height: 24px;
  margin-left: 5px;
  padding: 0 5px;
  border-radius: 50%;
  background: #168b6c;
  color: white;
  font-size: 12px;
}

.hero {
  overflow: hidden;
  background: linear-gradient(120deg, #e8f7ee, #f5fbf7 58%, #d9f2e6);
}

.hero-content {
  display: grid;
  grid-template-columns: 1.1fr 0.9fr;
  align-items: center;
  gap: 45px;
  min-height: 475px;
  padding-block: 60px;
}

.eyebrow {
  display: inline-block;
  color: #178866;
  font-size: 12px;
  font-weight: 800;
  letter-spacing: 2px;
}

.hero h1 {
  max-width: 600px;
  margin: 20px 0;
  font-size: clamp(35px, 4.5vw, 60px);
  line-height: 1.12;
  letter-spacing: -2px;
}

.hero p {
  max-width: 490px;
  color: #668078;
  line-height: 1.8;
  font-size: 16px;
}

.primary-button {
  display: inline-block;
  margin-top: 22px;
  padding: 17px 25px;
  background: #168b6c;
  color: white;
  border-radius: 13px;
  font-weight: 700;
  box-shadow: 0 10px 24px #168b6c30;
}

.primary-button:hover {
  background: #106e55;
}

.hero-benefits {
  display: flex;
  flex-wrap: wrap;
  gap: 22px;
  margin-top: 30px;
  color: #487568;
  font-size: 13px;
  font-weight: 600;
}

.hero-art {
  position: relative;
  display: grid;
  place-items: center;
  min-height: 340px;
}

.art-circle {
  position: relative;
  display: grid;
  place-items: center;
  width: min(330px, 90%);
  aspect-ratio: 1;
  border: 25px solid #ffffff90;
  border-radius: 50%;
  background: linear-gradient(145deg, #c1ecd5, #88cdb0);
  box-shadow: 0 25px 60px #1e79542b;
}

.art-cross {
  font-size: 140px;
  line-height: 1;
  color: white;
  text-shadow: 0 10px 20px #348b6730;
}

.art-leaf {
  position: absolute;
  right: 12px;
  bottom: 25px;
  font-size: 80px;
}

.floating-card {
  position: absolute;
  bottom: 15px;
  left: 0;
  max-width: 230px;
  padding: 17px;
  border-radius: 15px;
  background: white;
  box-shadow: 0 10px 30px #315a4220;
  font-size: 13px;
  font-weight: 700;
}

.benefits-section {
  background: white;
  border-bottom: 1px solid #e8eeeb;
}

.benefits-grid {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 25px;
  padding-block: 36px;
}

.benefit {
  display: flex;
  align-items: center;
  gap: 15px;
}

.benefit-icon {
  display: grid;
  place-items: center;
  flex-shrink: 0;
  width: 52px;
  height: 52px;
  border-radius: 14px;
  background: #e7f6ee;
  color: #168b6c;
  font-size: 28px;
}

.benefit h3 {
  margin: 0 0 5px;
  font-size: 15px;
}

.benefit p {
  margin: 0;
  color: #84958f;
  font-size: 12px;
}

.catalog-section {
  padding: 80px 0 100px;
}

.section-heading h2 {
  margin: 12px 0;
  font-size: clamp(30px, 4vw, 42px);
  letter-spacing: -1px;
}

.section-heading p {
  color: #84958f;
}

.search-panel {
  display: grid;
  gap: 9px;
  max-width: 520px;
  margin-top: 35px;
}

.search-panel label {
  font-size: 13px;
  font-weight: 700;
}

.search-panel input {
  width: 100%;
  padding: 17px 20px;
  border: 1px solid #dce8e2;
  border-radius: 14px;
  background: white;
  outline-color: #168b6c;
}

.catalog-placeholder {
  display: grid;
  justify-items: center;
  gap: 12px;
  margin-top: 35px;
  padding: 65px 25px;
  border: 2px dashed #cfe7d9;
  border-radius: 22px;
  background: white;
  text-align: center;
}

.catalog-placeholder span {
  font-size: 55px;
}

.catalog-placeholder h3 {
  margin: 0;
}

.catalog-placeholder p {
  max-width: 420px;
  margin: 0;
  color: #84958f;
  line-height: 1.7;
}

.footer {
  padding: 30px 0;
  background: #164c43;
  color: white;
}

.footer strong {
  font-size: 20px;
}

.footer-content span {
  font-size: 12px;
  color: #b9d6ca;
}

@media (max-width: 800px) {
  .hero-content {
    grid-template-columns: 1fr;
    padding-block: 50px;
  }

  .hero-art {
    min-height: 290px;
  }

  .benefits-grid {
    grid-template-columns: 1fr;
  }

  .navigation {
    gap: 14px;
  }
}

@media (max-width: 560px) {
  .top-content span:last-child {
    display: none;
  }

  .header-content {
    flex-wrap: wrap;
    padding-block: 16px;
  }

  .logo {
    font-size: 22px;
  }

  .navigation {
    order: 3;
    justify-content: space-between;
    width: 100%;
  }

  .cart-button {
    padding: 10px 12px;
  }

  .hero h1 {
    letter-spacing: -1px;
  }

  .hero-art {
    min-height: 270px;
  }

  .art-circle {
    width: 250px;
  }

  .art-cross {
    font-size: 105px;
  }

  .art-leaf {
    font-size: 55px;
  }

  .floating-card {
    left: 0;
    bottom: 0;
  }

  .catalog-section {
    padding-block: 55px;
  }

  .footer-content {
    flex-direction: column;
    text-align: center;
  }
}

@media (prefers-reduced-motion: reduce) {
  html {
    scroll-behavior: auto;
  }
}

.catalog-controls {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr;
  gap: 16px;
  margin-top: 35px;
}

.control-field {
  display: grid;
  min-width: 0;
  gap: 9px;
}

.control-field label {
  font-size: 13px;
  font-weight: 700;
}

.control-field input,
.control-field select {
  width: 100%;
  min-width: 0;
  padding: 16px;
  border: 1px solid #dce8e2;
  border-radius: 13px;
  background: white;
  color: #203b37;
  font: inherit;
  outline-color: #168b6c;
}

.results-count {
  margin: 28px 0 18px;
  color: #668078;
  font-size: 14px;
  font-weight: 600;
}

.products-grid {
  display: grid;
  grid-template-columns:
    repeat(auto-fit, minmax(min(250px, 100%), 1fr));
  gap: 22px;
}

@media (max-width: 800px) {
  .catalog-controls {
    grid-template-columns: 1fr 1fr;
  }

  .catalog-controls .control-field:first-child {
    grid-column: 1 / -1;
  }
}

@media (max-width: 560px) {
  .catalog-controls {
    grid-template-columns: 1fr;
  }

  .catalog-controls .control-field:first-child {
    grid-column: auto;
  }
}

.cart-overlay {
  position: fixed;
  inset: 0;
  z-index: 1000;
  display: flex;
  justify-content: flex-end;
  background: rgba(13, 38, 31, 0.55);
}

.cart-panel {
  display: flex;
  flex-direction: column;
  width: min(520px, 100%);
  height: 100%;
  max-height: 100dvh;
  overflow-y: auto;
  padding: 28px;
  background: #fff;
  box-shadow: -15px 0 50px #00000020;
}

.cart-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  padding-bottom: 20px;
  border-bottom: 1px solid #e5eee9;
}

.cart-header h2 {
  margin: 8px 0 0;
  font-size: 30px;
}

.close-cart {
  display: grid;
  place-items: center;
  width: 42px;
  height: 42px;
  flex-shrink: 0;
  border: 0;
  border-radius: 12px;
  background: #edf7f1;
  color: #164c43;
  font-size: 20px;
  cursor: pointer;
}

.empty-cart {
  display: grid;
  justify-items: center;
  align-content: center;
  flex: 1;
  padding: 50px 10px;
  text-align: center;
}

.empty-cart > span {
  font-size: 65px;
}

.empty-cart h3 {
  margin-bottom: 0;
  font-size: 22px;
}

.empty-cart p {
  color: #84958f;
  line-height: 1.6;
}

.continue-shopping,
.checkout-button {
  width: 100%;
  margin-top: 22px;
  padding: 16px;
  border: 0;
  border-radius: 12px;
  background: #168b6c;
  color: white;
  font-weight: 700;
  cursor: pointer;
}

.continue-shopping {
  max-width: 240px;
}

.cart-items {
  flex: 1;
}

.cart-summary {
  margin-top: auto;
  padding-top: 24px;
  border-top: 1px solid #e5eee9;
}

.summary-row {
  display: flex;
  justify-content: space-between;
  gap: 15px;
  margin-bottom: 14px;
  color: #668078;
}

.total-row {
  padding-top: 14px;
  border-top: 1px solid #e5eee9;
  color: #164c43;
  font-size: 21px;
  font-weight: 700;
}

@media (max-width: 560px) {
  .cart-panel {
    padding: 18px;
  }
}

.success-panel {
  justify-content: center;
}

.success-content {
  display: grid;
  justify-items: center;
  gap: 16px;
  text-align: center;
}

.success-icon {
  display: grid;
  place-items: center;
  width: 85px;
  height: 85px;
  border-radius: 50%;
  background: #e3f7ea;
  color: #168b6c;
  font-size: 45px;
  font-weight: 800;
}

.success-content h2 {
  margin: 10px 0 0;
  color: #164c43;
  font-size: 28px;
}

.success-content p {
  margin: 0;
  color: #668078;
  line-height: 1.7;
}

.order-number {
  width: 100%;
  padding: 20px;
  border-radius: 14px;
  background: #f0f8f3;
  color: #164c43;
}

.order-number strong {
  display: block;
  margin-top: 8px;
  font-size: 21px;
}

.success-disclaimer {
  font-size: 12px;
}

</style>
