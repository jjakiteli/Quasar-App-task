<template>
  <div>
    <q-btn
      color="white"
      text-color="black"
      label="Add Article"
      @click="isDialogOpen = true"
    />
    <q-table
      :rows="articles"
      :columns="[
        { name: 'id', label: 'ID', field: 'id' },
        { name: 'author', label: 'Author', field: 'author' },
        { name: 'title', label: 'Title', field: 'title' },
        { name: 'content', label: 'Content', field: 'content' },
        { name: 'created_at', label: 'Created At', field: 'created_at' },
        { name: 'actions', label: 'Actions', field: 'actions', align: 'left' },
      ]"
      row-key="id"
      separator="horizontal"
    >
      <template v-slot:body-cell-actions="props">
        <q-td class="q-pa-none">
          <div class="row full-width">
            <q-btn
              flat
              icon="edit"
              color="black"
              class="full-height"
              @click="editArticle(props.row)"
            />
            <q-btn
              flat
              icon="delete"
              color="red"
              class="full-height"
              @click="deleteArticle(props.row.id)"
            />
          </div>
        </q-td>
      </template>
    </q-table>

    <q-dialog v-model="isDialogOpen">
      <q-card>
        <q-card-section>
          <q-input v-model="form.author" label="Author" />
          <q-input v-model="form.title" label="Title" />
          <q-input v-model="form.content" label="Content" />
        </q-card-section>
        <q-card-actions>
          <q-btn label="Save" @click="saveArticle" />
          <q-btn label="Cancel" @click="closeDialog" />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useQuasar } from 'quasar';

const $q = useQuasar();
const articles = ref<{ id: number; author: string; title: string; content: string; created_at: string }[]>([]);
const isDialogOpen = ref(false);
const form = ref<{ id: number; author: string; title: string; content: string }>({
  id: 0,
  author: '',
  title: '',
  content: '',
});
const isEditing = ref(false);

//Mock initial data
const fetchArticles = async () => {
  articles.value = [
    {
      id: 1,
      author: 'Author 1',
      title: 'Article 1',
      content: 'Content 1',
      created_at: '2025-02-28',
    },
    {
      id: 2,
      author: 'Author 2',
      title: 'Article 2',
      content: 'Content 2',
      created_at: '2025-03-01',
    },
  ];
};

const saveArticle = () => {
  const newArticle = {
    id: isEditing.value ? form.value.id : articles.value.length + 1,
    author: form.value.author.trim(),
    title: form.value.title.trim(),
    content: form.value.content.trim(),
    created_at: getCurrentDate(),
  };

  if (isEditing.value) {
    const index = articles.value.findIndex((a) => a.id === newArticle.id);
    if (index !== -1) articles.value[index] = newArticle;
  } else {
    articles.value.push(newArticle);
  }
  closeDialog();
  showAlert('Article saved successfully');
};

const getCurrentDate = (): string => {
  const date = new Date();
  const year = date.getFullYear();
  const month = String(date.getMonth() + 1).padStart(2, '0');
  const day = String(date.getDate()).padStart(2, '0');
  return `${year}-${month}-${day}`;
};

const editArticle = (article: { id: number; author: string; title: string; content: string; created_at: string }) => {
  form.value = { ...article };
  isEditing.value = true;
  isDialogOpen.value = true;
};

const deleteArticle = (id: number) => {
  articles.value = articles.value.filter((article) => article.id !== id);
  showAlert('Article deleted successfully');
};

const closeDialog = () => {
  isDialogOpen.value = false;
  form.value = { id: 0, author: '', title: '', content: ''};
  isEditing.value = false;
};

const showAlert = (message: string) => {
  $q.notify({
    type: 'positive',
    message: message,
  });
};

onMounted(fetchArticles);
</script>
