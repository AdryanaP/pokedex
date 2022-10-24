<template>
  <div v-if="error" class="error-msg">
    <font-awesome-icon icon="fa-solid fa-triangle-exclamation" size="lg" fade />
    <p><span>Pokemon Inválido!</span> Verifique se escreveu corretamente.</p>
  </div>
</template>

<script>
export default {
  title: "ErrorWarning",
  props: {
    error: {
      type: Boolean,
      required: true,
      default: false,
    },
  },
};
</script>

<style>
.error-msg {
  background: #fbd5d5;
  padding: 0.8rem 1.5rem;
  border-radius: 5px;
  position: fixed;
  bottom: 1rem;
  width: -webkit-fill-available;
  margin: 0 4rem;
  display: flex;
  gap: 1rem;
  color: #9b1c1c;
}

.error-msg p {
  margin: 0;
}

.error-msg p span {
  font-weight: bold;
}
</style>
