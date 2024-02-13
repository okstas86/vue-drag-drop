<template>
	<div class="w-[144px] h-[86px] z-10">
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
const emit = defineEmits(["editItem"])

function handleEdit() {
	emit("editItem")
}
</script>
<style></style>
