<template>
  <v-card>
    <h2>Детали заявки</h2>
    <div v-if="order">
      <p><strong>ID:</strong> {{ order.id }}</p>
      <p><strong>Номер:</strong> {{ order.num }}</p>
      <p><strong>Дата добавления:</strong> {{ formatDate(order.dadd) }}</p>
      <p><strong>Профиль ID:</strong> {{ order.profile_id }}</p>
      <p><strong>Статус:</strong> {{ order.state }}</p>
      <p><strong>Последнее обновление:</strong> {{ formatDate(order.lastUpd) }}</p>
      <p><strong>Продукт:</strong> {{ order.stg.join(', ') }}</p>
      <p><strong>ИНН:</strong> {{ order.inn }}</p>
      <p><strong>Телефон:</strong> {{ order.person_phone }}</p>
      <p><strong>Тип компании:</strong> {{ order.company_type }}</p>
      <p><strong>Email:</strong> {{ order.person_email }}</p>
      <p><strong>Источник CRM:</strong> {{ order.extra.crm_source_type }}</p>
      <div v-if="order.extra && order.extra.partner">
        <h3>Партнер</h3>
        <p><strong>Тип:</strong> {{ order.extra.partner.type }}</p>
        <p><strong>User ID:</strong> {{ order.extra.partner.user_id }}</p>
        <p><strong>Имя клиента:</strong> {{ order.extra.partner.client_name }}</p>
        <p><strong>ИНН:</strong> {{ order.extra.partner.inn }}</p>
      </div>
    </div>
    <div v-else>
      <div class="no-data-message">
        <p v-if="isNewOrder">Извините, данных по новым заявкам на сервере нет</p>
        <p v-else>Загрузка данных...</p>
      </div>
    </div>
  </v-card>
</template>

<script>
import moment from 'moment';
import axios from 'axios';

export default {
  data() {
    return {
      order: null
    };
  },
  mounted() {
    const id = this.$route.path.split('/').pop();
    this.fetchOrderDetails(id);
  },
  methods: {
    fetchOrderDetails(id) {
      if (parseInt(id) >= 6) {
        this.order = null;
        return;
      }
      axios
        .get(`https://my-json-server.typicode.com/plushevy/demo/orders/${id}`)
        .then(response => {
          this.order = response.data;
        })
        .catch(error => {
          console.error('Ошибка при загрузке данных заявки:', error);
        });
    },
    formatDate(date) {
      return moment(date).format('DD.MM.YYYY HH:mm');
    },
  },
  computed: {
    isNewOrder() {
      return parseInt(this.$route.params.id) >= 6;
    }
  }
};
</script>

<style scoped>
.container {
  padding: 20px;
}

h2 {
  margin-bottom: 20px;
  text-align: center;
}

.no-data-message {
  text-align: center;
  padding: 20px;
  margin: 20px 0;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.no-data-message p {
  margin: 0;
}
</style>