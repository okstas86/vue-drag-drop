<template>
	<div class="w-full h-[100vh] bg-white flex p-5 overflow-x-auto">
		<div
			v-for="category in categories"
			:key="category.id"
			@drop="onDrop($event, category.id)"
			@dragover.prevent
			@dragenter.prevent
			class="droppable flex items-center w-[184px] h-full m-1 border-2 border-[#E3E5E8] border-t-0 bg-[#F7F7F7] rounded-lg flex flex-col"
		>
			<div
				class="rounded-t-lg w-[184px] mb-2 bg-gray-300"
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
						v-if="!showModalEdit && !showEditIcon"
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
					v-if="showModalEdit && showModalId === item.id"
					class="absolute right-[-165px] top-0"
					@editItem="handleEditItem(item)"
					@onRemove="confirmRemove(item.id)"
				/>

				<textarea
					class="bg-white w-[110px] text-[#1C2530] text-sm focus:outline-none placeholder:text-[#86949E]"
					v-if="item.editing"
					placeholder="Введите текст"
					v-model="item.editedTitle"
					@blur="stopEditing(item)"
					@keyup.enter="stopEditing(item)"
					type="text"
					rows="4"
				/>
			</div>

			<div>
				<button
					v-if="category.showAddButton"
					@click="addNewItem(category.id)"
					class="flex mt-2 ml-[-60px]"
				>
					<img src="../assets/icons/plus.svg" alt="plus" class="ml-2" />
					<p class="text-[#3D86F4] text-sm font-normal">Добавить</p>
				</button>
			</div>
		</div>
		<div>
			<button
				@click="addNewCategory"
				class="flex m-5 bg-white text-white p-2 rounded-md min-w-[200px]"
			>
				<img class="ml-2" src="../assets/icons/plus.svg" alt="plus" />
				<p class="text-[#3D86F4] text-sm font-normal">Добавить колонку</p>
			</button>
		</div>
		<div
			class="absolute inset-0 flex items-center justify-center w-full h-[100vh] bg-black opacity-60"
			v-if="openModalRemove"
		></div>
		<div
			v-if="openModalRemove"
			class="absolute inset-0 flex items-center justify-center w-full h-[100vh]"
		>
			<div
				class="relative w-[500px] h-[196px] bg-white opacity-100 p-[30px] z-100"
			>
				<h2 class="text-2xl ml-5 mb-2">Удалить задачу?</h2>
				<p class="text-sm font-normal ml-5 mb-7">{{ removeItem.title }}</p>
				<div class="flex mx-[20px] gap-5">
					<button
						class="w-[202px] h-[36px] border rounded-md"
						@click="deleteItem(removeItem.id)"
					>
						Удалить
					</button>
					<button
						class="w-[202px] h-[36px] border rounded-md"
						@click="cancelDelete"
					>
						Отменить
					</button>
					<button
						@click="cancelDelete"
						class="absolute flex items-center justify-center right-5 top-5"
					>
						<img class="" src="../assets/icons/close.svg" alt="close" />
					</button>
				</div>
			</div>
		</div>
		<ModalInfo
			v-if="showModalInfo"
			:title="newItemTitle"
			class="absolute bottom-10 right-10"
		/>
	</div>
</template>

<script setup>
import { ref } from "vue"
import ModalEdit from "./ModalEdit.vue"
import ModalInfo from "./ModalInfo.vue"

const showModalEdit = ref(false)
const showModalId = ref(null)
const showEditIcon = ref(false)
const showAddButton = ref(true)
const openModalRemove = ref(false)
const showModalInfo = ref(false)
const action = ""

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
		showAddButton: true,
	},
	{
		id: 1,
		title: "Новые",
		color: "#33A0FF",
		editing: false,
		showAddButton: true,
	},
	{
		id: 2,
		title: "В процессе",
		color: "#FFC633",
		editing: false,
		showAddButton: true,
	},
	{
		id: 3,
		title: "Готово",
		color: "#22C33D",
		editing: false,
		showAddButton: true,
	},
	{
		id: 4,
		title: "Доработать",
		color: "#F53D5C",
		editing: false,
		showAddButton: true,
	},
])

let newItemTitle = ""
let removeItem = {}

function handleOpenModal(id) {
	showModalEdit.value = !showModalEdit.value
	showModalId.value = id
}

function handleEditItem(editedItem) {
	showEditIcon.value = true
	showModalEdit.value = false

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
	showModalInfo.value = true
	setTimeout(() => {
		showModalInfo.value = false
	}, 2000)

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

function updateShowAddButton(categoryId, value) {
	const categoryIndex = categories.value.findIndex(
		category => category.id === categoryId
	)
	if (categoryIndex !== -1) {
		categories.value[categoryIndex].showAddButton = value
	}
}

function addNewItem(categoryId) {
	const newItemId = items.value.length

	const newItem = {
		id: newItemId,
		title: `Введите текст`,
		categoryId: categoryId,
		editing: true,
		editedTitle: "",
	}

	items.value = [...items.value, newItem]

	showModalId.value = newItemId
	newItemTitle = newItem.title

	showEditIcon.value = true

	updateShowAddButton(categoryId, false)

	// newItemTitle = ""
	if (newItem.editing === false) {
		showModalInfo.value = true
		setTimeout(() => {
			showModalInfo.value = false
		}, 2000)
	}
}

function startEditing(item) {
	item.editing = true
	item.editedTitle = item.title
}

function stopEditing(item) {
	item.editing = false
	item.title = item.editedTitle
	newItemTitle = item.title

	showEditIcon.value = false
	updateShowAddButton(item.categoryId, true)
	showModalInfo.value = true
	setTimeout(() => {
		showModalInfo.value = false
	}, 2000)
}

function cancelEditing(item) {
	item.editing = false
	showEditIcon.value = false
	updateShowAddButton(item.categoryId, true)
	showModalInfo.value = true
	setTimeout(() => {
		showModalInfo.value = false
	}, 2000)
}

function confirmRemove(itemId) {
	showModalEdit.value = false
	openModalRemove.value = true
	const deletedItem = items.value.find(item => item.id === itemId)
	removeItem = deletedItem
}

function deleteItem(removeId) {
	items.value = items.value.filter(item => item.id !== removeId)
	openModalRemove.value = false
	showModalInfo.value = true
	setTimeout(() => {
		showModalInfo.value = false
	}, 2000)
}
function cancelDelete() {
	openModalRemove.value = false
}

function getCategoryClass(categoryId) {
	const category = categories.value.find(item => item.id === categoryId)
	return category ? { "background-color": category.color } : ""
}
</script>
