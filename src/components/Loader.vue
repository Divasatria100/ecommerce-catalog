<template>
  <div class="spinner-wrapper">
    <div class="spinner-circle"></div>
  </div>
</template>

<script>
export default {
  name: 'LoadingSpinner'
}
</script>

<style scoped>
.spinner-wrapper {
  position: absolute;
  top: 125px;
  left: 50%;
  transform: translateX(-50%);
  width: 120px;
  height: 120px;
}

.spinner-circle {
  width: 100%;
  height: 100%;
  border: 16px solid #f3f3f3;
  border-top-color: #3498db;
  border-radius: 50%;
  animation: rotate-spinner 2s linear infinite;
}

@keyframes rotate-spinner {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
