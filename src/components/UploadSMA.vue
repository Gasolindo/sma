<template>
  <div class="max-w-5xl mx-auto p-10">

    <!-- Título -->
    <h1 class="text-6xl font-extrabold text-center mb-4">
      <span class="text-cyan-400">S</span>
      <span class="text-pink-400">M</span>
      <span class="text-emerald-400">A</span>
    </h1>

    <p class="text-center text-gray-300 mb-10">
      Sistema Multiagente para Avaliação da Extração de Informação
    </p>

    <!-- Upload -->
    <div class="bg-black/60 p-8 rounded-3xl border border-cyan-500">

      <input
        type="file"
        accept=".txt"
        @change="selecionarArquivo"
        class="block w-full mb-6 text-white"
      />

      <button
        @click="enviarArquivo"
        class="w-full bg-gradient-to-r from-cyan-500 to-pink-500 py-3 rounded-xl font-bold hover:scale-105 transition"
      >
        🚀 Processar Texto
      </button>

      <!-- Loading -->
      <p v-if="loading" class="mt-6 text-cyan-400 animate-pulse">
        🧠 SMA processando...
      </p>
    </div>

    <!-- Resultado -->
    <div v-if="mensagem" class="mt-12 bg-black/70 p-6 rounded-2xl border border-cyan-400">
      <h3 class="text-xl font-bold text-cyan-300 mb-2">📢 Resposta do SMA</h3>
      <p class="whitespace-pre-wrap">{{ mensagem }}</p>
    </div>

    <!-- Imagens -->
    <div v-if="imagens.length" class="grid grid-cols-1 md:grid-cols-2 gap-6 mt-10">
      <div
        v-for="(img, index) in imagens"
        :key="index"
        class="bg-black/50 p-4 rounded-xl border border-pink-400"
      >
        <img :src="img" class="rounded-lg shadow-lg" />
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue'

const arquivo = ref(null)
const loading = ref(false)
const mensagem = ref('')
const imagens = ref([])

function selecionarArquivo(event) {
  arquivo.value = event.target.files[0]
}

async function enviarArquivo() {
  if (!arquivo.value) return

  loading.value = true
  mensagem.value = ''
  imagens.value = []

  const formData = new FormData()
  formData.append('file', arquivo.value)

  try {
    const response = await fetch('http://localhost:8080/iniciar_processamento', {
      method: 'POST',
      body: formData
    })

    const data = await response.json()

    mensagem.value = data.mensagem || 'Processamento concluído.'
    imagens.value = data.imagens || []

  } catch (e) {
    mensagem.value = 'Erro ao se comunicar com o backend.'
  } finally {
    loading.value = false
  }
}
</script>
