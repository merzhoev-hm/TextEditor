<script setup>
import { ref, reactive, onMounted } from "vue";

const editor = ref(null);
const currentFormat = ref("p"); // Формат по умолчанию
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

// Применение стиля (H1, P)
const applyStyle = (style) => {
  currentFormat.value = style;
  document.execCommand("fontSize", false, style === "h1" ? "6" : "3");
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
    <div class="toolbar">
      <button @click="undo">
        <img src="/public/VectorL.svg" alt="" />
      </button>
      <button @click="redo">
        <img src="/public/VectorR.svg" alt="" />
      </button>
      <button @click="applyStyle('h1')">
        <img src="/public/h1.svg" alt="" />
      </button>
      <button @click="applyStyle('p')">
        <img src="/public/p.svg" alt="" />
      </button>
      <button @click="insertImage">
        <img src="/public/InsertImg.svg" alt="" />
      </button>
      <div @click="copyHtml">Скопировать HTML</div>
    </div>

    <div
      class="editor"
      contenteditable="true"
      ref="editor"
      @input="saveState"
    ></div>
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
  align-items: center;
}

.toolbar button {
  width: 42px;
  height: 38px;
  border-radius: 4px;
  background: rgba(40, 40, 40, 1);
  border: none;
  cursor: pointer;
}

.toolbar div {
  font-family: Roboto;
  font-size: 15px;
  font-weight: 400;
  line-height: 23px;
  color: rgba(99, 158, 255, 1);
  cursor: pointer;
}

.toolbar button:hover {
  background: #e0e0e0;
}

.toolbar div:hover {
  color: #e0e0e0;
}

.editor {
  outline: none;
  padding: 16px;
  min-height: 100vh;
  color: rgba(234, 234, 234, 1);
  border: 1px solid #2c2c2c;
  font-size: 16px;
  line-height: 1.5;
}

.editor h1 {
  font-size: 32px;
  font-weight: bold;
  margin: 0;
}

.editor p {
  font-size: 16px;
  margin: 0;
}

img {
  max-width: 100%;
}
</style>
