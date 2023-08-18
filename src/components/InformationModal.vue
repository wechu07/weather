<template>
    <Teleport to="body">
        <Transition name="modal-wrapper">
            <div v-show="modalActive" class="absolute w-full bg-black bg-opacity-30 h-screen top-0 left-0 flex justify-center px-8">
                <Transition name="modal-content">
                    <div v-if="modalActive" class="p-4 bg-white self-start mt-32 max-w-screen-md">
                        <slot/>
                        <button @click="$emit('close-modal')" class="text-white mt-8 max-w-screen bg-secondary py-2 px-6">close</button>
                    </div>
                </Transition>
            </div>
        </Transition>
    </Teleport>
</template>

<script setup>
defineEmits(["close-modal"]);
defineProps({
  modalActive: {
    type: Boolean,
    default: false,
  }
})
</script>

<style scoped>
.modal-wrapper-enter-active,
.modal-wrapper-leave-active {
  transition: opacity 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}

.modal-wrapper-enter-from,
.modal-wrapper-leave-to {
  opacity: 0;
}

.modal-content-enter-active {
  transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;
}

.modal-content-leave-active {
  transition: all 0.3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
}

.modal-content-enter-from {
  opacity: 0;
  transform: scale(0.8);
}

.modal-content-leave-to {
  transform: scale(0.8);
}
</style>
