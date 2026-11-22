
<script setup>
defineProps({
  product: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['add-to-cart'])
const imageBase = import.meta.env.BASE_URL
</script>

<template>
  <article class="product-card">
    <div class="product-image">
      <img
        :src="imageBase + product.image.replace(/^\//, '')"
        :alt="product.name"
        width="500"
        height="400"
        loading="lazy"
      />
      <span class="category-badge">{{ product.category }}</span>
    </div>

    <div class="product-info">
      <h3>{{ product.name }}</h3>
      <p>{{ product.description }}</p>

      <div class="product-bottom">
        <strong class="price">
          {{ product.price.toLocaleString('uk-UA') }} ₴
        </strong>

        <button
          type="button"
          class="add-button"
          @click="emit('add-to-cart', product)"
        >
          + У кошик
        </button>
      </div>
    </div>
  </article>
</template>

<style scoped>
.product-card {
  display: flex;
  flex-direction: column;
  min-width: 0;
  overflow: hidden;
  border: 1px solid #e5eee9;
  border-radius: 20px;
  background: white;
  transition: transform 0.2s, box-shadow 0.2s;
}

.product-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 15px 35px #174d3520;
}

.product-image {
  position: relative;
  background: #f2f8f4;
}

.product-image img {
  display: block;
  width: 100%;
  height: auto;
  aspect-ratio: 5 / 4;
  object-fit: contain;
  padding: 18px;
  background: #ffffff;
}

.category-badge {
  position: absolute;
  top: 12px;
  left: 12px;
  padding: 7px 11px;
  border-radius: 20px;
  background: white;
  color: #176b53;
  font-size: 11px;
  font-weight: 700;
}

.product-info {
  display: flex;
  flex: 1;
  flex-direction: column;
  padding: 20px;
}

.product-info h3 {
  margin: 0 0 9px;
  font-size: 17px;
}

.product-info p {
  flex: 1;
  margin: 0 0 20px;
  color: #84958f;
  font-size: 13px;
  line-height: 1.6;
}

.product-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 12px;
}

.price {
  color: #164c43;
  font-size: 20px;
}

.add-button {
  border: 0;
  border-radius: 11px;
  padding: 12px 15px;
  background: #168b6c;
  color: white;
  font-size: 12px;
  font-weight: 700;
  cursor: pointer;
}

.add-button:hover {
  background: #106e55;
}

.add-button:focus-visible {
  outline: 3px solid #f2a65a;
  outline-offset: 3px;
}
</style>
