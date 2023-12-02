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
      <BackToHomeButton />
    </div>
  </div>
</nav>

  <div class="container mt-5">
    <div v-if="editedProduct.image && !imagePreview" class="text-center mb-4">
      <div class=" circulo-img border border-primary d-inline-block"
        style="width: 150px height: 150px overflow: hidden">
        <img :src="imageUrl" class="w-100 h-100" alt="Imagem do Produto">
      </div>
    </div>
    <h2 class="text-center">{{ editing ? 'Editar Produto' : 'Adicionar Produto' }}</h2>
    <form @submit.prevent="saveProduct">

      <div v-if="imagePreview">
        <div class=" circulo-img border border-primary d-inline-block"
          style="width: 150px height: 150px overflow: hidden">
          <img :src="imagePreview" class="w-100 h-100" alt="Pré-visualização da Imagem">
        </div>
        <p>Pré-visualização da Imagem</p>
      </div>

      <div class="form-group">
        <label for="name">Nome:</label>
        <input type="text" class="form-control" id="name" v-model="editedProduct.name" required>
      </div>
      <div class="form-group">
        <label for="description">Descrição:</label>
        <textarea class="form-control" id="description" v-model="editedProduct.description" required></textarea>
      </div>
      <div class="form-group">
          <label for="price">Preço:</label>
          <input type="number" step=".01" class="form-control" id="price" v-model="editedProduct.price" required>
        </div>
      <div class="form-group">
        <label for="category">Categoria:</label>
        <select class="form-control" id="category" v-model="selectedCategoryId" required>
          <option v-for="category in categories" :value="category.id" :key="category.id">{{ category.name }}</option>
        </select>
      </div>
      <div class="form-group">
        <label for="image">Imagem do Produto:</label>
        <input type="file" class="form-control-file" id="image" @change="onImageChange" accept="image/*">
      </div>
      <button type="submit" class="btn btn-primary container">{{ editing ? 'Salvar' : 'Adicionar' }}</button>
    </form>
  </div>
</template>

<script>
import api from '@/api'

export default {
  props: {
    editing: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    imageUrl() {
      const baseUrl = 'http://localhost:8080'
      return `${baseUrl}${this.editedProduct.image}`
    },
    selectedCategoryId: {
      get() {
        return this.editedProduct.category.id
      },
      set(value) {
        const selectedCategory = this.categories.find(category => category.id === value)
        this.editedProduct.category = selectedCategory
      }
    }
  },
  data() {
    return {
      imagePreview: null,
      editedProduct: {
        name: '',
        description: '',
        price: 0,
        category: {},
        image: null,
      },
      categories: [],
    }
  },
  mounted() {
    if (this.$route.params.id) {
      this.fetchProduct(this.$route.params.id)
    }
    this.loadCategories()
  },
  methods: {
    async fetchProduct(productId) {
      try {
        const response = await api.get(`/products/${productId}`)
        this.editedProduct = response.data
      } catch (error) {
        console.error(error)
      }
    },
    async loadCategories() {
      try {
        const response = await api.get('/categories')
        this.categories = response.data
      } catch (error) {
        console.error(error)
      }
    },
    onImageChange(event) {
      const file = event.target.files[0]
      if (file) {
        // Crie uma URL temporária para pré-visualização da imagem
        this.imagePreview = URL.createObjectURL(file)
        // Atualize o campo de imagem no objeto editedProduct com o arquivo
        this.editedProduct.image = file
      }
    },
    async saveProduct() {
      try {
        const formData = new FormData()
        formData.append('name', this.editedProduct.name)
        formData.append('description', this.editedProduct.description)
        formData.append('price', this.editedProduct.price)
        formData.append('category', this.editedProduct.category.id)
        formData.append('image', this.editedProduct.image)
        this.imagePreview = null

        if (this.$route.params.id) {
          await api.patch(`products/${this.$route.params.id}`, formData)
        } else {
          await api.post('products', formData)
        }
        this.$router.push('/products')
      } catch (error) {
        console.error(error)
      }
    },
  },
}
</script>

<style scoped>
/* Estilo para o círculo da imagem */
.rounded-circle img {

  width: 10px;
  height: 10px;

  object-fit: cover
  
}

.circulo-img {
    width: 200px;
    height: 200px;
    border-radius: 50%;
    overflow: hidden;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.circulo-img img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}
</style>
