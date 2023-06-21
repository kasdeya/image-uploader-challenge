<script setup lang="ts">
import { ref } from 'vue'
import DropZone from './components/DropZone.vue'
import { getDownloadURL, uploadBytesResumable, getStorage, ref as fireRef } from 'firebase/storage'

const storage = getStorage()

let dropzoneFile = ref<File>()
let imgUrl = ref('')
let progressPercent = ref(0)
let loading = ref(false)

const drop = (event: DragEvent) => {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif']
  if (!event.dataTransfer) return
  if (!allowedTypes.includes(event.dataTransfer.files[0].type)) return
  dropzoneFile.value = event.dataTransfer.files[0]
  // console.log(dropzoneFile.value)
  const imageRef = fireRef(storage, dropzoneFile.value?.name)

  const uploadTask = uploadBytesResumable(imageRef, dropzoneFile.value)

  uploadTask.on(
    'state_changed',
    (snapshot) => {
      const progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100
      progressPercent.value = progress
      loading.value = true
      console.log('Upload is ' + progress + '% done')
      switch (snapshot.state) {
        case 'paused':
          console.log('Upload is paused')
          break
        case 'running':
          console.log('Upload is running')
          break
      }
    },
    (error) => {
      // Handle unsuccessful uploads
    },
    () => {
      loading.value = false
      getDownloadURL(uploadTask.snapshot.ref).then((downloadURL) => {
        console.log('file available at', downloadURL)
        imgUrl.value = downloadURL
      })
    }
  )
}

const selectedFile = () => {
  const input = document.querySelector('.dropzoneFile') as HTMLInputElement | null
  if (input && input.files && input.files.length > 0) {
    const allowedTypes = ['image/jpeg', 'image/png', 'image/gif']
    if (!input.files[0]) return
    if (!allowedTypes.includes(input.files[0].type)) return
    dropzoneFile.value = input.files[0]
    const imageRef = fireRef(storage, dropzoneFile.value?.name)

    const uploadTask = uploadBytesResumable(imageRef, dropzoneFile.value)

    uploadTask.on(
      'state_changed',
      (snapshot) => {
        const progress = (snapshot.bytesTransferred / snapshot.totalBytes) * 100
        progressPercent.value = progress
        loading.value = true
        console.log('Upload is ' + progress + '% done')
        switch (snapshot.state) {
          case 'paused':
            console.log('Upload is paused')
            break
          case 'running':
            console.log('Upload is running')
            break
        }
      },
      (error) => {
        // Handle unsuccessful uploads
      },
      () => {
        loading.value = false
        getDownloadURL(uploadTask.snapshot.ref).then((downloadURL) => {
          console.log('file available at', downloadURL)
          imgUrl.value = downloadURL
        })
      }
    )
  }
}

const copyUrl = () => {
  if (imgUrl.value.length > 1) {
    navigator.clipboard.writeText(imgUrl.value)
  }
}
</script>

<template>
  <main>
    <div v-if="!dropzoneFile" className="uploadContainer">
      <h1>Upload your image</h1>
      <p className="greyText">File should be Jpeg, Png, ...</p>
      <DropZone @drop.prevent="drop" @change="selectedFile" />
      <!-- <span className="file-info">File: {{ dropzoneFile?.name }}</span> -->
      <!-- <img v-if="imgUrl" :src="imgUrl" alt="" srcset="" /> -->
    </div>

    <!-- <div v-if="loading"> -->
    <div v-if="loading" className="loadingPage">
      <p>Uploading...</p>

      <div className="progressBar">
        <div className="progressBarFill" :style="{ width: `${progressPercent}% !important` }"></div>
      </div>
    </div>

    <!-- <div v-if="imgUrl && progressPercent == 100"> -->
    <div className="success" v-if="progressPercent == 100 && imgUrl">
      <img src="/checkmark.png" alt="" class="checkmark" />
      <h1>Uploaded Successfully!</h1>
      <img :src="imgUrl" alt="" />

      <div className="copy-link">
        <input type="text" className="copy-link-input" :value="imgUrl" readonly />
        <button type="button" class="copy-link-button" @click="copyUrl">Copy Link</button>
      </div>
    </div>
  </main>
</template>

<style>
.checkmark {
  width: 50px !important;
  margin: auto;
}
.copy-link {
  --height: 36px;
  display: flex;
  /* max-width: 250px; */
  background-color: #f6f8fb;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  padding: 2px 2px;
}

.copy-link-input {
  flex-grow: 1;
  padding: 0 8px;
  font-size: 14px;
  border-right: none;
  border: transparent;
  outline: none;
  background-color: transparent;
}

.copy-link:hover {
  background-color: #eee;
}

.copy-link-button {
  /* flex-shrink: 0; */
  /* width: var(--height); */
  height: var(--height);
  display: flex;
  align-items: center;
  padding: 0 15px;
  justify-content: center;
  color: white;
  cursor: pointer;
  background-color: #2f80ed;
  outline: transparent;
  border: transparent;
  border-radius: 8px;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Poppins', sans-serif;
  font-family: 'Ubuntu', sans-serif;
}

h1 {
  color: #4f4f4f;
}

main {
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: #fff;
  gap: 2rem;
  width: 80%;
  margin: auto;
}

.file-info {
  margin-top: 32px;
}

.progressBar {
  background-color: #f2f2f2;
  /* border: 1px solid black; */
  height: 10px;
  width: 100%;
}

.progressBarFill {
  background-color: #2f80ed;
  height: 100%;
  width: 0%;
}

.greyText {
  color: #828282;
}

.uploadContainer {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  justify-content: center;
  align-items: center;
}

.loadingPage {
  width: 50%;
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.loadingPage p {
  font-weight: 500;
}

.success {
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.success img {
  width: 338px;
  border-radius: 12px;
}

.success h1 {
  /* font-size: 18px; */
  text-align: center;
}
</style>
