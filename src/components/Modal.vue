<template>
  <div class="modal-backdrop" @click="close">
    <div class="modal" @click.stop>
      <header>
        <h2>{{ title }}</h2>
        <button @click="close">Закрыть</button>
      </header>
      <main>
        <slot></slot>
      </main>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "Modal",
  props: {
    title: {
      type: String,
      required: true,
    },
  },
  emits: ["close"],
  methods: {
    close() {
      this.$emit("close");
    },
  },
});
</script>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal {
  background: white;
  padding: 20px;
  border-radius: 8px;
  width: 400px;
  cursor: default; /* Курсор по умолчанию внутри модального окна */
}

button {
  cursor: pointer; /* Курсор на кнопке в модальном окне */
}
</style>
