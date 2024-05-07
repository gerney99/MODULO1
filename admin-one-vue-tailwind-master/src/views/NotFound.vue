<template>
  <div class="flex items-center justify-center h-screen bg-gradient-to-tr from-pink-400 via-red-500 to-yellow-500">
    <div class="text-center text-white">
      <svg class="w-20 h-20 inline-block mb-6 animate-bounce" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
      </svg>
      <h1 class="text-4xl font-bold mb-4">¡Ups! Algo salió mal</h1>
      <p class="text-lg mb-8">La página que estás buscando no se encuentra.</p>
      <router-link to="/" class="bg-white text-red-600 hover:bg-red-200 text-lg font-semibold py-2 px-4 rounded-full inline-block transition duration-300 ease-in-out">Volver al inicio</router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: 'NotFound'
}
</script>

<style scoped>
/* Estilos específicos para este componente */
.animate-bounce {
  animation: bounce 3s infinite;
}

@keyframes bounce {
  0%, 20%, 50%, 80%, 100% {
    transform: translateY(0);
    transform: rotate(0deg);
  }
  40% {
    transform: translateY(-20px);
  }
  60% {
    transform: rotate(360deg);
  }
}
</style>

