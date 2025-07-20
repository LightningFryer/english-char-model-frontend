<script setup>
import { ref } from "vue";

const selectedFile = ref(null);
const imagePreview = ref(null);
const prediction = ref(null);
const loading = ref(false);

function handleFileChange(e) {
  const file = e.target.files[0];
  if (file) {
    selectedFile.value = file;
    imagePreview.value = URL.createObjectURL(file);
  }
}

async function submitImage() {
  const formData = new FormData();
  formData.append("file", selectedFile.value);
  loading.value = true;

  try {
    const res = await fetch(
      "https://pytorch-english-char-model-server.onrender.com/predict",
      {
        method: "POST",
        body: formData,
      }
    );

    const data = await res.json();
    prediction.value = data.prediction;
  } catch (err) {
    console.error(err);
    prediction.value = "Error occurred";
  } finally {
    loading.value = false;
  }
}
</script>

<template>
  <div class="container">
    <h1>Image Classifier</h1>

    <input type="file" accept="image/*" @change="handleFileChange" />
    <button :disabled="!selectedFile" @click="submitImage">Predict</button>

    <div v-if="prediction !== null">
      <h2>Prediction: {{ prediction }}</h2>
    </div>

    <div v-if="loading">
      <h2>Loading...</h2>
    </div>

    <div v-if="imagePreview">
      <img
        :src="imagePreview"
        alt="Preview"
        style="max-width: 300px; margin-top: 1rem"
      />
    </div>
  </div>
</template>

<style>
.container {
  text-align: center;
  padding: 2rem;
  font-family: sans-serif;
}
button {
  margin-top: 10px;
  padding: 8px 16px;
}
</style>
