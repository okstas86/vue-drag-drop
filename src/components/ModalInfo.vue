<template>
	<div class="flex w-[400px] min-h-[76px] shadow-md shadow-gray-400">
		<div class="w-4 bg-[#22C33D]"></div>
		<div class="w-[64px] h-[56px] flex items-center justify-center">
			<img class="w-6 h-6" src="../assets/icons/done.svg" alt="done" />
		</div>
		<div class="w-[336px] overflow-y-auto p-1">
			<h4 class="my-1 text-md">{{ actionText }}</h4>
			<p class="text-sm my-1 mb-1 mr-2 overflow-y-auto">{{ title }}</p>
		</div>
	</div>
</template>

<script setup>
import { defineProps } from "vue"

const { title, actionText } = defineProps(["title", "actionText"])
</script>
