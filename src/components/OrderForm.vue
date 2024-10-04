<template>
    <div class="order-form">
      <h2>Оформление заказа</h2>
      <form @submit.prevent="submitOrder">
        <div class="mb-3">
          <label for="name" class="form-label">Имя:</label>
          <input type="text" id="name" class="form-control" v-model="name" required>
        </div>
        <div class="mb-3">
          <label for="email" class="form-label">Email:</label>
          <input type="email" id="email" class="form-control" v-model="email" required>
        </div>
        <div class="mb-3">
          <label for="address" class="form-label">Адрес доставки:</label>
          <input type="text" id="address" class="form-control" v-model="address" required>
        </div>
        <button type="submit" class="btn btn-primary">Подтвердить заказ</button>
      </form>

      <div v-if="orderSubmitted" class="mt-3 alert alert-success">
        Заказ успешно оформлен!
      </div>
    </div>
  </template>

  <script>
  export default {
    data() {
      return {
        name: '',
        email: '',
        address: '',
        orderSubmitted: false
      };
    },
    methods: {
      submitOrder() {
        this.orderSubmitted = true;
        console.log('Заказ оформлен:', {
          name: this.name,
          email: this.email,
          address: this.address
        });

      }
    }
  }
  </script>

  <style scoped>
  .order-form {
    max-width: 500px;
    margin: auto;
  }
  </style>
