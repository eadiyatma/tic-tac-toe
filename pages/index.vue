<script setup lang="ts">
import {
    TransitionRoot,
    TransitionChild,
    Dialog,
    DialogPanel,
    DialogTitle
} from '@headlessui/vue';
const board = ref<Array<number>>([-1, -1, -1, -1, -1, -1, -1, -1, -1]);
const turn = ref(0);
const isEnded = ref<boolean>(false);
const isTie = ref<boolean>(false);
const mark = ref<number>(0);

const isOpen = ref(false);

function closeModal() {
    isOpen.value = false;
    reset();
}
function openModal() {
    isOpen.value = true;
}

function markSquare(id: number) {
    if (board.value[id] != -1 || isEnded.value) return;

    // fill mark
    // X = 0 || O = 1
    mark.value = turn.value % 2;
    board.value[id] = mark.value;

    // add turn
    turn.value++;

    console.log(turn.value);

    // check win
    for (let index = 0; index < 3; index++) {
        if (
            // horizontal
            (board.value[0 + index] === mark.value &&
                board.value[3 + index] === mark.value &&
                board.value[6 + index] === mark.value) ||
            // vertical
            (board.value[0 + index * 3] === mark.value &&
                board.value[1 + index * 3] === mark.value &&
                board.value[2 + index * 3] === mark.value)
        ) {
            isOpen.value = true;
            return (isEnded.value = true);
        }
    }

    // check win diagonal
    if (
        (board.value[0] == mark.value &&
            board.value[4] == mark.value &&
            board.value[8] == mark.value) ||
        (board.value[2] == mark.value &&
            board.value[4] == mark.value &&
            board.value[6] == mark.value)
    ) {
        isOpen.value = true;
        return (isEnded.value = true);
    }

    if (turn.value > 8) {
        isEnded.value = true;
        isTie.value = true;
        isOpen.value = true;
        return;
    }
}

function reset() {
    board.value = [-1, -1, -1, -1, -1, -1, -1, -1, -1];
    turn.value = 0;
    isEnded.value = false;
    mark.value = 0;
}
</script>

<template>
    <div class="bg-gray-900 flex justify-center items-center h-screen">
        <div
            class="max-w-xs rounded-xl shadow-md p-8 px-12 bg-gray-800 flex flex-col gap-4"
        >
            <div class="text-white font-semibold text-3xl text-center">
                Tic Tac Toe
            </div>

            <!-- turn section -->
            <div class="flex flex-col gap-2 justify-center items-center">
                <span class="text-xl font-semibold text-white">Turn:</span>
                <Square :value="turn % 2 == 0 ? 'X' : 'O'"></Square>
            </div>

            <!-- row tic tac toe section -->
            <div class="grid grid-cols-3 gap-2">
                <Square
                    v-for="item in 9"
                    :key="item"
                    :value="
                        board[item - 1] != -1
                            ? board[item - 1] > 0
                                ? 'O'
                                : 'X'
                            : ''
                    "
                    @click="markSquare(item - 1)"
                ></Square>
            </div>

            <!-- button reset -->
            <button
                class="rounded w-full px-4 py-2 bg-cyan-600 active:bg-cyan-300 transition-colors duration-150"
                @click="reset"
            >
                Reset
            </button>
        </div>

        <TransitionRoot appear :show="isOpen" as="template">
            <Dialog as="div" @close="closeModal" class="relative z-10">
                <TransitionChild
                    as="template"
                    enter="duration-300 ease-out"
                    enter-from="opacity-0"
                    enter-to="opacity-100"
                    leave="duration-200 ease-in"
                    leave-from="opacity-100"
                    leave-to="opacity-0"
                >
                    <div class="fixed inset-0 bg-black bg-opacity-25" />
                </TransitionChild>

                <div class="fixed inset-0 overflow-y-auto">
                    <div
                        class="flex min-h-full items-center justify-center p-4 text-center"
                    >
                        <TransitionChild
                            as="template"
                            enter="duration-300 ease-out"
                            enter-from="opacity-0 scale-95"
                            enter-to="opacity-100 scale-100"
                            leave="duration-200 ease-in"
                            leave-from="opacity-100 scale-100"
                            leave-to="opacity-0 scale-95"
                        >
                            <DialogPanel
                                class="w-full max-w-xs transform overflow-hidden rounded-2xl p-6 text-left align-middle shadow-xl transition-all bg-gray-800"
                            >
                                <DialogTitle
                                    as="h3"
                                    class="text-lg font-medium leading-6 text-center text-white"
                                >
                                    Game Over
                                </DialogTitle>
                                <div
                                    class="mt-2 flex flex-col justify-center items-center gap-2"
                                >
                                    <p class="text-sm text-white">Winner is</p>
                                    <Square
                                        :value="turn % 2 == 0 ? 'O' : 'X'"
                                    ></Square>
                                </div>

                                <div class="mt-4 flex justify-center">
                                    <button
                                        type="button"
                                        class="inline-flex justify-center rounded-md border border-transparent px-4 py-2 text-sm font-medium bg-cyan-600 active:bg-cyan-300"
                                        @click="closeModal"
                                    >
                                        Play Again
                                    </button>
                                </div>
                            </DialogPanel>
                        </TransitionChild>
                    </div>
                </div>
            </Dialog>
        </TransitionRoot>
    </div>
</template>
