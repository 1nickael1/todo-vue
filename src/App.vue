<template>
	<div :class="{'light-mode': lightMode}">
		<div class="home-container">
			<div class="todo-app">
				<div class="header">
					<h1>TODO</h1>
					<img @click="lightAdnDarkMode" class="icon-sun" :src="iconSunOrMoon" />
				</div>
				<div class="create-todo">
					<input v-model="newTodo"  placeholder="Create a new todo..." @keyup.enter="addNewTodo" />
				</div>
				<div class="container-all-todo">
					<Container class="container-drag" @drop="onDrop" orientation="vertical">
						<Draggable drag-class="list-todos" class="list-todos" v-for="(todo, index) in todosToShow" :key="index">
							<div style="width: 100%">
								<a :class="[{'icon-checked': todo.completed}]" @click="checkTodo(index)">
									<img v-if="todo.completed" src="./assets/images/icon-check.svg" />
								</a>
								<p :class="[{'text-checked': todo.completed}, {'text-unchecked': !todo.completed}]">{{todo.descript}}</p>
							</div>
							<img @click="removeTodo(index)" src="./assets/images/icon-cross.svg" alt="">
						</Draggable>
					</Container>
					<div v-if="todosOriginal.length > 0" class="list-todos-footer">
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
	</div>
</template>

<script>
import { defineComponent } from '@vue/runtime-core';
import IconSun from './assets/images/icon-sun.svg';
import IconMoon from './assets/images/icon-moon.svg';
import './assets/Global.css';
// import TodoCard from './components/TodoCard'
import { Container, Draggable } from "vue3-smooth-dnd";
export default defineComponent({
		name: 'App',
		data() {
				return {
					todosOriginal: [],
					todosToShow: [],
					newTodo: '',
					buttonActive: 1,
					lightMode: false,
					iconSunOrMoon: IconSun,
				}
		},
		components: {Container, Draggable},
		mounted() {
			this.recoveryTodos();
		},
		methods: {
			recoveryTodos() {
				const todosRecovered =  JSON.parse(localStorage.getItem('@todos'))
				this.lightMode =  JSON.parse(localStorage.getItem('@lightmode'))
				
				if(todosRecovered) {
					this.todosOriginal = todosRecovered;
					this.todosToShow = todosRecovered;
				}

				if(this.lightMode) {
					this.iconSunOrMoon = IconMoon
				} else {
					this.iconSunOrMoon = IconSun;
				}
			},
			setOnLocalStorage() {
				localStorage.setItem('@todos', JSON.stringify(this.todosOriginal));
				localStorage.setItem('@lightmode', JSON.stringify(this.lightMode));
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
				let newList = this.todosOriginal.filter(item => item.completed != true);
				if(!newList) {
					this.buttonActive = 1;
					return;
				}
				this.todosOriginal = newList;
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
			},
			lightAdnDarkMode() {
				this.lightMode = !this.lightMode;
				if(this.lightMode) {
					this.iconSunOrMoon = IconMoon
				} else {
					this.iconSunOrMoon = IconSun;
				}

				this.setOnLocalStorage()
			},
			applyDrag(arr, dragResult) {
				const { removedIndex, addedIndex, payload } = dragResult;

				if (removedIndex === null && addedIndex === null) return arr;
				const result = [...arr];
				let itemToAdd = payload;
				
				if (removedIndex !== null) {
					itemToAdd = result.splice(removedIndex, 1)[0];
				}
				if (addedIndex !== null) {
					result.splice(addedIndex, 0, itemToAdd);
				}
				return result;
			},
			onDrop (dropResult) {
				let newList = this.applyDrag(this.todosOriginal, dropResult);
				
				this.todosToShow = newList;
				this.todosOriginal = newList;
				this.setOnLocalStorage();
			}
	
		}

	}) 
	
</script>

<style>

</style>
