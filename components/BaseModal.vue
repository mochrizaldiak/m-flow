<template>
  <Transition name="fade">
    <div v-if="modelValue" class="modal-backdrop" @click.self="$emit('update:modelValue', false)">
      <div class="modal-box">
        <header class="modal-header">
          <slot name="header" />
        </header>

        <section class="modal-body">
          <slot />
        </section>

        <footer class="modal-footer">
          <slot name="footer" />
        </footer>
      </div>
    </div>
  </Transition>
</template>

<script setup>
defineProps({
  modelValue: {
    type: Boolean,
    required: true
  }
});

defineEmits(['update:modelValue']);
</script>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.4);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 50;
}

.modal-box {
  background: white;
  border-radius: 12px;
  width: 90%;
  max-width: 360px;
  padding: 20px;
  box-shadow: 0 20px 40px rgba(0,0,0,0.1);
  animation: scaleIn 0.2s ease;
}

.modal-header {
  margin-bottom: 12px;
}

.modal-body {
  margin-bottom: 16px;
}

.modal-footer {
  text-align: right;
}

@keyframes scaleIn {
  0% {
    transform: scale(0.95);
    opacity: 0;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

/* Fade transition */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.2s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>
