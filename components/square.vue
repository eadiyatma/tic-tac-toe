<script lang="ts" setup>
defineProps<{
    value: 'X' | 'O' | '';
}>();
</script>
<template>
    <button
        class="rounded-md bg-gray-700 transition-all ease-out duration-150 h-16 w-16 flex items-center justify-center active:bg-gray-600"
        :class="[
            value == 'X' ? '!bg-cyan-600' : '',
            value == 'O' ? '!bg-rose-500' : ''
        ]"
    >
        <span class="">{{ value }}</span>
    </button>
</template>
