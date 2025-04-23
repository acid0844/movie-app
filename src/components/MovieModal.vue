<template>
  <div class="modal-overlay" @click.self="closeModal">
    <div class="modal-content">
      <button class="modal-close" @click="closeModal">×</button>
      <img :src="imageUrl" />
      <h2>{{ movie.title }}</h2>
      <p>評価: {{ movie.vote_average }} / 10</p>
      <p>公開日: {{ movie.release_date }}</p>
      <p>{{ movie.overview }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "MovieModal",
  props: {
    movie: Object,
    imageUrl: String,
    closeModal: Function
  }
};
</script>

<style scoped lang="scss">
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;

  .modal-content {
    background: #fff;
    padding: 24px;
    max-width: 600px;
    width: 90%;
    max-height: 80vh;
    overflow-y: auto;
    border-radius: 10px;
    position: relative;

    img {
      width: 100%;
      max-height: 300px;
      object-fit: contain;
      border-radius: 6px;
      margin-bottom: 16px;
    }

    .modal-close {
      position: absolute;
      top: 8px;
      right: 12px;
      font-size: 24px;
      background: none;
      border: none;
      cursor: pointer;
    }
  }
}
</style>
