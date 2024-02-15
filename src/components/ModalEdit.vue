<template>
	<div class="absolute left-[165px] top-0 z-50 w-[144px] h-[86px]">
		<img
			style="stroke: #fff"
			class="text-red-300 w-6"
			src="../assets/icons/more-horizontal.svg"
			alt="dots"
		/>
		<div class="w-[144px] h-[76px] bg-white shadow-md shadow-gray-500 py-2">
			<button
				@click="handleEdit"
				class="h-[30px] w-[144px] bg-white hover:bg-[#E1F1FF] text-sm font-normal flex items-center"
			>
				<img class="w-5 h-5 mx-2" src="../assets/icons/edit-3.svg" alt="edit" />
				<p>Редактировать</p>
			</button>

			<button
				@click="remove"
				class="h-[30px] w-[144px] bg-white hover:bg-[#E1F1FF] text-sm font-normal flex items-center"
			>
				<img class="w-5 h-5 mx-2" src="../assets/icons/trash.svg" alt="trash" />
				<p>Удалить</p>
			</button>
		</div>
	</div>
</template>

<script setup>
import { defineEmits } from "vue"
const emit = defineEmits(["editItem", "onRemove"])

function handleEdit() {
	emit("editItem")
}
function remove() {
	emit("onRemove")
}
</script>
