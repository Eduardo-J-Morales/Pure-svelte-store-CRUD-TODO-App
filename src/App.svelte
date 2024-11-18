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

onMount(()=> null)
</script>

<main>
 <div class="todo-app">
  <div class="todo-container">
    <h1>Svelte Todo</h1>
    
    <form on:submit|preventDefault={handleSubmit}>
      <input 
      type="text" 
      bind:value={newTodoText} 
      placeholder="What needs tod be done?"
      />
      <button type="submit">Add</button>
    </form>

    <ul>
      {#each filteredTodos as todo (todo.id)}
        <li class:completed={todo.completed}>
        <div class="todo-item">
          <input 
          type="checkbox"
          checked={todo.completed}
          on:change={() => todoStore.toggleTodo(todo.id)}
          id={`todo-${todo.id}`}
          />
          <label for={`todo-${todo.id}`}>{todo.text}</label>
        </div>
        <button
        on:click={() => todoStore.deleteTodo(todo.id)}
        class="delete-btn"
        aria-label="Delete Todo"
        >x</button>
        </li>
      {/each}
    </ul>
  </div>
 </div>
</main>

<style>

</style>
