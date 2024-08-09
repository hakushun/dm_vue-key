<script setup lang="ts">
import { ref } from "vue";

type Item = {
  id: string;
};

const generateId = () => Math.floor(Math.random() * 10000).toString();

const items = ref<Item[]>(Array.from({ length: 5 }, (_) => ({ id: generateId() })));

const handleAdd = () => {
  items.value.unshift({ id: generateId() });
};

const handleDelete = (index: number) => {
  items.value.splice(index, 1);
};

const onDragStart = (id: string, event: DragEvent) => {
  if (!event.dataTransfer) return;
  event.dataTransfer.effectAllowed = "move";
  event.dataTransfer.setData("text/plain", id);

  const listItem = (event.target as HTMLElement).closest("li");
  if (listItem) event.dataTransfer.setDragImage(listItem, 0, 0);
};

const onDrop = (index: number, event: DragEvent) => {
  if (!event.dataTransfer) return;
  event.dataTransfer.dropEffect = "move";
  const id = event.dataTransfer.getData("text/plain");
  const targetIndex = items.value.findIndex((item) => item.id === id);

  if (targetIndex === -1) return;

  const [removed] = items.value.splice(targetIndex, 1);
  items.value.splice(index, 0, removed);
};
</script>

<template>
  <main class="root">
    <section class="controller">
      <button type="button" @click="handleAdd">Add item to the beginning of the list</button>
    </section>
    <section class="wrapper">
      <h1><code>:key="item.id"</code></h1>
      <ul class="list">
        <li v-for="(item, index) in items" :key="item.id" class="item" @dragover.prevent @drop="onDrop(index, $event)">
          <span draggable="true" :id="item.id" class="icon" @dragstart="onDragStart(item.id, $event)">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="size-6"
            >
              <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5" />
            </svg>
          </span>
          <label :for="`${item.id}_left`" class="label">{{ item.id }}</label>
          <input type="text" :id="`${item.id}_left`" class="input" />
          <button type="button" :disabled="items.length === 1" @click="handleDelete(index)">delete</button>
        </li>
      </ul>
    </section>
    <section class="wrapper">
      <h1><code>:key="index"</code></h1>
      <ul class="list">
        <li v-for="(item, index) in items" :key="index" class="item" @dragover.prevent @drop="onDrop(index, $event)">
          <span draggable="true" :id="item.id" class="icon" @dragstart="onDragStart(item.id, $event)">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              class="size-6"
            >
              <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5" />
            </svg>
          </span>
          <label :for="`${item.id}_right`" class="label">{{ item.id }}</label>
          <input type="text" :id="`${item.id}_right`" class="input" />
          <button type="button" :disabled="items.length === 1" @click="handleDelete(index)">delete</button>
        </li>
      </ul>
    </section>
  </main>
</template>

<style scoped>
.root {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: 50px 1fr;
}
.controller {
  grid-column: 1 / 3;
  display: flex;
  justify-content: center;
}
.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
}
.list {
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.item {
  display: flex;
  gap: 4px;
  align-items: center;
}
.icon {
  display: flex;
  width: 40px;
  height: 100%;
  cursor: grab;
}
.label {
  min-width: 52px;
}
.input {
  width: 100%;
}
</style>
