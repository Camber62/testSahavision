<template>
  <Modal title="Выберите папку" @close="close">
    <FolderTree
        :folders="folders"
        :selected-folder="selectedFolder"
        @select="selectFolder"
    />
    <footer>
      <button
          class="confirm-btn"
          @click="confirm"
          :disabled="!selectedFolder"
      >
        Ок
      </button>
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
    const selectedFolder = ref<number | undefined>();

    const close = () => emit("close");
    const confirm = () => {
      if (selectedFolder.value) emit("select", selectedFolder.value);
      close();
    };
    const selectFolder = (id: number) => {
      selectedFolder.value = selectedFolder.value === id ? undefined : id;
    };

    return { selectedFolder, close, confirm, selectFolder };
  },
});
</script>

<style scoped>
footer {
  display: flex;
  justify-content: flex-end;
  margin-top: 10px;
}

button {
  padding: 10px 15px;
  font-size: 14px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.confirm-btn {
  background-color: #28a745;
  color: white;
}

.confirm-btn:disabled {
  background-color: #6c757d;
  cursor: not-allowed;
  opacity: 0.65;
}

.confirm-btn:hover:not(:disabled) {
  background-color: #218838;
  transform: scale(1.05);
}

.confirm-btn:active {
  background-color: #1e7e34;
  transform: scale(0.95);
}
</style>
