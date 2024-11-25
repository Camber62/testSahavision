<template>
  <Modal title="Выберите папку" @close="close">
    <FolderTree
        :folders="folders"
        :selected-folder="selectedFolder"
        @select="selectFolder"
    />
    <footer>
      <button @click="confirm" :disabled="!selectedFolder">Ок</button>
      <button @click="close">Закрыть</button>
    </footer>
  </Modal>
</template>

<script lang="ts">
import { defineComponent, PropType, ref } from "vue";
import Modal from "./Modal.vue";
import FolderTree from "./FolderTree.vue";

interface Folder {
  id: number;
  name: string;
  children: Folder[];
}

export default defineComponent({
  name: "FolderModal",
  components: { Modal, FolderTree },
  props: {
    folders: {
      type: Array as PropType<Folder[]>,
      required: true,
    },
  },
  emits: ["close", "select"],
  setup(_, { emit }) {
    const selectedFolder = ref<number | null>(null);

    const close = () => emit("close");
    const confirm = () => {
      if (selectedFolder.value) emit("select", selectedFolder.value);
      close();
    };
    const selectFolder = (id: number) => {
      if (selectedFolder.value === id) {
        selectedFolder.value = null; // Отменить выбор при повторном клике
      } else {
        selectedFolder.value = id; // Выбрать новую папку
      }
    };

    return { selectedFolder, close, confirm, selectFolder };
  },
});
</script>

<style scoped>
footer {
  display: flex;
  justify-content: space-between;
  margin-top: 10px;
}
button:disabled {
  opacity: 0.5;
  pointer-events: none;
}
</style>
