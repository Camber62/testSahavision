<template>
  <div class="app-container">
    <header>
      <h1>Выбор папки</h1>
      <div v-if="selectedFolderName" class="selected-folder">
        Выбранная папка: {{ selectedFolderName }}
      </div>
      <button class="open-modal-btn" @click="openModal">Открыть</button>
    </header>

    <!-- Модальное окно с деревом папок -->
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

interface Folder {
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
    const folders = ref<Folder[]>([
      {
        id: 1,
        name: "Папка 1",
        children: [
          { id: 2, name: "Папка 1.1", children: [] },
          {
            id: 3,
            name: "Папка 1.2",
            children: [{ id: 4, name: "Папка 1.2.1", children: [] }],
          },
        ],
      },
      { id: 5, name: "Папка 2", children: [] },
    ]);

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
      // Если папка уже выбрана, снимаем выбор
      if (selectedFolderId.value === id) {
        selectedFolderId.value = null; // Снимаем выбор
      } else {
        selectedFolderId.value = id; // Выбираем новую папку
      }
      // Закрываем модальное окно после выбора
      isModalOpen.value = false;
    };

    return { isModalOpen, selectedFolderId, selectedFolderName, folders, openModal, handleSelect };
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

.open-modal-btn {
  padding: 10px 15px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
}

.open-modal-btn:hover {
  background-color: #0056b3;
}

button {
  cursor: pointer;
}
</style>
