<script setup>
const todos = ref([]);

onMounted(() => {
  const todosFromStorage = localStorage.getItem('chill31-nuxt-todo-data');
  if (todosFromStorage) {
    todos.value = JSON.parse(todosFromStorage);
  }
});

const todoFormVisible = ref(false);
const formTitle = ref('');
const formValue = ref('');

const editModalVisible = ref(false);
const editIndex = ref(0);
const editTitle = ref('');
const editValue = ref('');

function toggleTODOForm() {
  todoFormVisible.value = !todoFormVisible.value;
  formTitle.value = '';
  formValue.value = '';
}

function addTODO() {
  localStorage.setItem('chill31-nuxt-todo-data', JSON.stringify([...todos.value, { title: formTitle.value, content: formValue.value, done: false }]));
  todos.value.push({ title: formTitle.value, content: formValue.value, done: false });
  toggleTODOForm();
}

function deleteTODO(index) {
  todos.value.splice(index, 1);
  localStorage.setItem('chill31-nuxt-todo-data', JSON.stringify(todos.value));
}

function toggleDone(index) {
  todos.value[index].done = !todos.value[index].done;
  localStorage.setItem('chill31-nuxt-todo-data', JSON.stringify(todos.value));
}

function openEditModal(index) {
  editIndex.value = index;
  editModalVisible.value = true;
  editTitle.value = todos.value[index].title;
  editValue.value = todos.value[index].content;
}

function editTodo() {
  todos.value[editIndex.value].title = editTitle.value;
  todos.value[editIndex.value].content = editValue.value;
  localStorage.setItem('chill31-nuxt-todo-data', JSON.stringify(todos.value));
  editModalVisible.value = false;
}

</script>

<template>
  <ThemeButton />
  <div
    class="min-h-screen w-screen overflow-x-hidden flex flex-col gap-12 items-center justify-start dark:bg-slate-900 dark:selection:bg-zinc-600/30 selection:bg-zinc-400/50 bg-slate-200 py-8">
    <h1 class="text-6xl text-center font-serif">TODO</h1>
    <p class="text-center">Built with <code
        class="px-2 py-1 border border-white/25 bg-zinc-500/10 rounded-md">Nuxt.js</code> with the help of <code
        class="px-2 py-1 border border-white/25 bg-zinc-600/10 rounded-md">@nuxt/ui</code></p>

    <UButton class="mt-16 flex items-center justify-center gap-2" icon="i-heroicons-plus" size="xl" color="gray"
      v-if="!todoFormVisible" v-on:click="toggleTODOForm()">Add TODO</UButton>

    <UButton class="mt-16 flex items-center justify-center gap-2" icon="i-heroicons-x-circle" size="xl" color="red"
      v-if="todoFormVisible" v-on:click="toggleTODOForm()">Cancel</UButton>
    <div class="flex flex-col gap-2 items-start justify-start w-[25rem] max-w-[65vw]" v-if="todoFormVisible">

      <label for="form-input">Add TODO</label>
      <UInput id="form-input" class="w-full" color="gray" variant="outline" size="xl" v-model="formTitle"
        placeholder="enter todo title..."></UInput>
      <UTextarea id="form-input" class="w-full" color="gray" variant="outline" size="xl" v-model="formValue"
        placeholder="enter todo content here..."></UTextarea>

      <UButton icon="i-heroicons-plus" class="mt-4 self-end" size="lg" v-on:click="addTODO()"
        :disabled="formValue.length === 0 || formTitle.length === 0">Add</UButton>

    </div>

    <div class="flex w-screen flex-wrap items-start justify-center gap-4">
      <div v-for="todo, k in todos">

        <div
          class="grid auto-cols-auto auto-rows-auto gap-2 w-[20rem] max-w-[calc(98vw-1rem)] py-4 px-2 bg-zinc-300 dark:bg-white/10 selection:bg-white/10 rounded-lg relative rounded-tl-none mt-[2.65rem] border-t-slate-500 border-t-2">
          <div
            class="absolute top-[calc(-2.5rem-2px)] left-0 w-[10rem] max-w-[50%] bg-zinc-300 dark:bg-white/10 rounded-lg rounded-b-none px-4 py-2 text-[1.5rem] flex items-center justify-start gap-2">
            <UIcon v-if="todo.done" name="i-bi-check-lg"></UIcon>
            <span v-if="todo.done" class="text-sm dark:text-slate-400 text-slate-600">(done)</span>
            <UIcon v-if="!todo.done" name="i-bi-clock"></UIcon>
            <span v-if="!todo.done" class="text-sm dark:text-slate-400 text-slate-600">(pending)</span>
          </div>
          <h3 class="text-[1.5rem] font-semibold overflow-hidden max-w-[80%]"
            :class="todo.done ? 'dark:text-slate-400 text-slate-600' : ''">{{ todo.title }}</h3>
          <p class="[line-break:anywhere] h-[15ch] overflow-y-scroll text-sm"
            :class="todo.done ? 'dark:text-slate-400 text-slate-600' : ''">{{ todo.content }}</p>

          <div
            class="flex items-center justify-center gap-2 mt-8 ml-auto px-4 py-2 rounded-md dark:bg-white/10 bg-zinc-400/20">
            <UButton icon="i-bi-check-lg" color="transparent"
              class="dark:text-gray-400 text-gray-700 bg-slate-300 dark:bg-inherit !p-1 focus:outline-current rounded-md hover:bg-white/10"
              size="sm" v-if="!todo.done" v-on:click="toggleDone(k)"></UButton>
            <UButton icon="i-bi-x-lg" color="transparent"
              class="dark:text-gray-400 text-gray-700 bg-slate-300 dark:bg-inherit !p-1 focus:outline-current rounded-md hover:bg-white/10"
              size="sm" v-if="todo.done" v-on:click="toggleDone(k)"></UButton>
            <UButton icon="i-mdi-pencil" color="transparent"
              class="dark:text-primary-400 text-primary-500 dark:bg-inherit bg-slate-300 !p-1 focus:outline-current rounded-md hover:bg-white/10"
              size="sm" v-on:click="openEditModal(k)"></UButton>
            <UButton icon="i-heroicons-trash" color="transparent"
              class="dark:text-red-500 text-red-600 dark:bg-inherit bg-slate-300 !p-1 focus:outline-current rounded-md hover:bg-white/10"
              size="sm" v-on:click="deleteTODO(k)"></UButton>
          </div>
        </div>

      </div>
    </div>
    <UModal v-model="editModalVisible">
      <div class="h-full w-full p-8 flex flex-col items-start justify-start gap-8">
        <h2 class="text-3xl">Edit TODO</h2>
        <div class="flex flex-col items-start justify-start gap-4 w-full">
          <UInput v-model="editTitle" variant="outline" size="xl" class="w-full" placeholder="enter new TODO title"></UInput>
          <UInput v-model="editValue" variant="outline" size="xl" class="w-full" placeholder="enter new TODO value"></UInput>
        </div>
        <UButton icon="i-bi-check-lg" color="sky" size="lg" class="" v-on:click="editTodo()">Save</UButton>
      </div>
    </UModal>
  </div>
</template>
