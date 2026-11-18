<script lang="ts">
    import { onMount } from 'svelte';

    type Todo = {
        id: number;
        text: string;
        completed: boolean;
    };

    let todoText = $state('');
    let todos = $state<Todo[]>([]);

    // 1. Save todos to LocalStorage
    function saveTodos() {
        localStorage.setItem('todos', JSON.stringify(todos));
    }

    // 2. Retrieve todos from LocalStorage
    function getTodos() {
        const savedTodos = localStorage.getItem('todos');

        if (savedTodos) {
            todos = JSON.parse(savedTodos);
        }
    }

    // 3. Add a new todo
    function addTodo() {
        if (!todoText.trim()) return;

        const newTodo: Todo = {
            id: Date.now(),
            text: todoText.trim(),
            completed: false
        };

        todos.push(newTodo);

        saveTodos();
        todoText = '';
    }

    // 4. Delete an individual todo
    function deleteTodo(id: number) {
        todos = todos.filter(todo => todo.id !== id);

        saveTodos();
    }

    // 5. Mark todo as completed or incomplete
    function toggleTodo(id: number) {
        const todo = todos.find(todo => todo.id === id);

        if (todo) {
            todo.completed = !todo.completed;
        }

        saveTodos();
    }

    // 6. Clear all todos
    function clearTodos() {
        todos = [];

        saveTodos();
    }

    // 7. Retrieve saved todos when the page opens
    onMount(() => {
        getTodos();
    });
</script>

<div class="container">
    <h1>TODO LIST</h1>

    <input
        type="text"
        name="name"
        placeholder="Add todo..."
        bind:value={todoText}
        onkeydown={(event) => event.key === 'Enter' && addTodo()}
    >

    <button onclick={addTodo}>Submit</button>
    <button onclick={clearTodos}>Clear All</button>

    {#each todos as todo (todo.id)}
        <div class="todo">
            <input class="checked"
                type="checkbox"
                checked={todo.completed}
                onchange={() => toggleTodo(todo.id)}
            >

            <span class:completed={todo.completed}>
                {todo.text}
            </span>

            <button class="cancel" onclick={() => deleteTodo(todo.id)}>X</button>
        </div>
    {/each}
</div>

<style>
.container{
    display: flex;
    flex-direction: column;
    align-items: center;
}

input{
    width: 400px;
    height: 40px;
    border-radius: 5px;
    margin: 5px;
}

button{
    width: 400px;
    height: 40px;
    border-radius: 5px;
    margin: 5px;
}

.completed {
    text-decoration: line-through;
}

.todo {
    display: flex;
    align-items: center;
}

.checked{
    width: 20px;
    height: 20px;
}

.cancel{
    background: 0;
    border: 0;
}
</style>
