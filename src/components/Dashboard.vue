<template>
  <div className="mt-4 mx-4">
       <!-- Resumo de pedidos -->
    <div className="flex justify-around">
        <div className=" flex flex-col gap-2 items-center ">
          <span className="bg-green-800 py-3 px-5 rounded-full">{{ concluidos }}</span>
          <p className="text-sm text-zinc-400">Concluidos</p>
        </div>
        <div className=" flex flex-col gap-2 items-center ">
          <span className="bg-yellow-800 py-3 px-5 rounded-full">{{ emAndamento }}</span>
          <p className="text-sm text-zinc-400">Em andamento</p>
        </div>
        <div className="flex flex-col gap-2 items-center ">
          <span className="bg-red-800  py-3 px-5 rounded-full">{{ cancelados }}</span>
          <p className="text-sm text-zinc-400">Cancelados</p>
        </div>
        </div>
    <!-- Lista de pedidos -->
    <div className="p-4">
      <h3>Todos os pedidos</h3>
      <ul className="flex flex-col gap-2">
        <li className="flex" v-for="pedido in pedidos" :key="pedido.id">
        <span>Pedido {{ pedido.name }} - R$ {{ pedido.price.toFixed(2) }} -</span>
        <span> {{ pedido.status }}</span>
        <select v-model="pedido.status" @change="atualizarStatusPedido(pedido.id, pedido.status)">
          <option value="concluido">Concluído</option>
          <option value="em andamento">Em andamento</option>
          <option value="cancelado">Cancelado</option>
        </select>
        <button @click="excluirPedido(pedido.id)">Excluir</button>
        </li>
      </ul>
      
      </div>
        <!-- Formulário para adicionar novos pedidos -->
        <form @submit.prevent="adicionarPedido">
      <div className="flex flex-col gap-2">
        <input type="text" id="nome" placeholder="Nome" v-model="novoPedido.name" required />
        
        <label for="preco">Preço:</label>
        <input type="number" id="preco" v-model="novoPedido.price" required />
        <label for="status">Status:</label>
        <select id="status" v-model="novoPedido.status" required>
          <option value="concluido">Concluído</option>
          <option value="em andamento">Em andamento</option>
          <option value="cancelado">Cancelado</option>
        </select>
        <button type="submit">Adicionar</button>
      </div>
    </form>
    <!-- Fim do formulário -->
  </div>
  
</template>

<script>
import axios from 'axios';


export default {
  data() {
    return {
      concluidos: 0,
      emAndamento: 0,
      cancelados: 0,
      pedidos: [],
      novoPedido: {
        name: '',
        price: 0,
        status: 'concluido',
      },
    };
  },
  methods: {
    async buscarPedidos() {
      const response = await axios.get('http://127.0.0.1:8000/api/orders');
      const pedidosJson = await response.data;
      this.pedidos = pedidosJson;
      this.concluidos = pedidosJson.filter(
        (pedido) => pedido.status === 'concluido'
      ).length;
      this.emAndamento = pedidosJson.filter(
        (pedido) => pedido.status === 'em andamento'
      ).length;
      this.cancelados = pedidosJson.filter(
        (pedido) => pedido.status === 'cancelado'
      ).length;
    },
    async adicionarPedido() {
      const response = await axios.post(
        'http://127.0.0.1:8000/api/orders',
        this.novoPedido
      );
      this.novoPedido = { name: '', price: 0, status: 'concluido' };
      this.buscarPedidos();
    },
    async excluirPedido(id) {
      await axios.delete(`http://127.0.0.1:8000/api/orders/${id}`);
      this.buscarPedidos();
    },
  },
  mounted() {
    this.buscarPedidos();
  },
  async atualizarStatusPedido(id, novoStatus) {
    await axios.patch(`http://127.0.0.1:8000/api/orders/${id}`, { status: novoStatus });
    this.buscarPedidos();
  },
};
</script>
