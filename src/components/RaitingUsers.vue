<template>
  <div class="container">
    <div class="row">
      <table class="table table-hover">
        <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Пользователь</th>
          <th scope="col">Лучший результат по количеству верных ответов</th>
        </tr>
        </thead>
        <tbody>
          <tr v-for="(user, index) in listResultUsers">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ user.displayName}}</td>
            <td class="correct-answer"> {{ user.Rate }}</td>
          </tr>
        </tbody>
      </table>
      <div v-if="showSpinner" class="spinner-wrapper">
        <img src="../assets/spinner.gif" alt="spinner">
      </div>
    </div>
  </div>
</template>


<script>
  import * as firebase from  'firebase'
  export  default {
    name: 'RaitingUsers',
    data() {
      return {
        listResultUsers: [],
        showSpinner: true
      }
    },
    created() {
      this.getRatingUsers();
      if (this.$router.currentRoute.name === 'MainMenu') {
        this.$store.state.nameControlButton = 'Меню'
      } else {
        this.$store.state.nameControlButton = 'Вернуться в меню'
      }
    },
    methods: {
      getRatingUsers() {
        firebase.database().ref('/TotalUsers').once('value').then((snapshot) => {
          this.getResultUser(snapshot.val());
        }).catch((e) => {
               console.log('error', e)
              });
      },
      getResultUser(objectResultUser) {
        let _this = this;
        for (let key in objectResultUser) {
          _this.listResultUsers.push({
            'displayName': objectResultUser[key].displayName,
            'Rate': objectResultUser[key].resultTesting
          })
        }
        for (let i = 0; i < this.listResultUsers.length; i++) {
          for (let k = 0; k < this.listResultUsers.length; k++) {
            if (this.listResultUsers[i].Rate > this.listResultUsers[k].Rate) {
              [this.listResultUsers[i], this.listResultUsers[k]] = [this.listResultUsers[k], this.listResultUsers[i]]
            }
          }
        }
        this.showSpinner = false;
      }
    }
  }
</script>

<style scoped>

  .correct-answer {
    color: green;
    font-weight: 900;
    font-size: 18px;
  }

  body {
    font-family: 'Open Sans', sans-serif;
  }

  .row {
    display: grid;
    grid-template-columns: 1fr;
    grid-template-rows: 1fr;
    grid-gap: 30px;

    min-height: 420px;
    text-align: center;

    border-radius: 10px;
    border-top-left-radius: 0;
    border-top-right-radius: 0;
    border: 2px solid black;

    background: beige;
  }

  .raiting-table {
    margin-top: 30px;
  }
</style>
