<template>
	<div class="home-container">
		<div class="todo-app">
			<div class="header">
				<h1>TODO</h1>
				<img class="icon-sun" src="./assets/images/icon-sun.svg" />
			</div>
			<div class="create-todo">
				<a></a>
				<input v-model="newTodo" placeholder="Create a new todo..." @keyup.enter="addNewTodo" />
			</div>
			<div class="container-todo">
				<div class="list-todos" v-for="(todo, index) in todos" :key="index">
					<a @click="checkTodo(index)">
						<img v-if="todo.completed" src="./assets/images/icon-check.svg" />
					</a>
					<p :class="[{'text-checked': todo.completed}]">{{todo.descript}}</p>
				</div>
				<div v-if="todos.length > 0" class="list-todos-footer" @click="clearCompleted">
					<p>Clear Completed</p>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import './assets/Global.css';
// import TodoCard from './components/TodoCard'
export default {
	name: 'App',
	data() {
			return {
				todos: [],
				newTodo: '',
			}
	},
	components: {
		// TodoCard
	},
	mounted() {
		this.recoveryTodos();
	},
	methods: {
		recoveryTodos() {
			const todosRecovered =  JSON.parse(localStorage.getItem('@todos'))
			if(todosRecovered) {
				this.todos = todosRecovered;
			}
		},
		setOnLocalStorage() {
			localStorage.setItem('@todos', JSON.stringify(this.todos));
		},
		addNewTodo() {
			this.todos.push({descript: this.newTodo, completed: false});
			this.newTodo = '';

			this.setOnLocalStorage();
		},
		checkTodo(index) {
			if(this.todos[index].completed) {
				this.todos[index].completed = false
			} else {
				this.todos[index].completed = true
			}

			this.setOnLocalStorage();
		},
		clearCompleted() {
			this.todos = this.todos.filter(item => item.completed != true);
			this.setOnLocalStorage();
		}

	}
}
</script>

<style>

</style>
