<template>
  <router-link :to="{ name: 'Details', query: { id: movie._id } }">
    <div class="image-box">
      <img v-bind:src="movie.poster_path" :alt="movie.title" />
      <div class="title">
        {{ movie.title }}
      </div>
    </div>
  </router-link>
</template>

<script>
export default {
  name: "Movie",
  props: {
    movie: Object,
  },
};
</script>

<style scoped>
.image-box {
  display: flex;
  flex-direction: column;
  align-items: center;
  border-radius: 10px;
  position: relative;
  justify-content: space-evenly;
  box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.25);
  transition: 0.2s ease-in-out;
}
.image-box:hover {
  transform: translateY(-7px);
}
img {
  width: 100%;
  border-radius: 10px 10px 0px 0px;
}
.title {
  margin: 20px;
}

a {
  color: black;
  text-decoration: none;
}
</style>
