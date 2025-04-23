<template>
  <div class="genre-filter">
    <button
      v-for="genre in genres"
      :key="genre.id"
      @click="selectGenre(genre.id)"
      :class="{ active: selectedGenre === genre.id }"
    >
      {{ genre.name }}
    </button>
    <button
      @click="selectGenre(null)"
      :class="{ active: selectedGenre === null }"
    >
      すべて
    </button>
  </div>
</template>

<script>
export default {
  name: "GenreFilter",
  props: {
    genres: Array,
    selectedGenre: [Number, null],
    selectGenre: Function
  }
};
</script>

<style scoped lang="scss">
.genre-filter {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
  margin-bottom: 20px;

  button {
    padding: 6px 12px;
    border: 1px solid #ccc;
    border-radius: 6px;
    background: #eee;
    cursor: pointer;

    &.active {
      background-color: #333;
      color: #fff;
    }
  }
}
</style>
