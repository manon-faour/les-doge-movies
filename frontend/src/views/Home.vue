<template>
  <div class="home">
    <img alt="doge photo" src="../assets/doge.png" class="logo" />
    <h1>DogeMovies</h1>
    <router-link to="/add"><button>+ Ajouter un film</button></router-link>

    <div v-if="user_id === -1" class="login">
      <h3>Connecte-toi pour avoir accès à plus de fonctionnalités</h3>
      <div class="input-field">
        <label for="email">Entrez votre email </label>
        <input v-model="user_email" id="email" @keyup.enter="connect" />
      </div>
      <div v-if="emailAbsent" class="error">
        Cette adresse n'a pas de compte. Créez un compte pour vous connecter
      </div>
      <button @click="connect">Connexion</button>
      <router-link to="/Signin" tag="button">
        <button>Inscription</button>
      </router-link>
    </div>

    <h2>Top 12</h2>
    <Caroussel :movies="top12" />

    <div v-if="user_id !== -1" class="perso">
      <h2>Pour vous</h2>
      <Caroussel
        v-if="recommendedMovies.length > 0"
        :movies="recommendedMovies"
      />
      <p v-if="recommendedMovies.length == 0">
        Note quelques films pour qu'on puisse trouver des films qui te
        correspondent
      </p>
      <h2>Mes Films</h2>
      <Caroussel v-if="myMovies.length > 0" :movies="myMovies" />
      <p v-if="myMovies.length == 0">Aucun film dans ta liste..</p>
    </div>
  </div>
</template>

<script>
import Caroussel from "../components/Caroussel.vue";
import axios from "axios";

export default {
  name: "Home",
  created() {
    if (localStorage.user_id) {
      this.user_id = localStorage.user_id;
    }
    this.fetchTop12();
    this.fetchMyMovies(this.user_id);
    this.fetchReco(this.user_id);
  },
  data: function () {
    return {
      emailAbsent: false,
      top12: [],
      recommendedMovies: [],
      myMovies: [],
      user_id: -1,
      user_email: "",
    };
  },
  components: {
    Caroussel,
  },
  methods: {
    fetchTop12: function () {
      axios
        .get(`${process.env.VUE_APP_BACKEND_BASE_URL}/movies/top/12`)
        .then((response) => {
          this.top12 = response.data.movies;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    fetchMyMovies: function (user_id) {
      if (user_id !== -1) {
        axios
          .get(
            `${process.env.VUE_APP_BACKEND_BASE_URL}/movies/mymovies/` +
              String(user_id)
          )
          .then((response) => {
            this.myMovies = response.data.movies;
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
    fetchReco: function (user_id) {
      if (user_id !== -1) {
        axios
          .get(
            `${process.env.VUE_APP_BACKEND_BASE_URL}/users/recommended/` +
              String(user_id)
          )
          .then((response) => {
            this.recommendedMovies = response.data.movies;
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
    getId: function (user_mail) {
      axios
        .get(
          `${process.env.VUE_APP_BACKEND_BASE_URL}/users/email/` +
            String(user_mail)
        )
        .then((response) => {
          this.user_id = response.data.user._id;
          localStorage.user_id = response.data.user._id;
          this.fetchMyMovies(this.user_id);
          this.fetchReco(this.user_id);
          window.location.reload();
        })
        .catch((error) => {
          this.emailAbsent = true;
          console.log(error);
        });
    },
    connect: function () {
      this.getId(this.user_email);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.home {
  padding: 20px;
  text-align: center;
}

h1 {
  font-size: 3em;
}

.error {
  color: red;
}

.logo {
  height: 200px;
}

@keyframes wiggle {
  0% {
    transform: rotate(0deg);
  }
  25% {
    transform: rotate(-30deg);
  }
  50% {
    transform: rotate(30deg);
  }
  75% {
    transform: rotate(-30deg);
  }
  100% {
    transform: rotate(0deg);
  }
}

.logo:hover {
  animation-name: wiggle;
  animation-duration: 0.4s;
}

h2 {
  text-align: left;
  margin-left: 10%;
  font-size: 2.3em;
  margin-top: 100px;
}
.login {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.login > h3 {
  font-size: 2.3em;
}
.input-field {
  font-size: 1.5em;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: flex-end;
  justify-content: center;
}
.input-field label {
  min-width: 300px;
}
.input-field > input {
  margin: 0px 10px;
  width: 250px;
  font-size: 0.8em;
}
</style>
