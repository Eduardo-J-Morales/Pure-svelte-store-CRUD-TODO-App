<script>
import { onMount } from 'svelte';
import { writable } from 'svelte/store'

function createTodoStore() {
  const { subscribe, update } = writable({
    todo: [],
    filter: 'all'
  });

  return {
    subscribe,
    addTodo: (text) => update(state => {
      const newTodo = { id: Date.now(), text, completed: false };
      return { ...state, todo: [...state.todo, newTodo]}
    }),
    toogleTodo: (id) => update(state => {
      const updatedTodo = state.todo.map(todo => todo.id === id ? {...todo, completed: !todo.completed} : todo
      );
      return { ...state, todo: updatedTodo }
    }),
    deleteTodo: (id) => update(state => {
      const filteredTodo = state.todo.filter(todo => todo.id !== id);
      return { ...state, todo: filteredTodo }
    }),
    setFilter: (filter) => update(state => ({ ...state, filter }))
  }
}

const todoStore = createTodoStore();
let newTodoText = ''

$: filteredTodos = $todoStore.todo.filter(todo => {
  if ($todoStore.filter === 'active') return !todo.completed;
  if ($todoStore.filter === 'completed') return todo.completed;
  return true;
})

$: remainingTodos = $todoStore.todo.filter(todo => !todo.completed).length

function handleSubmit() {
  if (newTodoText.trim() !== '') {
    todoStore.addTodo(newTodoText.trim());
    newTodoText = '';
  }
}
</script>

<main>
 
</main>

<style>

</style>
