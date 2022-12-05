<template>
  <div class="main-block">
    <div class="user-table">
      <button @click="showModal = true" class="add-button button">
        Добавить
      </button>
      <div class="user-table__tittle">
        <span @click="sortBy('name')">Имя</span>
        <span @click="sortBy('phone')">Телефон</span>
      </div>
      <ul class="user-table__list" v-for="(user,n) in users" :key="user.n">
        <li>
          <span>{{ user.name }}</span>
          <span>{{ user.phone }}</span>
          <div v-if="user.parentName && user.parentPhone" class="user-parent">
            <span>{{ user.parentName.name }}</span>
            <span>{{ user.parentPhone.phone }}</span>
          </div>
          <button @click="removeUser(n)"></button>
        </li>
      </ul>
    </div>
    <div class="overlay" v-if="showModal" @click="showModal = false"></div>
    <div v-if="showModal" class="add-modal">
      <h2>Добавление пользователя</h2>
      <div class="add-modal__input">
        <label for="userName">Имя</label>
        <input id="userName" v-model="userName">
      </div>
      <div class="add-modal__input">
        <label for="userPhone">Телефон</label>
        <input id="userPhone" v-model="userPhone">
      </div>
      <div class="add-modal__input">
        <label for="userParent">Родитель</label>
        <select v-model="parent">
          <option v-for="(user,n) in users" :key="n" :value="n">{{ user.name }} {{ user.phone }}</option>
        </select>
      </div>
      <button class="save-button button" @click="addUser">Сохранить</button>
      <button @click="showModal = false" class="close-button"></button>
    </div>
  </div>

</template>

<script>
export default {
  name: 'Test',
  data () {
    return {
      users: [],
      userName: null,
      userPhone: null,
      parent: null,
      showModal: false,
      sortRevers: false
    }
  },
  mounted () {
    if (localStorage.getItem('users')) {
      try {
        this.users = JSON.parse(localStorage.getItem('users'))
      } catch (e) {
        localStorage.removeItem('users')
      }
    }
  },
  methods: {
    addUser () {
      console.log(this.users[this.parent].name)
      if (!this.userName || !this.userPhone) return
      let newUser = {
        name: this.userName,
        phone: this.userPhone,
        parentName: this.parent !== null ? {
          name: this.users[this.parent].name
        } : null,
        parentPhone: this.parent !== null ? {
          phone: this.users[this.parent].phone
        } : null
      }
      this.users.push(newUser)
      this.userName = ''
      this.userPhone = ''
      this.parentName = ''
      this.parentPhone = ''
      this.parent = ''
      this.saveUsers()
    },
    removeUser (x) {
      this.users.splice(x, 1)
      this.saveUsers()
    },
    saveUsers () {
      let parsed = JSON.stringify(this.users)
      localStorage.setItem('users', parsed)
    },
    sortBy: function (arg) {
      this.sortRevers = !this.sortRevers
      this.users.sort((a, b) => {
        return this.sortRevers
          ? (a[arg] < b[arg] ? 1 : -1)
          : (a[arg] > b[arg] ? 1 : -1)
      })
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

.button{
  background: #cccccc;
  border: 1px solid #000;
  padding: 7px 15px;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.button:hover{
  opacity: 0.8;
}

.main-block{
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  max-width: 1240px;
  margin: 0 auto;
}

.add-button{
  max-width: 200px;
  align-self: flex-end;
}

.user-table{
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
}

.user-table__tittle{
  display: flex;
  justify-content: space-between;
  width: 100%;
  margin: 20px 0 0 0;
}

.user-table__tittle span{
  text-align: start;
  width: 50%;
  padding: 15px;
  outline: 1px solid #000;
  margin: 0 1px 1px 0;
  cursor: pointer;
}

.user-table__list{
  padding: 0;
  margin: 0;
}

.user-table__list li{
  display: flex;
  justify-content: space-between;
  width: 100%;
  position: relative;
  flex-wrap: wrap;
}

.user-table__list span{
  text-align: start;
  box-sizing: border-box;
  width: calc(250px - 1px);
  padding: 15px;
  outline: 1px solid #000;
  margin: 0 1px 1px 0;
}
.user-parent{
  width: 100%;
  display: flex;
  padding: 0 0 0 30px;
}

.user-parent span:first-child{
  width: calc(220px - 1px);
}

.user-table__list button{
  position: absolute;
  right: 15px;
  border: none;
  background: none;
  height: 15px;
  width: 15px;
  top: 15px;
  cursor: pointer;
}

.user-table__list button::before,.user-table__list button::after{
  position: absolute;
  content: '';
  background: #333333;
  left: 0;
  width: 15px;
  height: 2px;
  transform: rotate(45deg);
}

.user-table__list button::after{
  transform: rotate(135deg);
}

.overlay{
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.add-modal{
  position: relative;
  display: flex;
  flex-direction: column;
  gap: 15px;
  max-width: 400px;
  width: 100%;
  padding: 15px;
  border: 1px solid #000;
  z-index: 9999;
  margin: 51px 0 0 0;
  height: 100%;
}

.add-modal h2{
  text-align: left;
  margin: 0;
  font-size: 18px;
  color: #333333;
}

.add-modal__input{
  display: flex;
  justify-content: space-between;
}
.add-modal__input input,.add-modal__input select{
  max-width: 220px;
  box-sizing: border-box;
  width: 55%;
  padding: 3px 5px;
  margin: 0;
}
.save-button{
  max-width: 120px;
}

.close-button{
  position: absolute;
  top: 15px;
  right: 15px;
  background: none;
  border: none;
  cursor: pointer;
  width: 15px;
  height: 15px;
}

.close-button::before,.close-button::after{
  position: absolute;
  content: '';
  background: #333333;
  top: 50%;
  left: 0;
  width: 15px;
  height: 2px;
  transform: rotate(45deg);
}
.close-button::after{
  transform: rotate(135deg);
}
</style>
