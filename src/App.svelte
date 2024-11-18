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
      const updatedTodo = state.todo.map(todo => {
        
      })
    })
  }
}
</script>

<main>
 
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
  .read-the-docs {
    color: #888;
  }
</style>
