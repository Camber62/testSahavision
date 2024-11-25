<template>
  <div class="app-container">
    <header>
      <h1>Выбор папки</h1>
      <div v-if="selectedFolderName" class="selected-folder">
        Выбранная папка: {{ selectedFolderName }}
      </div>
      <div class="action-buttons">
        <button class="open-modal-btn" @click="openModal">Открыть</button>
        <button
            class="delete-selection-btn"
            :disabled="!selectedFolderId"
            @click="clearSelection"
        >
          Удалить выбор
        </button>
      </div>
    </header>

    <FolderModal
        v-if="isModalOpen"
        :folders="folders"
        @close="isModalOpen = false"
        @select="handleSelect"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from "vue";
import FolderModal from "./components/FolderModal.vue";
import folderData from './folders.json';

export interface Folder {
  id: number;
  name: string;
  children: Folder[];
}

export default defineComponent({
  name: "App",
  components: { FolderModal },
  setup() {
    const isModalOpen = ref(false);
    const selectedFolderId = ref<number | null>(null);
    const folders = ref<Folder[]>(folderData.folders);

    // Вычисляем имя выбранной папки
    const selectedFolderName = computed(() => {
      if (!selectedFolderId.value) return null;
      const findFolderById = (folders: Folder[], id: number): Folder | null => {
        for (const folder of folders) {
          if (folder.id === id) return folder;
          if (folder.children.length) {
            const result = findFolderById(folder.children, id);
            if (result) return result;
          }
        }
        return null;
      };
      const folder = findFolderById(folders.value, selectedFolderId.value);
      return folder?.name || null;
    });

    const openModal = () => (isModalOpen.value = true);

    const handleSelect = (id: number) => {
      if (selectedFolderId.value === id) {
        selectedFolderId.value = null;
      } else {
        selectedFolderId.value = id;
      }
      isModalOpen.value = false;
    };

    // Сброс выбора папки
    const clearSelection = () => {
      selectedFolderId.value = null;
    };

    return {
      isModalOpen,
      selectedFolderId,
      selectedFolderName,
      folders,
      openModal,
      handleSelect,
      clearSelection,
    };
  },
});
</script>

<style scoped>
.app-container {
  padding: 20px;
  font-family: Arial, sans-serif;
}

header h1 {
  margin-bottom: 20px;
}

.selected-folder {
  margin-bottom: 10px;
  font-size: 16px;
  font-weight: bold;
  color: #333;
}

.action-buttons {
  display: flex;
  gap: 10px;
}

.open-modal-btn,
.delete-selection-btn {
  padding: 10px 15px;
  font-size: 14px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.open-modal-btn {
  background-color: #007bff;
  color: white;
}

.open-modal-btn:hover {
  background-color: #0056b3;
}

.delete-selection-btn {
  background-color: #dc3545;
  color: white;
}

.delete-selection-btn:disabled {
  background-color: #6c757d;
  cursor: not-allowed;
  opacity: 0.65;
}

.delete-selection-btn:hover:not(:disabled) {
  background-color: #c82333;
}
</style>
