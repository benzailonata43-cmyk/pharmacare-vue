
<script setup>
defineProps({
  item: {
    type: Object,
    required: true
  }
})

const emit = defineEmits([
  'increase',
  'decrease',
  'remove'
])
</script>

<template>
  <article class="cart-item">
    <img
      :src="item.image"
      :alt="item.name"
      width="90"
      height="90"
    />

    <div class="item-details">
      <h3>{{ item.name }}</h3>
      <p>{{ item.price }} ₴ / шт.</p>

      <div class="quantity-controls">
        <button
          type="button"
          :aria-label="`Зменшити кількість ${item.name}`"
          @click="emit('decrease', item.id)"
        >
          −
        </button>

        <span>{{ item.quantity }}</span>

        <button
          type="button"
          :aria-label="`Збільшити кількість ${item.name}`"
          @click="emit('increase', item.id)"
        >
          +
        </button>
      </div>
    </div>

    <div class="item-actions">
      <strong>
        {{ (item.price * item.quantity).toLocaleString('uk-UA') }} ₴
      </strong>

      <button
        type="button"
        class="remove-button"
        :aria-label="`Видалити ${item.name} з кошика`"
        @click="emit('remove', item.id)"
      >
        ✕ Видалити
      </button>
    </div>
  </article>
</template>

<style scoped>
.cart-item {
  display: grid;
  grid-template-columns: 90px minmax(0, 1fr) auto;
  gap: 18px;
  align-items: center;
  padding: 20px 0;
  border-bottom: 1px solid #e5eee9;
}

.cart-item img {
  width: 90px;
  height: 90px;
  object-fit: cover;
  border-radius: 12px;
  background: #edf7f1;
}

.item-details {
  min-width: 0;
}

.item-details h3 {
  margin: 0 0 7px;
  font-size: 16px;
}

.item-details p {
  margin: 0 0 12px;
  color: #84958f;
  font-size: 13px;
}

.quantity-controls {
  display: inline-flex;
  align-items: center;
  gap: 15px;
  padding: 5px;
  border: 1px solid #dce8e2;
  border-radius: 10px;
}

.quantity-controls button {
  width: 30px;
  height: 30px;
  border: 0;
  border-radius: 7px;
  background: #e7f6ee;
  color: #168b6c;
  font-size: 20px;
  cursor: pointer;
}

.quantity-controls span {
  min-width: 18px;
  text-align: center;
  font-weight: 700;
}

.item-actions {
  display: grid;
  justify-items: end;
  gap: 15px;
}

.item-actions strong {
  color: #164c43;
  font-size: 17px;
  white-space: nowrap;
}

.remove-button {
  border: 0;
  background: transparent;
  color: #c24a4a;
  font-size: 12px;
  cursor: pointer;
}

@media (max-width: 560px) {
  .cart-item {
    grid-template-columns: 75px minmax(0, 1fr);
    gap: 12px;
  }

  .cart-item img {
    width: 75px;
    height: 75px;
  }

  .item-actions {
    grid-column: 2;
    justify-items: start;
    gap: 8px;
  }
}
</style>
