
<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  total: {
    type: Number,
    required: true
  },
  count: {
    type: Number,
    required: true
  }
})

const emit = defineEmits(['submit-order', 'back'])

const firstName = ref('')
const lastName = ref('')
const phone = ref('')
const delivery = ref('nova-poshta')
const city = ref('')
const branch = ref('')
const comment = ref('')

const needsAddress = computed(
  () => delivery.value === 'nova-poshta'
)

function submitOrder() {
  const order = {
    firstName: firstName.value.trim(),
    lastName: lastName.value.trim(),
    phone: phone.value.trim(),
    delivery: delivery.value,
    city: needsAddress.value ? city.value.trim() : '',
    branch: needsAddress.value ? branch.value.trim() : '',
    comment: comment.value.trim()
  }

  emit('submit-order', order)
}
</script>

<template>
  <div class="checkout">
    <button
      type="button"
      class="back-button"
      @click="emit('back')"
    >
      ← Повернутися до кошика
    </button>

    <div class="checkout-heading">
      <span class="eyebrow">PHARMACARE</span>
      <h2>Оформлення замовлення</h2>
      <p>Заповніть інформацію для оформлення покупки</p>
    </div>

    <form class="checkout-form" @submit.prevent="submitOrder">
      <div class="form-section">
        <h3>01. Контактні дані</h3>

        <div class="form-grid">
          <div class="form-field">
            <label for="first-name">Ім'я *</label>
            <input
              id="first-name"
              v-model="firstName"
              type="text"
              autocomplete="given-name"
              placeholder="Ваше ім'я"
              required
            />
          </div>

          <div class="form-field">
            <label for="last-name">Прізвище *</label>
            <input
              id="last-name"
              v-model="lastName"
              type="text"
              autocomplete="family-name"
              placeholder="Ваше прізвище"
              required
            />
          </div>
        </div>

        <div class="form-field">
          <label for="phone">Номер телефону *</label>
          <input
            id="phone"
            v-model="phone"
            type="tel"
            autocomplete="tel"
            placeholder="+380XXXXXXXXX"
            pattern="\+380[0-9]{9}"
            title="Введіть номер у форматі +380XXXXXXXXX"
            required
          />
        </div>
      </div>

      <div class="form-section">
        <h3>02. Спосіб доставки</h3>

        <div class="delivery-options">
          <label class="delivery-option">
            <input
              v-model="delivery"
              type="radio"
              value="nova-poshta"
            />
            <span>📦 Нова пошта</span>
          </label>

          <label class="delivery-option">
            <input
              v-model="delivery"
              type="radio"
              value="pickup"
            />
            <span>🏪 Самовивіз</span>
          </label>
        </div>

        <div v-if="needsAddress" class="form-grid">
          <div class="form-field">
            <label for="city">Місто *</label>
            <input
              id="city"
              v-model="city"
              type="text"
              placeholder="Наприклад, Київ"
              required
            />
          </div>

          <div class="form-field">
            <label for="branch">Відділення *</label>
            <input
              id="branch"
              v-model="branch"
              type="text"
              placeholder="Наприклад, №5"
              required
            />
          </div>
        </div>

        <p v-else class="pickup-note">
          Самовивіз — демонстраційний варіант.
          Адресу пункту видачі не визначено.
        </p>
      </div>

      <div class="form-section">
        <h3>03. Коментар до замовлення</h3>

        <div class="form-field">
          <label for="order-comment">Коментар (необов'язково)</label>
          <textarea
            id="order-comment"
            v-model="comment"
            rows="3"
            placeholder="Ваші побажання..."
          ></textarea>
        </div>
      </div>

      <div class="order-total">
        <div>
          <span>Товарів у замовленні</span>
          <strong>{{ count }}</strong>
        </div>

        <div class="grand-total">
          <span>Разом за товари</span>
          <strong>{{ total.toLocaleString('uk-UA') }} ₴</strong>
        </div>

        <p>Вартість доставки не включена.</p>
      </div>

      <button type="submit" class="submit-order">
        Підтвердити замовлення
      </button>

      <p class="demo-note">
        Навчальний проєкт. Реальне замовлення не створюється.
      </p>
    </form>
  </div>
</template>

<style scoped>
.checkout {
  padding: 10px 0 20px;
}

.back-button {
  padding: 0;
  border: 0;
  background: transparent;
  color: #168b6c;
  font-weight: 700;
  cursor: pointer;
}

.checkout-heading {
  margin: 28px 0;
}

.eyebrow {
  color: #168b6c;
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 2px;
}

.checkout-heading h2 {
  margin: 9px 0;
  color: #164c43;
  font-size: clamp(25px, 4vw, 32px);
}

.checkout-heading p {
  color: #84958f;
  font-size: 14px;
}

.form-section {
  margin-bottom: 26px;
}

.form-section h3 {
  margin-bottom: 18px;
  color: #164c43;
  font-size: 17px;
}

.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 14px;
}

.form-field {
  display: grid;
  gap: 8px;
  margin-bottom: 15px;
}

.form-field label {
  font-size: 13px;
  font-weight: 700;
}

.form-field input,
.form-field textarea {
  width: 100%;
  min-width: 0;
  padding: 14px;
  border: 1px solid #dce8e2;
  border-radius: 11px;
  background: #fff;
  color: #203b37;
  font: inherit;
  outline-color: #168b6c;
}

.delivery-options {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  margin-bottom: 18px;
}

.delivery-option {
  display: flex;
  align-items: center;
  gap: 9px;
  padding: 15px;
  border: 1px solid #dce8e2;
  border-radius: 12px;
  cursor: pointer;
}

.delivery-option:has(input:checked) {
  border-color: #168b6c;
  background: #effaf4;
}

.pickup-note {
  color: #668078;
  font-size: 13px;
  line-height: 1.6;
}

.order-total {
  padding: 20px;
  border-radius: 15px;
  background: #f0f8f3;
}

.order-total > div {
  display: flex;
  justify-content: space-between;
  gap: 15px;
  margin-bottom: 12px;
}

.grand-total {
  padding-top: 14px;
  border-top: 1px solid #d6e9dc;
  color: #164c43;
  font-size: 19px;
  font-weight: 700;
}

.order-total p,
.demo-note {
  color: #84958f;
  font-size: 12px;
  line-height: 1.5;
}

.submit-order {
  width: 100%;
  margin-top: 22px;
  padding: 17px;
  border: 0;
  border-radius: 12px;
  background: #168b6c;
  color: white;
  font-size: 15px;
  font-weight: 700;
  cursor: pointer;
}

.submit-order:hover {
  background: #106e55;
}

.demo-note {
  text-align: center;
}

@media (max-width: 560px) {
  .form-grid,
  .delivery-options {
    grid-template-columns: 1fr;
  }
}
</style>
