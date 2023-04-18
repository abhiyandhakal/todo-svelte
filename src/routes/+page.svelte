<script lang="ts">
	import { browser } from '$app/environment';
	import { v4 as uuid } from 'uuid';

	let todoValue = '';

	let firstList: { id: string; completed: boolean; todo: string }[] = [];

	if (browser) {
		let todolistStr = localStorage.getItem('todos');

		if (todolistStr !== null) {
			firstList = JSON.parse(todolistStr);
		}
	}

	$: todolist = firstList;
	let isEditing = false;
	let toBeEdited = '';

	const handleSubmit = (e: Event) => {
		e.preventDefault();

		if (!isEditing) {
			todolist.push({ id: uuid(), todo: todoValue, completed: false });
			todolist = todolist;
		} else {
			todolist = todolist.map((item) => {
				if (item.id === toBeEdited) return { ...item, todo: todoValue };
				else return item;
			});

			// todolist = todolist;
		}

		localStorage.setItem('todos', JSON.stringify(todolist));

		todoValue = '';
	};

	const deleteTodo = (id: string) => {
		const newList = todolist.filter((todo) => todo.id !== id);
		todolist = newList;
		firstList = newList;

		localStorage.setItem('todos', JSON.stringify(todolist));
	};
</script>

<svelte:head>
	<title>Todo app</title>
</svelte:head>
<h1>Todo app</h1>

<ul>
	{#each todolist as todo (todo.id)}
		<li>
			<input
				type="checkbox"
				bind:checked={todo.completed}
				on:change={() => {
					localStorage.setItem('todos', JSON.stringify(todolist));
				}}
			/>
			{todo.todo}
			<button on:click={() => deleteTodo(todo.id)}>delete</button>
			<button
				on:click={() => {
					isEditing = true;
					todoValue = todo.todo;
					toBeEdited = todo.id;
				}}>edit</button
			>
		</li>
	{/each}
</ul>

<form on:submit={handleSubmit}>
	<button type="submit">+</button>
	<input bind:value={todoValue} type="text" />
</form>
