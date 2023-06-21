<template>
  <div className="container">
    <div
      @dragenter.prevent="toggleActive"
      @dragleave.prevent="toggleActive"
      @dragover.prevent
      @drop.prevent="toggleActive"
      :class="{ 'active-dropzone': active }"
      class="dropzone"
    >
      <img src="/image.svg" />
      <span>Drag & Drop your image here</span>
      <input
        type="file"
        id="dropzoneFile"
        class="dropzoneFile"
        accept="image/png, image/gif, image/jpeg"
      />
    </div>

    <div className="separated">
      <span>Or</span>

      <label for="dropzoneFile" class="chooseBtn">Choose a file</label>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref } from 'vue'

const active = ref(false)

const toggleActive = () => {
  active.value = !active.value
}
</script>
<style>
.container {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}
.dropzone {
  width: 338px;
  height: 218px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  row-gap: 16px;
  border: 2px dashed #97bef4;
  background-color: #f6f8fb;
  transition: 0.3s ease all;
}

.dropzone span {
  padding: 8px 12px;
  color: #bdbdbd;
  /* background-color: #41b883; */
  transition: 0.3s ease all;
}

.dropzone input {
  display: none;
}

.active-dropzone {
  color: #fff;
  border-color: #fff;
  background-color: #41b883;
}

/* .active-dropzone label {
  background-color: white;
  color: #41b883;
} */

.chooseBtn {
  color: white;
  background-color: #2f80ed;
  padding: 0.7rem 1.2rem;
  border-radius: 10px;
  cursor: pointer;
}
.separated {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
}

.separated span {
  color: #bdbdbd;
}
</style>
