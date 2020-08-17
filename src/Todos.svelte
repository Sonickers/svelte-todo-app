<script>
  import TodoItem from "./TodoItem.svelte";
  import { time } from './stores.js'

  let newTodoTitle = "";
  let currentFilter = "all";
  let nextId = 4;
  let todos = [];
  const formatter = new Intl.DateTimeFormat("en", {
    hour12: true,
    hour: "numeric",
    minute: "2-digit",
    second: "2-digit",
  });

  $: todosRemaining = filteredTodos.filter((todo) => !todo.completed).length;
  $: filteredTodos =
    currentFilter === "all"
      ? todos
      : currentFilter === "completed"
      ? todos.filter((todo) => todo.completed)
      : todos.filter((todo) => !todo.completed);

  function addTodo(event) {
    if (event.key === "Enter") {
      todos = [
        ...todos,
        {
          id: nextId,
          compelted: false,
          title: newTodoTitle,
        },
      ];

      nextId = nextId + 1;
      newTodoTitle = "";
    }
  }
  function checkAllTodos(event) {
    todos.forEach((todo) => (todo.completed = event.target.checked));
    todos = todos;
  }
  function updateFilter(newFilter) {
    currentFilter = newFilter;
  }
  function clearCompleted() {
    todos = todos.filter((todo) => !todo.completed);
  }
  function handleDeleteTodo(event) {
    todos = todos.filter((todo) => todo.id !== event.detail.id);
  }
  function handleToggleComplete(event) {
    const todoIndex = todos.findIndex((todo) => todo.id === event.detail.id);
    const updatedTodo = {
      ...todos[todoIndex],
      completed: !todos[todoIndex].completed,
    };
    todos = [
      ...todos.slice(0, todoIndex),
      updatedTodo,
      ...todos.slice(todoIndex + 1),
    ];
  }
</script>

<style>
  .container {
    max-width: 800px;
    margin: 10px auto;
  }
  .title-logo-container {
    padding: 35px 0;
  }
  .logo {
    display: block;
    margin-left: 30%;
    margin-top: 30%;
    height: 50%;
  }
  .todo-input {
    width: 100%;
    padding: 10px, 20px;
    font-size: 18px;
    margin-bottom: 20px;
    border-radius: 8px;
  }
  .inner-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    padding-top: 30px;
    margin-bottom: 13px;
  }
  .inner-container-input {
    margin-right: 12px;
  }
  .time-text {
    color: #59646F;
  }
  button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    border-radius: 8px;
    border-color: transparent;
  }
  button:hover {
    background: #faf1cf;
  }
  button:focus {
    outline: none;
  }
  .active {
    background: #faf1cf;
  }
  .circle {
    height: 100px;
    width: 100px;
    background-color: #bbb;
    border-radius: 50%;
    display: inline-block;
    text-align: center;
  }
</style>

<div class="container">
  <div class="title-logo-container">
    <div class="circle">
      <img src={'/img/strategic-plan.svg'} alt="svelte logo" class="logo" />
    </div>
    <h1 class="time-text">The time is {formatter.format($time)}</h1>
  </div>
  <input
    type="text"
    class="todo-input"
    placeholder="Insert todo item ..."
    bind:value={newTodoTitle}
    on:keydown={addTodo} />
  {#each filteredTodos as todo}
    <div class="todo-item">
      <TodoItem
        {...todo}
        on:deleteTodo={handleDeleteTodo}
        on:toggleComplete={handleToggleComplete} />
    </div>
  {/each}
  <div class="inner-container">
    <div>
      <input
        class="inner-container-input"
        type="checkbox"
        on:change={checkAllTodos} />
      All Done!
    </div>
    <div>{todosRemaining} items left</div>
  </div>
  <div class="inner-container">
    <div>
      <button
        on:click={() => updateFilter('all')}
        class:active={currentFilter === 'all'}>
        All
      </button>
      <button
        on:click={() => updateFilter('active')}
        class:active={currentFilter === 'active'}>
        Active
      </button>
      <button
        on:click={() => updateFilter('completed')}
        class:active={currentFilter === 'completed'}>
        Completed
      </button>
    </div>
    <div>
      <button on:click={clearCompleted}>Clear Completed</button>
    </div>
  </div>
</div>
