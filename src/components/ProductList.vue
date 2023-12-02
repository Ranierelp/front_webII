<template>
 <nav class="navbar navbar-expand-lg ">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Ecommerce API</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
      </ul>
      <LogoutButton/>
      <RouterButton @navigate="navigateTo" routePath="/product/add" buttonText="Adicionar produto" />
    </div>
  </div>
</nav>

<div class="container text-center teste mt-5">
      <h2>Lista de Produtos</h2>
  </div>
  <div class="container  text-center conteiner_table">
    <table class="table tabela_sem_fundo">
      <thead>
        <tr>
          <th>ID</th>
          <th>Nome</th>
          <th>Descrição</th>
          <th>Preço</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="product in products" :key="product.id" class="tr">
          <td><router-link :to="{ name: 'ProductDetails', params: { id: product.id } }">{{ product.id }}</router-link></td>
          <td>{{ product.name }}</td>
          <td>{{ product.description }}</td>
          <td>US$ {{ product.price.toFixed(2) }}</td>
          <td>
            <router-link :to="{ name: 'ProductEdit', params: { id: product.id },query: { editing: true } }"
              class="btn btn-primary btn-sm mr-2">Editar</router-link>
            <button @click="deleteProduct(product.id)" class="btn btn-danger btn-sm">Excluir</button>
          </td>
        </tr>
      </tbody>
    </table>
        <div class="pagination position-absolute start-50 translate-middle mt-3">
        <button @click="prevPage" :disabled="currentPage === 1" class="btn btn-secondary mr-2">Anterior</button>
        <button @click="nextPage" :disabled="currentPage * pageSize >= totalProducts" class="btn btn-secondary">Próximo</button>
      </div>
  </div>
</template>

<script>
import api from '@/api'

import RouterButton from "@/components/RouterButton.vue"
import LogoutButton from "@/components/LogoutButton.vue"

export default {
  components: {
    RouterButton,
    LogoutButton
  },
  data() {
    return {
      products: [],
      currentPage: 1,
      pageSize: 3, // Número de itens por página
      totalProducts: 0
    }
  },
  beforeMount() {
    this.fetchProducts()
  },
  methods: {
    navigateTo(routePath) {
      this.$router.push(routePath)
    },
    async fetchProducts() {
      try {
        const response = await api.get(`/products?page=${this.currentPage}&size=${this.pageSize}&sort=price&direction=desc`)
        this.products = response.data.content
        this.totalProducts = response.data.totalElements;
      } catch (error) {
        console.error(error)
      }
    },
    async deleteProduct(productId) {
      if (confirm('Tem certeza de que deseja excluir este produto?')) {
        try {
          await api.delete(`/products/${productId}`)
          this.products = this.products.filter(product => product.id !== productId)
        } catch (error) {
          console.error(error)
        }
      }
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
        this.fetchProducts();
      }
    },
    nextPage() {
      if (this.currentPage * this.pageSize < this.totalProducts) {
        this.currentPage++;
        this.fetchProducts();
      }
    }
  },
}
</script>
<style scoped>
.pagination {
  margin-top: 20px;
}

.justify-content-end {
  justify-content: flex-end;
}

.conteiner_table{
  box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
  border-radius: 15px;

}
.teste{
  background-color: rgb(0, 0, 0);
  border-radius: 10px 10px 0px 0px;
  color: rgb(255, 255, 255);
}
</style>
