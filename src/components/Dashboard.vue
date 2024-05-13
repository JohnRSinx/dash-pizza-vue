<template>
  <div className="mt-4 mx-4">
    <!-- Order summary -->
    <div className="flex justify-around">
      <div className="flex flex-col gap-2 items-center">
        <span className="bg-green-800 py-3 px-5 rounded-full">{{ completed }}</span>
        <p className="text-sm text-zinc-400">Concluídos</p>
      </div>
      <div className="flex flex-col gap-2 items-center">
        <span className="bg-yellow-800 py-3 px-5 rounded-full">{{ inProgress }}</span>
        <p className="text-sm text-zinc-400">Em andamento</p>
      </div>
      <div className="flex flex-col gap-2 items-center">
        <span className="bg-red-800 py-3 px-5 rounded-full">{{ cancelled }}</span>
        <p className="text-sm text-zinc-400">Cancelados</p>
      </div>
    </div>

    <!-- Orders list -->
    <div className="flex justify-around pt-8">
      <div>
        <h3 className="text-zinc-200">Todos os pedidos</h3>
        <ul className="flex flex-col gap-2">
          <li className="flex justify-between items-center gap-2" v-for="order in orders" :key="order.id">
            <span className="text-zinc-300">Pedido {{ order.name }} - R$ {{ order.price.toFixed(2) }}</span>
            <!-- Bolinha colorida correspondente ao status -->
            <span :class="getStatusColorClass(order.status)"></span>
            <select className="bg-zinc-900  rounded-sm border border-zinc-500" v-model="order.status" @change="updateOrderStatus(order)">
              <option value="concluido">Concluído</option>
              <option value="em andamento">Em andamento</option>
              <option value="cancelado">Cancelado</option>
            </select>
            <button className="text-zinc-300 bg-red-900 py-1 px-2 rounded-sm hover:bg-red-500" @click="deleteOrder(order.id)">Excluir</button>
          </li>
        </ul>
      </div>

      <!-- Form to add new orders -->
      <form @submit.prevent="addOrder">
        <h3 className="p-1">Adicionar novo pedido</h3>
        <div className="flex flex-col gap-2">
          <input className="bg-zinc-900 p-1 rounded-sm border border-zinc-700" type="text" id="name" placeholder="Nome" v-model="newOrder.name" required />
          
          <label for="price">Preço:</label>
          <input className="bg-zinc-900 p-1 rounded-sm border border-zinc-700"  type="number" id="price" v-model="newOrder.price" required />

          <label for="status">Status:</label>
          <select className="bg-zinc-900 p-1 rounded-sm border border-zinc-700"  id="status" v-model="newOrder.status" required>
            <option value="concluido">Concluído</option>
            <option value="em andamento">Em andamento</option>
            <option value="cancelado">Cancelado</option>
          </select>
          
          <button className="bg-zinc-900 p-1 rounded-sm border border-zinc-500"  type="submit">Adicionar</button>
        </div>
      </form>
      <!-- End of form -->
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      completed: 0,
      inProgress: 0,
      cancelled: 0,
      orders: [],
      newOrder: {
        name: '',
        price: 0,
        status: 'concluido',
      },
    };
  },
  methods: {
    async fetchOrders() {
      const response = await axios.get('http://127.0.0.1:8000/api/orders');
      const ordersJson = await response.data;
      this.orders = ordersJson;
      this.completed = ordersJson.filter(
        (order) => order.status === 'concluido'
      ).length;
      this.inProgress = ordersJson.filter(
        (order) => order.status === 'em andamento'
      ).length;
      this.cancelled = ordersJson.filter(
        (order) => order.status === 'cancelado'
      ).length;
    },
    async addOrder() {
      const response = await axios.post(
        'http://127.0.0.1:8000/api/orders',
        this.newOrder
      );
      this.newOrder = { name: '', price: 0, status: 'concluido' };
      this.fetchOrders();
    },
    async deleteOrder(id) {
      await axios.delete(`http://127.0.0.1:8000/api/orders/${id}`);
      this.fetchOrders();
    },
    async updateOrderStatus(order) {
      await axios.put(`http://127.0.0.1:8000/api/orders/${order.id}`, order);
      this.fetchOrders();
    },
    // Método para definir a classe da bolinha colorida com base no status
    getStatusColorClass(status) {
      switch (status) {
        case 'concluido':
          return 'bg-green-500 w-4 h-4 rounded-full';
        case 'em andamento':
          return 'bg-yellow-500 w-4 h-4 rounded-full';
        case 'cancelado':
          return 'bg-red-500 w-4 h-4 rounded-full';
        default:
          return '';
      }
    },
  },
  mounted() {
    this.fetchOrders();
  },
};
</script>
