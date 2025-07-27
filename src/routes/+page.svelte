<script lang="ts">
	type Todo = {
		text: string;
		done: boolean;
	};

	type Filters = 'all' | 'active' | 'completed';

	let todos = $state<Todo[]>([]);

	let filter = $state<Filters>('all');

	let filteredTodos = $derived(filterTodos());


	$effect(() => {
		const savedTodos = localStorage.getItem('todos')
		savedTodos && (todos = JSON.parse(savedTodos))
	})

	$effect(() => {
		localStorage.setItem('todos', JSON.stringify(todos))
	})


	// $effect(() => {
	// 	console.log(todos);
	// 	//este effect se ejecutara cada vez que se modifique el array todos
	// 	console.log(filter);
	// });

	function addTodo(event: KeyboardEvent) {
		if (event.key !== 'Enter') return;

		const todoEl = event.target as HTMLInputElement;
		const text = todoEl.value;
		const done = false;

		todos = [...todos, { text, done }];
		// esta funcion limpia el input. tambien se puede hacer con un binding
		todoEl.value = '';
	}

	function editTodo(event: Event) {
		const inputEl = event.target as HTMLInputElement;
		const index = +inputEl.dataset.index!;
		//este operador ! es para decirle a typescript que el valor no sera null y ello de + es para convertir el string a un numero

		todos[index].text = inputEl.value;
	}

	function toggleTodo(event: Event) {
		const inputEl = event.target as HTMLInputElement;
		const index = +inputEl.dataset.index!;
		todos[index].done = !todos[index].done;
	}

	function setFilter(newFilter: Filters) {
		filter = newFilter;
	}

	function filterTodos() {
		switch (filter) {
			case 'all':
				return todos;

			case 'active':
				return todos.filter((todo) => !todo.done);

			case 'completed':
				return todos.filter((todo) => todo.done);
			default:
				break;
		}
	}

	function remaining() {
		return todos.filter(todo => !todo.done).length
	}
</script>

<input onkeydown={addTodo} placeholder="Add todo" type="text" class="add-todo-input" />
<!-- este input obtendra la funcion addTodo con la cual se le agregaran los valores a los otros inputs -->

<div class="todos">
	{#each filteredTodos as todo, i}
		<div class:completed={todo.done} class="todo">
			<input oninput={editTodo} data-index={i} value={todo.text} type="text" class="value" />
			<input onchange={toggleTodo} data-index={i} checked={todo.done} type="checkbox" />
		</div>
	{/each}
</div>

<div class="filters">
	<button onclick={() => setFilter('all')}>All</button>
	<button onclick={() => setFilter('active')}>Active</button>
	<button onclick={() => setFilter('completed')}>Completed</button>
</div>

<p class="paragraph">{remaining()} remaining</p>

<style>
	:global(body) {
		min-height: 100vh;
		margin: 0;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
		background-color: rgb(27, 27, 27);
	}

	.add-todo-input {
		font-size: 24px;
		width: 50%;
		padding: 1rem;
		border-radius: 4px;
		background-color: #272627;
		color: white;
		border: none;
		box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
		font-family: monospace;
	}

	.todos {
		display: grid;
		gap: 1rem;
		margin-block-start: 1rem;
	}
	.todo {
		position: relative;
		display: flex;
		align-items: center;
		gap: 1rem;
		transition: opacity: 0.3s;
	}

	.completed {
		opacity: 0.4;
	}

	.value {
		font-size: 24px;
		width: 100%;
		padding: 1rem;
		border-radius: 4px;
		background-color: #272627;
		color: white;
		border: none;
		box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
		font-family: monospace;
	}

	input[type='checkbox'] {
		position: absolute;
		right: 4%;
		top: 48%;
		translate: 0% -50%;
		width: 18px;
		height: 18px;
		accent-color: #444851;
	}

	.filters {
		margin-block: 1rem;
	}

	button {
		font-size: 24px;
		padding: 8px 20px;
		background-color: #454c55;
		color: white;
		border-radius: 4px;
		border: none;
		box-shadow: 2px 0px 5px rgba(0, 0, 0, 0.1);
		margin-left: 4px;
		margin-top: 12px;
		cursor: pointer;
	}
	
	.paragraph {
		font-size: 24px;
		font-family: monospace;
		color: white;
	}
</style>
