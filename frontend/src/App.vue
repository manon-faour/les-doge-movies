<template>
  <nav>
    <router-link class="nav-link" to="/"
      ><img src="./assets/doge.png" alt="doge photo" class="logo"
    /></router-link>
    <div class="searchbar">
      <img src="./assets/search.svg" alt="search icon" class="search-logo" />
      <input
        v-model="searchText"
        type="text"
        @keyup.enter="search"
        class="search-input"
        placeholder="Rechercher..."
      />
    </div>
    <div class="user-block">
      <p v-if="connected" class="user-name">{{ user_name }}</p>
      <img
        src="./assets/logout.svg"
        alt="logout icon"
        class="logout-icon"
        v-if="connected"
        @click="logout"
      />
    </div>
  </nav>
  <router-view />
</template>

<script>
import axios from "axios";

export default {
  name: "navbar",
  data: function () {
    return {
      searchText: "",
      user_name: "",
      connected: false,
    };
  },
  methods: {
    search: function () {
      this.$router.push({
        name: "Results",
        query: { search: this.searchText },
      });
    },
    logout: function () {
      localStorage.removeItem("user_id");
      this.connected = false;
      window.location.reload();
    },
  },
  created() {
    if (localStorage.user_id) {
      axios
        .get(
          `${process.env.VUE_APP_BACKEND_BASE_URL}/users/` +
            String(localStorage.user_id)
        )
        .then((response) => {
          this.user_name = response.data.user.firstName;
          this.connected = true;
        })
        .catch((error) => {
          console.log(error);
        });
    }
  },
};
</script>

<style scoped>
nav {
  width: 100%;
  height: 70px;
  background: #3d5a6c;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
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

.logo {
  height: 50px;
  margin-left: 15px;
  margin-right: 15px;
  transition: 0.4s ease;
}

.logo:hover {
  animation-name: wiggle;
  animation-duration: 0.4s;
}

.searchbar {
  background: #becfda;
  width: 40%;
  display: flex;
  height: 45px;
  border-radius: 10px;
  align-items: center;
}
.search-input {
  width: 100%;
  border: 0px;
  background: transparent;
  margin-left: 5px;
  font-size: 18px;
}

.search-input:focus {
  outline: none;
}

.search-logo {
  height: 45px;
  margin-bottom: 5px;
  margin-left: 5px;
  fill: white;
}

.user-block {
  display: flex;
}
.user-name {
  font-size: 1.4em;
}
.logout-icon {
  width: 30px;
  margin: 0px 10px;
  transition: 0.2s;
}
.logout-icon:hover {
  transform: translateX(5px);
}
</style>
