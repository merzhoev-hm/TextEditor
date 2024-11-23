<script setup>
import { ref, reactive, onMounted } from "vue";

const editor = ref(null); // Ссылка на редактируемую область
const state = reactive({
  history: [], // История изменений
  historyIndex: -1,
});

// Сохранение текущего состояния редактора в историю
const saveState = () => {
  const editorContent = editor.value.innerHTML;
  state.history = state.history.slice(0, state.historyIndex + 1);
  state.history.push(editorContent);
  state.historyIndex++;
};

// Отмена действия
const undo = () => {
  if (state.historyIndex > 0) {
    state.historyIndex--;
    editor.value.innerHTML = state.history[state.historyIndex];
  }
};

// Повтор действия
const redo = () => {
  if (state.historyIndex < state.history.length - 1) {
    state.historyIndex++;
    editor.value.innerHTML = state.history[state.historyIndex];
  }
};

// Форматирование текста (заголовок или абзац)
const format = (tag) => {
  document.execCommand(
    tag === "h1" ? "formatBlock" : "formatBlock",
    false,
    tag
  );
};

// Вставка изображения по URL
const insertImage = () => {
  const url = prompt("Введите URL изображения:");
  if (url) {
    document.execCommand("insertImage", false, url);
  }
};

// Копирование HTML в буфер обмена
const copyHtml = () => {
  const html = editor.value.innerHTML;
  navigator.clipboard.writeText(html).then(() => {
    alert("HTML скопирован в буфер обмена!");
  });
};

// Инициализация редактора и сохранение начального состояния
onMounted(() => {
  saveState();
});
</script>

<template>
  <div class="editor-container">
    <!-- Панель инструментов -->
    <div class="toolbar">
      <button @click="undo"><i class="icon">↶</i></button>
      <button @click="redo"><i class="icon">↷</i></button>
      <button @click="format('h1')"><i class="icon">H1</i></button>
      <button @click="format('p')"><i class="icon">P</i></button>
      <button @click="insertImage"><i class="icon">🖼️</i></button>
      <button @click="copyHtml"><i class="icon">📋</i></button>
    </div>

    <!-- Редактируемая область -->
    <div class="editor" contenteditable="true" ref="editor" @input="saveState">
      Редактируемый текст...
    </div>
  </div>
</template>

<style>
.editor-container {
  width: 80%;
  margin: 0 auto;
}
.toolbar {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;
}
.toolbar button {
  padding: 8px;
  background: #f4f4f4;
  border: 1px solid #ccc;
  cursor: pointer;
}
.toolbar button:hover {
  background: #e0e0e0;
}
.editor {
  border: 1px solid #ccc;
  padding: 16px;
  min-height: 300px;
  background: #fff;
}
</style>
