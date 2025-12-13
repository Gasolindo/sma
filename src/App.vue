<template>
  <div class="min-h-screen bg-gradient-to-r from-pink-600 via-purple-600 to-indigo-600 animate-gradient-x text-white flex items-center justify-center p-6">
    <div class="w-full max-w-5xl bg-black/40 backdrop-blur-xl rounded-[2rem] shadow-2xl p-10 relative overflow-hidden">

      <!-- Floating shapes -->
      <div class="absolute w-40 h-40 bg-emerald-400/30 rounded-full top-[-50px] left-[-50px] animate-float"></div>
      <div class="absolute w-56 h-56 bg-pink-500/30 rounded-full bottom-[-80px] right-[-80px] animate-float-slow"></div>

      <!-- Header -->
      <div class="text-center mb-12">
        <h1 class="text-6xl font-extrabold tracking-widest neon-text">S M A</h1>
        <p class="mt-3 text-xl text-pink-200">Sistema Multiagente • Grafos • Extração de Informação</p>
      </div>

      <!-- Action zone -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
        <div class="bg-white/10 p-6 rounded-2xl hover:scale-105 transition transform">
          <label class="block mb-2 text-emerald-300 font-semibold">📄 Input Path</label>
          <input v-model="inputPath" type="text"
            class="w-full px-4 py-3 rounded-xl bg-black/40 border border-emerald-400 focus:outline-none focus:ring-2 focus:ring-emerald-400"
            placeholder="C:\\Users\\usuario\\arquivo.txt" />
        </div>

        <div class="bg-white/10 p-6 rounded-2xl hover:scale-105 transition transform">
          <label class="block mb-2 text-pink-300 font-semibold">📁 Output Path</label>
          <input v-model="outputPath" type="text"
            class="w-full px-4 py-3 rounded-xl bg-black/40 border border-pink-400 focus:outline-none focus:ring-2 focus:ring-pink-400"
            placeholder="C:\\Users\\usuario\\resultado" />
        </div>
      </div>

      <!-- CTA -->
      <div class="text-center mt-12">
        <button @click="iniciarProcessamento" :disabled="loading || !inputPath || !outputPath"
          class="px-14 py-5 rounded-full text-xl font-bold bg-gradient-to-r from-emerald-400 via-cyan-400 to-indigo-400 text-black shadow-lg hover:scale-110 transition-all animate-pulse">
          {{ loading ? '⚙️ Executando SMA...' : '🚀 Ativar SMA' }}
        </button>
      </div>

      <!-- Result -->
      <transition name="fade">
        <div v-if="resultado" class="mt-14 grid grid-cols-1 md:grid-cols-2 gap-6">
          <div class="bg-black/50 p-6 rounded-2xl border border-emerald-400">
            <p class="text-emerald-300">Status</p>
            <p class="text-3xl font-bold">{{ resultado.status }}</p>
          </div>
          <div class="bg-black/50 p-6 rounded-2xl border border-pink-400">
            <p class="text-pink-300">Arquivos Gerados</p>
            <p class="text-3xl font-bold">{{ resultado.qtd_arquivos }}</p>
          </div>
        </div>
      </transition>

      <footer class="mt-14 text-center text-sm text-white/70">
        SMA • Interface Interativa • Vue 3
      </footer>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const inputPath = ref('')
const outputPath = ref('')
const loading = ref(false)
const resultado = ref(null)

const iniciarProcessamento = async () => {
  loading.value = true
  try {
    const response = await axios.post('http://localhost:8080/iniciar_processamento', {
      input_path: inputPath.value,
      output_path: outputPath.value
    })
    resultado.value = response.data
  } catch (e) {
    alert('Erro ao iniciar o processamento')
  } finally {
    loading.value = false
  }
}
</script>

<style>
@keyframes gradient-x {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}
.animate-gradient-x {
  background-size: 400% 400%;
  animation: gradient-x 15s ease infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-20px); }
}
.animate-float {
  animation: float 6s ease-in-out infinite;
}
.animate-float-slow {
  animation: float 10s ease-in-out infinite;
}

.neon-text {
  text-shadow: 0 0 10px #f0abfc, 0 0 30px #a855f7, 0 0 60px #6366f1;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.6s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
}
</style>
