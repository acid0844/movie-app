<template>
  <div class="movie-card" @click="onClickCard">
    <img :src="imageUrl" />
    <div class="card-content">
      <h2>{{ movie.title }}</h2>
      <button @click.stop="toggleFavorite(movie)">
        {{ isFavorite(movie) ? '★ お気に入り済み' : '☆ お気に入り追加' }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "MovieCard",
  props: {
    movie: Object,
    imageUrl: String,
    isFavorite: Function,
    toggleFavorite: Function,
    onClickCard: Function
  }
};
</script>

<style scoped lang="scss">
.movie-card {
  border: 1px solid #ccc;
  padding: 10px;
  background: #fff;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  cursor: pointer;
  transition: transform 0.2s ease;

  &:hover {
    transform: scale(1.03);
  }

  img {
    width: 100%;
    border-radius: 4px;
  }

  button {
    margin-top: 10px;
    padding: 4px 8px;
    font-size: 0.9rem;
    border: none;
    border-radius: 6px;
    background-color: #ffd700;
    cursor: pointer;
  }

  .card-content {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    flex: 1;
  }
}
</style>