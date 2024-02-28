<template>
  <div class="container">
    <form class="card" @submit.prevent="handleSubmit">
      <h2>Работа с БД</h2>

      <div class="form-control">
        <label for="name"></label>
        <input type="text" id="name" v-model="name">
      </div>

      <button class="btn primary" :disabled="name === ''">Отправить</button>
    </form>

    <app-user-list v-if="!loader" :users="users" @load="loadList" @remove="removeUser">

    </app-user-list>
  </div>
  <div class="loader" v-if="loader"></div>
</template>

<script>
import axios from 'axios';
import AppUsersList from './components/AppUsersList.vue';

export default {
  data() {
    return {
      name: '',
      users: [],
      loader: false
    }
  },
  mounted() {
    this.loadList();
    console.log(this.users);
  },
  methods: {
    async handleSubmit() {
      try {
        this.loader = true;

        const response = await fetch('https://vue-http-2d428-default-rtdb.firebaseio.com/users.json', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            firstName: this.name
          })
        });

        const data = await response.json();

        this.users = [...this.users, { firstName: this.name, id: data.id }];

        this.name = '';

        if (!response.ok) {
          throw new Error('Ошибка при попытке внесения пользователя в БД')
        }

      } catch (error) {
        console.error('Ошибка: ', error);
        alert(error.message);
      } finally {
        this.loader = false;
      }
    },
    async loadList() {
      try {
        this.loader = true;

        const response = await axios.get('https://vue-http-2d428-default-rtdb.firebaseio.com/users.json');

        this.users = Object.keys(response.data).map(key => {
          return {
            id: key,
            ...response.data[key]
          }
        });

        if (response.status !== 200) {
          throw new Error('Ошибка при запросе списка пользователей')
        }
      } catch (error) {
        console.error('Ошибка: ', error);
        // alert(error.message);
      } finally {
        this.loader = false;
      }
    },
    async removeUser(id) {
      try {
        const response = await axios.delete(`https://vue-http-2d428-default-rtdb.firebaseio.com/users/${id}.json`);

        this.users = this.users.filter(el => el.id !== id);

        if (response.status !== 200) {
          throw new Error('Ошибка при удалении пользователя')
        }
      } catch (error) {
        console.error('Ошибка: ', error);
        alert(error.message);
      }
    }
  },
  components: {
    'app-user-list': AppUsersList
  }
}
</script>

<style scoped lang="scss">
.loader {
  background-color: #42b983;
  border-radius: 50%;
  display: block;
  width: 50px;
  height: 50px;
  animation: spin 1s linear infinite;
  position: fixed;
  top: 50%;
  left: 50%;
  margin-top: -25px;
  margin-left: -25px;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
</style>