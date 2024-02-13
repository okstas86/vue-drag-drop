<template>
	<div class="w-full h-full flex p-5 bg-gray-100 overflow-x-auto">
		<div
			v-for="category in categories"
			:key="category.id"
			@drop="onDrop($event, category.id)"
			@dragover.prevent
			@dragenter.prevent
			class="droppable w-[184px] h-full m-1 border-2 border-[#E3E5E8] border-t-0 bg-white rounded-lg flex flex-col"
		>
			<div
				class="rounded-t-lg w-[180px] mb-2"
				:style="getCategoryClass(category.id)"
			>
				<h4
					@click="startEditing(category)"
					v-if="!category.editing"
					class="text-[#1C2530] mb-3 text-center"
				>
					{{ category.title }}
				</h4>
			</div>
			<input
				class="bg-gray-500 text-white mb-3 outline-none text-center"
				v-if="category.editing"
				v-model="category.editedTitle"
				@blur="stopEditing(category)"
				@keyup.enter="stopEditing(category)"
				type="text"
			/>
			<div
				v-for="item in items.filter(x => x.categoryId === category.id)"
				:key="item.id"
				@dragstart="onDragStart($event, item)"
				draggable="true"
				class="draggable relative flex bg-white text-white p-2 mx-4 rounded-md mb-2 w-[154px] border-2 border-[#C4CAD4]"
			>
				<h5
					class="text-[#1C2530] font-normal text-sm overflow-y-auto"
					v-if="!item.editing"
				>
					{{ item.title }}
				</h5>
				<button @click="handleOpenModal(item.id)">
					<img
						v-if="!showModal && !showEditIcon"
						class="absolute w-5 h-5 top-1 right-2"
						src="../assets/icons/more-horizontal.svg"
					/>
				</button>
				<div
					v-if="showEditIcon && showModalId === item.id"
					class="absolute flex flex-col top-0 right-0"
				>
					<button @click="stopEditing(item)">
						<img src="../assets/icons/check.svg" alt="check" />
					</button>
					<button @click="cancelEditing(item)">
						<img src="../assets/icons/x.svg" alt="close" />
					</button>
				</div>
				<ModalEdit
					v-if="showModal && showModalId === item.id"
					class="absolute right-[-165px] top-0"
					@editItem="handleEditItem(item)"
				/>

				<textarea
					class="bg-white w-[110px] text-[#1C2530] focus:outline-none"
					v-if="item.editing"
					placeholder="Введите текст"
					v-model="item.editedTitle"
					@blur="stopEditing(item)"
					@keyup.enter="stopEditing(item)"
					type="text"
					rows="4"
				/>
			</div>
			<button @click="addNewItem(category.id)" class="m-2 text-white">
				<img src="../assets/icons/plus.svg" alt="plus" />
				<p>Добавить</p>
			</button>
		</div>
		<div>
			<button
				@click="addNewCategory"
				class="m-5 bg-gray-500 text-white p-2 rounded-md min-w-[200px]"
			>
				Add another column
			</button>
		</div>
	</div>
</template>

<script setup>
import { ref } from "vue"
import ModalEdit from "./ModalEdit.vue"
const showModal = ref(false)
const showModalId = ref(null)
const showEditIcon = ref(false)

const items = ref([
	{
		id: 0,
		title: "Задача 1",
		categoryId: 0,
		editing: false,
	},
	{
		id: 1,
		title: "Создание тестового задания",
		categoryId: 0,
		editing: false,
	},
	{
		id: 2,
		title: "Доработка меню",
		categoryId: 1,
		editing: false,
	},
])
const categories = ref([
	{
		id: 0,
		title: "На согласовании",
		color: "#FF65DD",
		editing: false,
	},
	{
		id: 1,
		title: "Новые",
		color: "#33A0FF",
		editing: false,
	},
	{
		id: 2,
		title: "В процессе",
		color: "#FFC633",
		editing: false,
	},
	{
		id: 3,
		title: "Готово",
		color: "#22C33D",
		editing: false,
	},
	{
		id: 4,
		title: "Доработать",
		color: "#F53D5C",
		editing: false,
	},
])

let newItemTitle = ""

function handleOpenModal(id) {
	showModal.value = !showModal.value
	showModalId.value = id
}

function handleEditItem(editedItem) {
	showEditIcon.value = true
	showModal.value = false

	const item =
		typeof editedItem === "number"
			? items.find(i => i.id === editedItem)
			: editedItem

	if (item) {
		item.editing = true
		item.editedTitle = item.title
	}
}

function onDragStart(e, item) {
	e.dataTransfer.dropEffect = "move"
	e.dataTransfer.effectAllowed = "move"
	e.dataTransfer.setData("itemId", item.id.toString())
}
function onDrop(e, categoryId) {
	const itemId = parseInt(e.dataTransfer.getData("itemId"))
	items.value = items.value.map(item => {
		if (item.id === itemId) item.categoryId = categoryId
		return item
	})
}
function addNewCategory() {
	const newCategoryId = categories.value.length
	const newCategory = {
		id: newCategoryId,
		title: `New Category`,
	}
	categories.value = [...categories.value, newCategory]
}

function addNewItem(categoryId) {
	const newItemId = items.value.length
	const newItem = {
		id: newItemId,
		title: `Введите текст`,
		categoryId: categoryId,
		editing: false,
		editedTitle: "",
	}
	items.value = [...items.value, newItem]
	newItemTitle = ""
}

function startEditing(item) {
	item.editing = true
	item.editedTitle = item.title
}

function stopEditing(item) {
	item.editing = false
	item.title = item.editedTitle
	showEditIcon.value = false
}

function cancelEditing(item) {
	item.editing = false
	// item.editedTitle = item.title
	showEditIcon.value = false
}

function getCategoryClass(categoryId) {
	const category = categories.value.find(item => item.id === categoryId)
	return category ? { "background-color": category.color } : "default-class"
}
</script>
