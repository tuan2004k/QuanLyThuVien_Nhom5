<template>
  <v-container>
    <!-- Nút thêm sách với màu xanh dương và icon -->
    <v-btn color="primary" @click="openDialog" class="mb-4" elevation="2">
      <v-icon left>mdi-book-plus</v-icon>
      Thêm sách
    </v-btn>
    
    <!-- Dialog để thêm sách -->
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card elevation="10">
        <v-card-title class="headline" color="blue">Thông tin sách</v-card-title>
        <v-card-text>
          <v-form ref="bookForm" v-model="formValid">
            <v-row>
              <v-col cols="12" sm="6">
                <v-text-field label="Tên sách" v-model="book.title" required outlined color="primary"></v-text-field>
              </v-col>
              <v-col cols="12" sm="6">
                <v-text-field label="Tác giả" v-model="book.author" required outlined color="primary"></v-text-field>
              </v-col>
              <v-col cols="12" sm="6">
                <v-text-field label="Năm xuất bản" v-model="book.year" required outlined color="primary"></v-text-field>
              </v-col>
              <v-col cols="12" sm="6">
                <v-text-field label="Thể loại" v-model="book.genre" required outlined color="primary"></v-text-field>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="primary" @click="luusach">Lưu</v-btn>
          <v-btn color="secondary" @click="closeDialog">Hủy</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Hiển thị danh sách sách với các nút Sửa và Xóa -->
    <v-data-table :headers="headers" :items="books" item-key="id">
      <template v-slot:item.actions="{ item }">
        <v-btn color="blue" @click="suasach(item)" class="mx-2" elevation="2">Sửa</v-btn>
        <v-btn color="#ffff" @click="xoasach(item.id)" class="mx-2" elevation="2">Xóa</v-btn>
      </template>
    </v-data-table>

    <!-- Dropdown chọn thể loại sách để xóa -->
    <v-select
      v-model="selectedGenre"
      :items="genres"
      label="Chọn thể loại sách để xóa"
      @change="deleteByGenre"
      color="primary"
      class="mt-4"
      outlined
    ></v-select>
  </v-container>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';

export default defineComponent({
  setup() {
    const dialog = ref(false);
    const formValid = ref(false);
    const bookForm = ref<any>(null);
    const book = ref<{ title: string; author: string; year: string; genre: string; id?: number }>({
      title: '',
      author: '',
      year: '',
      genre: '',
    });
    const books = ref<any[]>(JSON.parse(localStorage.getItem('books') || '[]'));
    const selectedGenre = ref<string>('');

    const genres = computed(() => {
      return [...new Set(books.value.map((book: any) => book.genre))];
    });

    const headers = [
      { text: 'Tên sách', value: 'title' },
      { text: 'Tác giả', value: 'author' },
      { text: 'Năm xuất bản', value: 'year' },
      { text: 'Thể loại', value: 'genre' },
      { text: 'Actions', value: 'actions', sortable: false },
    ];

    const openDialog = () => {
      book.value = { title: '', author: '', year: '', genre: '' };
      dialog.value = true;
    };

    const closeDialog = () => {
      dialog.value = false;
    };

    const luusach = () => {
      if (!bookForm.value?.validate()) return;

      if (book.value.id) {
        const index = books.value.findIndex((b: any) => b.id === book.value.id);
        if (index !== -1) {
          books.value[index] = { ...book.value };
        }
      } else {
        const newBook = { ...book.value, id: Date.now() };
        books.value.push(newBook);
      }
      localStorage.setItem('books', JSON.stringify(books.value));
      closeDialog();
    };

    const suasach = (item: any) => {
      book.value = { ...item };
      dialog.value = true;
    };

    const xoasach = (id: number) => {
      books.value = books.value.filter((book: any) => book.id !== id);
      localStorage.setItem('books', JSON.stringify(books.value));
    };

    const deleteByGenre = () => {
      if (selectedGenre.value) {
        books.value = books.value.filter((book: any) => book.genre !== selectedGenre.value);
        localStorage.setItem('books', JSON.stringify(books.value));
      }
    };

    return {
      dialog,
      formValid,
      bookForm,
      book,
      books,
      headers,
      openDialog,
      closeDialog,
      luusach,
      suasach,
      xoasach,
      selectedGenre,
      genres,
      deleteByGenre,
    };
  },
});
</script>
<style scoped>
.dialog{

}
</style>