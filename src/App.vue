<template>
  <div class="project-builder">
    <h2>Состав проекта</h2>

    <div class="add-service">
      <select v-model="selectedServiceId">
        <option value="" disabled>Выберите услугу</option>
        <option v-for="service in availableServices" :key="service.id" :value="service.id">
          {{ service.name }} — {{ service.price.toLocaleString() }} ₽
        </option>
      </select>

      <input v-model.number="quantity" type="number" min="1" max="10" placeholder="Кол-во" />

      <button @click="addService" :disabled="!selectedServiceId">Добавить</button>
    </div>

    <table v-if="selectedServices.length > 0" class="services-table">
      <thead>
        <tr>
          <th>Услуга</th>
          <th>Цена за ед.</th>
          <th>Кол-во</th>
          <th>Сумма</th>
          <th>Действие</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in selectedServices" :key="item.service.id">
          <td>{{ item.service.name }}</td>
          <td>{{ item.service.price.toLocaleString() }} ₽</td>
          <td>{{ item.quantity }}</td>
          <td>{{ (item.service.price * item.quantity).toLocaleString() }} ₽</td>
          <td>
            <button @click="removeService(item)">Удалить</button>
          </td>
        </tr>
      </tbody>
      <tfoot>
        <tr>
          <td colspan="3"><strong>Итого:</strong></td>
          <td colspan="2"><strong>{{ totalPrice.toLocaleString() }} ₽</strong></td>
        </tr>
      </tfoot>
    </table>

    <p v-else class="empty">Пока не добавлено ни одной услуги</p>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'

interface Service {
  id: number
  name: string
  price: number
}

interface ServiceItem {
  service: Service
  quantity: number
}

const availableServices: Service[] = [
  { id: 1, name: 'Разработка лендинга', price: 50000 },
  { id: 2, name: 'Интернет-магазин', price: 150000 },
  { id: 3, name: 'Корпоративный портал', price: 300000 },
  { id: 4, name: 'SEO-оптимизация', price: 20000 },
]

const selectedServices = ref<ServiceItem[]>([])
const selectedServiceId = ref<number | string>('')
let quantity: number = 1 //<- нет нужды делать реактивной, переменная общая для всего приложения и мы ее явно прибавляем

const totalPrice = computed(() => {
  return selectedServices.value.reduce((sum, item) => sum + item.service.price * item.quantity, 0)
})

function addService() {
  const service = availableServices.find(s => s.id === Number(selectedServiceId.value))
  if (!service) return

  const existing = selectedServices.value.find(item => item.service.id === service.id)
  if (existing) {
    existing.quantity += quantity
  } else {
    selectedServices.value.push({ service, quantity })
  }
}

function removeService(item: ServiceItem) {
  const index = selectedServices.value.indexOf(item)
  if (index > -1) {
    selectedServices.value.splice(index, 1)
  }
}
</script>

<style scoped>
.project-builder {
  max-width: 700px;
  margin: 0 auto;
  font-family: sans-serif;
}
.add-service {
  display: flex;
  gap: 12px;
  margin-bottom: 24px;
  align-items: center;
}
.add-service select,
.add-service input {
  padding: 8px 12px;
  font-size: 14px;
}
.add-service input {
  width: 80px;
}
.services-table {
  width: 100%;
  border-collapse: collapse;
}
.services-table th,
.services-table td {
  border: 1px solid #ddd;
  padding: 10px;
  text-align: left;
}
.empty {
  color: #888;
  font-style: italic;
}
</style>
