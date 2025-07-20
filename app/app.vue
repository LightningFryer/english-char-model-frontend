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
        // CORS-safe headers
        headers: {
          // Don't set Content-Type manually for FormData!
          Accept: "application/json",
        },
      }
    );

    if (!res.ok) {
      throw new Error(`Server error: ${res.status}`);
    }

    const data = await res.json();
    prediction.value = data.prediction;
  } catch (err) {
    console.error("Error:", err);
    prediction.value = "Error occurred";
  } finally {
    loading.value = false;
  }
}
</script>
