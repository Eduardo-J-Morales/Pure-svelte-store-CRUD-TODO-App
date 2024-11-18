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
    <div class="todo-footer">
      <span>{remainingTodos} item{remainingTodos === 1 ? 's': '' } left</span>
      <div class="filters">
        <button
        class:active={$todoStore.filter === 'all'}
        on:click={() => todoStore.setFilter('all')}
        >All</button>

        <button
        class:active={$todoStore.filter === 'active'}
        on:click={() => todoStore.setFilter('active')}
        >Active</button>

        <button
        class:active={$todoStore.filter === 'completed'}
        on:click={() => todoStore.setFilter('completed')}
        >Completed</button>

      </div>
    </div>
  </div>
 </div>
</main>

<style>

:global(body) {
  font-family: Arial, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  margin: 0;
  padding: 0;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.todo-app {
  width: 100%;
  max-width: 500px;
  margin: 2rem auto;
  background: white;
  border-radius: 10px;
  box-shadow: 0 0 20px rgba(0,0,0, 0.1);
  overflow: hidden;
}

.todo-container {
  padding: 2rem;
}
</style>
