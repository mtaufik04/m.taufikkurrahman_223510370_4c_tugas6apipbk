<template>
  <div class="root">
    <div>
      <form @submit.prevent="save" class="form-container">
        <input type="text" v-model="form.title" placeholder="Masukkan judul" /><br />
        <textarea v-model="form.content" placeholder="Masukkan isi konten"></textarea><br />
        <button type="submit">Simpan</button>
      </form>
      <table class="article-table">
        <thead>
          <tr>
            <th>Judul</th>
            <th>Isi Konten</th>
            <th>Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="article in articles" :key="article.id">
            <td>{{ article.title }}</td>
            <td>{{ article.content }}</td>
            <td>
              <button @click="edit(article)">Edit</button>
              <button @click="remove(article.id)">Hapus</button>
            </td>
          </tr>
        </tbody>
      </table>
      <button @click="load" class="load-button">Muat</button>
    </div>
    <footer class="footer">
      <p>Hak Cipta &copy; 2024 - M.TAUFIK KURRAHMAN</p>
    </footer>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: '',
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('/db.json');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';

        const { id, ...formDataWithoutId } = form;

        const response = await axios[method](url, formDataWithoutId);

        if (response.status === 201 || response.status === 200) {
          load();
          resetForm();
        }
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function remove(id) {
      try {
        if (!id) {
          console.error('Invalid article ID');
          return;
        }
        const response = await axios.delete(`http://localhost:3000/articles/${id}`);
        if (response.status === 200) {
          articles.value = articles.value.filter((article) => article.id !== id);
        } else {
          console.error('Error deleting article:', response.statusText);
        }
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    function resetForm() {
      form.id = null;
      form.title = '';
      form.content = '';
    }

    onMounted(load);

    return { form, articles, save, edit, remove };
  },
};
</script>

<style scoped>
.root {
  background-image: url('./assets/gambar1.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  min-height: 100vh;
  padding: 20px;
}

.form-container {
  margin-bottom: 20px;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.1);
}

.form-container input[type="text"],
.form-container textarea {
  width: calc(50% - 22px);
  padding: 10px;
  margin-bottom: 15px;
  border: 1px solid #ccc;
  border-radius: 6px;
  box-sizing: border-box;
  font-size: 14px;
}

.form-container button[type="submit"] {
  width: 100px;
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  margin-left: 600px;
}

.article-table {
  width: calc(50% - 20px);
  border-collapse: collapse;
  margin-bottom: 20px;
  box-shadow: 50px 50px 50px 50px rgba(242, 4, 4, 0.2); 
  border-radius: 10px;
}

.article-table th,
.article-table td {
  padding: 12px;
  border: 1px solid #ddd;
  text-align: center;
  color: white;
}

.article-table th {
  background-color: #0d0d0d;
}

.article-table td {
  background-color: transparent;
}

.article-table button {
  padding: 8px 10px;
  margin-right: 5px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
}

.article-table button:hover {
  background-color: #0056b3;
}

.load-button {
  width: 100px;
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  margin-top: 20px;
  font-size: 14px;
  margin-left: 600px;
}

.footer {
  text-align: center;
  margin-top: 150px;
}

.footer p {
  font-size: 14px;
  color: #fff;
}
</style>