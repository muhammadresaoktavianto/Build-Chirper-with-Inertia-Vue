<template>
  <div class="container">
    <h1>Upload Image</h1>
    <form @submit.prevent="uploadImage">
      <input type="file" @change="handleFileUpload" />
      <button type="submit">Upload</button>
    </form>
    <p v-if="progress !== null">Progress: {{ progress }}%</p>
    <img v-if="imageUrl" :src="imageUrl" alt="Uploaded Image" style="margin-top: 20px;" />
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const file = ref(null);
    const progress = ref(null);
    const imageUrl = ref(null);

    // Menangani pemilihan file
    const handleFileUpload = (event) => {
      file.value = event.target.files[0];
    };

    // Mengunggah gambar
    const uploadImage = async () => {
      if (!file.value) return;

      const formData = new FormData();
      formData.append('image', file.value);

      // Menambahkan CSRF token ke dalam FormData
      formData.append('_token', document.querySelector('meta[name="csrf-token"]').getAttribute('content'));

      try {
        const response = await fetch('/upload', {
          method: 'POST',
          body: formData,
        });

        if (!response.ok) {
          throw new Error('Upload failed');
        }

        // Mengubah respons JSON
        const data = await response.json();
        if (data.success) {
          imageUrl.value = data.imageUrl;
          file.value = null; // Reset file setelah upload berhasil
        } else {
          alert('Upload failed');
        }
      } catch (error) {
        console.error('Upload error:', error);
      }
    };

    return { handleFileUpload, uploadImage, progress, imageUrl };
  },
};
</script>

