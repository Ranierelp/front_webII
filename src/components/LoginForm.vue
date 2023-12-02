<template>
  <br>
  <br>
  <div class="efeito-vidro container"> 
    <h1 class="text-center">Login</h1>   
    <form @submit.prevent="login">
      <div class="form-group">
        <label for="username">UserName:</label>
        <input type="text" id="username" v-model="username" class="form-control" required>
      </div>
      <div class="form-group">
        <label for="password">Password:</label>
        <input type="password" id="password" v-model="password" class="form-control" required>
      </div>
      <button type="submit" class="btn btn-outline-success btn-lg">Entrar</button>
    </form>
  </div>

</template>

<script>
import api from '@/api'

export default {
  data() {
    return {
      username: '',
      password: ''
    };
  },
  methods: {
    async login() {
      try {
        const response = await api.post('/users/login', {
          username: this.username,
          password: this.password
        });
        // Armazena o token de autenticação no localStorage
        localStorage.setItem('token', response.data.token);
        // Redireciona o usuário para outra página após o login, se necessário
        this.$router.push('/products');
      } catch (error) {
        alert('Erro ao fazer login.', error);
        // Trate o erro de acordo com sua necessidade (exibindo uma mensagem de erro, por exemplo)
      }
    }
  }
};
</script>
<style scoped>

.form-group {
  margin-bottom: 10px;
}

button {
  
  display: flex;
  justify-content: center;
  margin: auto;
}


</style>