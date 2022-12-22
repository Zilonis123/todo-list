
<script>
    import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';

    const [send, receive] = crossfade({
        fallback(node, params) {
            const style = getComputedStyle(node);
            const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
        }
    })


    let todos = [
        { id: 1, value: "Clean the kitchen", completed: false},
        { id: 2, value: "Go to the gym", completed: false},
        { id: 3, value: "Open the todo app", completed: true},

    ];
    let uid = todos.length + 1;
    function add(input) {
        if (input.value == "") return;

        var todo = {
            id: uid++,
            completed: false,
            value: input.value
        }
        todos = [todo, ...todos];
        input.value = "";
    }

    function remove(todo) {
		todos = todos.filter(t => t !== todo);
	}
</script>


<div class="board">
    <h1 class="title">Todo list</h1>
    <input
    class="add-todo"
    placeholder="Add a todo"
    on:keydown="{event => event.key === 'Enter' && add(event.target)}">
    <div class="left">
        <h2>todo</h2>
        {#each todos.filter(t => !t.completed) as todo (todo.id)}
            <label
            in:receive="{{key: todo.id}}"
            out:send="{{key: todo.id}}"
            animate:flip
        >
            <input type="checkbox" bind:checked={todo.completed}>
            {todo.value}
            <button on:click={() => remove(todo)}>x</button>
        </label>
        {/each}
    </div>
    <div class="right">
        <h2>done</h2>
        {#each todos.filter(t => t.completed) as todo (todo.id)}
            <label
                in:receive="{{key: todo.id}}"
				out:send="{{key: todo.id}}"
				animate:flip
            >
                <input type="checkbox" bind:checked={todo.completed}>
                {todo.value}
                <button on:click={() => remove(todo)}>x</button>
            </label>
        {/each}
    </div>
</div>

<style>
    .board {
        padding: 10px;
        max-width: 36em;
		margin: 0 auto;
    }

    .left, .right {
		float: left;
		width: 50%;
		padding: 0 1em 0 0;
		box-sizing: border-box;
	}

    .title {
        text-align: center;
    }

    .add-todo {
		font-size: 1.4em;
		width: 100%;
		margin: 2em 0 1em 0;
	}

    h2 {
		font-size: 2em;
		font-weight: 200;
		user-select: none;
	}

	label {
		top: 0;
		left: 0;
		display: block;
		font-size: 1em;
		line-height: 1;
		padding: 0.5em;
		margin: 0 auto 0.5em auto;
		border-radius: 2px;
		background-color: #eee;
		user-select: none;
	}

	input { margin: 0 }

	.right label {
		background-color: rgb(100, 240, 142);
	}

	button {
		float: right;
		height: 1em;
		box-sizing: border-box;
		padding: 0 0.5em;
		line-height: 1;
		background-color: transparent;
		border: none;
		color: rgb(170,30,30);
		opacity: 0;
		transition: opacity 0.2s;
	}

	label:hover button {
		opacity: 1;
	}
</style>