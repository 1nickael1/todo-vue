<template>
	<div class="home-container">
		<div class="todo-app">
			<div class="header">
				<h1>TODO</h1>
				<img class="icon-sun" src="./assets/images/icon-sun.svg" />
			</div>
			<div class="create-todo">
				<input v-model="newTodo"  placeholder="Create a new todo..." @keyup.enter="addNewTodo" />
			</div>
			<div class="container-todo">
				<div class="list-todos" v-for="(todo, index) in todosToShow" :key="index">
					<div style="width: 100%">
						<a :class="[{'icon-checked': todo.completed}]" @click="checkTodo(index)">
							<img v-if="todo.completed" src="./assets/images/icon-check.svg" />
						</a>
						<p :class="[{'text-checked': todo.completed}, {'text-unchecked': !todo.completed}]">{{todo.descript}}</p>
					</div>
					<img @click="removeTodo(index)" src="./assets/images/icon-cross.svg" alt="">
				</div>
				<div v-if="todosOriginal.length > 0" class="list-todos-footer" :class="[{'full-border-radius': todosToShow.length < 1}]">
					<p>{{todosOriginal.length}} items left</p>
					<div class="buttons-footer">
						<p @click="listAllTodos" :class="[{'buttons-footer-active': buttonActive === 1}]">All</p>
						<p @click="listActiveTodos" :class="[{'buttons-footer-active': buttonActive === 2}]">Active</p>
						<p @click="listCompletedTodos" :class="[{'buttons-footer-active': buttonActive === 3}]">Completed</p>
					</div>
					<p @click="clearCompleted">Clear Completed</p>
				</div>
			</div>
			<div v-if="todosOriginal.length > 0" class="buttons-footer-mobile">
				<p @click="listAllTodos" :class="[{'buttons-footer-active': buttonActive === 1}]">All</p>
				<p @click="listActiveTodos" :class="[{'buttons-footer-active': buttonActive === 2}]">Active</p>
				<p @click="listCompletedTodos" :class="[{'buttons-footer-active': buttonActive === 3}]">Completed</p>
			</div>
		</div>
	</div>
</template>

<script>
import { defineComponent } from '@vue/runtime-core';
import './assets/Global.css';
// import TodoCard from './components/TodoCard'
export default defineComponent({
		name: 'App',
		data() {
				return {
					todosOriginal: [],
					todosToShow: [],
					newTodo: '',
					buttonActive: 1,
				}
		},
		mounted() {
			this.recoveryTodos();
		},
		methods: {
			recoveryTodos() {
				const todosRecovered =  JSON.parse(localStorage.getItem('@todos'))
				if(todosRecovered) {
					this.todosOriginal = todosRecovered;
					this.todosToShow = todosRecovered;
				}
			},
			setOnLocalStorage() {
				localStorage.setItem('@todos', JSON.stringify(this.todosOriginal));
				this.todosToShow = this.todosOriginal
			},
			addNewTodo() {
				if(this.newTodo.trim().length > 0) {
					this.todosOriginal.push({descript: this.newTodo, completed: false});
					this.newTodo = '';
		
					this.setOnLocalStorage();
				}
			},
			checkTodo(index) {
				if(this.todosOriginal[index].completed) {
					this.todosOriginal[index].completed = false
				} else {
					this.todosOriginal[index].completed = true
					
				}
				this.setOnLocalStorage();
			},
			clearCompleted() {
				this.todosOriginal = this.todosOriginal.filter(item => item.completed != true);
				this.setOnLocalStorage();
			},
			removeTodo(indexTodo) {
				this.todosOriginal = this.todosOriginal.filter((item, index) => index != indexTodo);
				this.setOnLocalStorage();
			},
			listAllTodos() {
				this.buttonActive = 1;
				this.todosToShow =  this.todosOriginal;
			},
			listActiveTodos() {
				this.buttonActive = 2;
				this.todosToShow = this.todosOriginal.filter(item => item.completed === false);
			},
			listCompletedTodos() {
				this.buttonActive = 3;
				this.todosToShow = this.todosOriginal.filter(item => item.completed === true);
			}
	
		}

	}) 
	
</script>

<style>

</style>
