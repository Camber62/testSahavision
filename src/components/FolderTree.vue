<template>
  <ul>
    <li v-for="folder in folders" :key="folder.id">
      <div
          @click.stop="selectFolder(folder.id)"
          :class="{ selected: folder.id === selectedFolder }"
      >
        <span @click.stop="toggle(folder.id)">
          {{ folder.children.length ? (folder.isOpen ? "📂" : "📁") : "📄" }}
        </span>
        {{ folder.name }}
      </div>
      <FolderTree
          v-if="folder.isOpen"
          :folders="folder.children"
          :selected-folder="selectedFolder"
          @select="handleSelect"
      />
    </li>
  </ul>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";

interface Folder {
  id: number;
  name: string;
  children: Folder[];
  isOpen?: boolean;
}

export default defineComponent({
  name: "FolderTree",
  props: {
    folders: {
      type: Array as PropType<Folder[]>,
      required: true,
    },
    selectedFolder: {
      type: Number,
      required: false,
    },
  },
  emits: ["select"],
  methods: {
    toggle(id: number) {
      const folder = this.folders.find((f) => f.id === id);
      if (folder) folder.isOpen = !folder.isOpen;
    },
    selectFolder(id: number) {
      this.$emit("select", id);
    },
    handleSelect(id: number) {
      this.$emit("select", id);
    },
  },
});
</script>

<style scoped>
.selected {
  font-weight: bold;
  color: blue;
  cursor: pointer;
}
</style>
