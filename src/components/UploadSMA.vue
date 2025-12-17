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

      <!-- <input
        type="file"
        accept=".txt"
        @change="selecionarArquivo"
        class="block w-full mb-6 text-white"
      /> -->

      <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">

<!-- INPUT PATH -->
<div>
  <label class="block text-cyan-300 font-semibold mb-2">
    📄 Caminho do arquivo de entrada
  </label>
  <input
    v-model="inputPath"
    type="text"
    placeholder="C:\\Users\\usuario\\arquivo.txt"
    class="w-full px-4 py-3 rounded-xl bg-black/40 border border-cyan-400 text-white focus:outline-none focus:ring-2 focus:ring-cyan-400"
  />
</div>

<!-- OUTPUT PATH -->
<div>
  <label class="block text-pink-300 font-semibold mb-2">
    📁 Caminho da pasta de saída
  </label>
  <input
    v-model="outputPath"
    type="text"
    placeholder="C:\\Users\\usuario\\resultado"
    class="w-full px-4 py-3 rounded-xl bg-black/40 border border-pink-400 text-white focus:outline-none focus:ring-2 focus:ring-pink-400"
  />
</div>

</div>

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
import { onMounted } from 'vue'

let arquivo_path = ''
const loading = ref(false)
const mensagem = ref('')
const imagens = ref([])

// function selecionarArquivo(event) {
  
//   arquivo_path = event.target.value
// }
function selecionarArquivo(event) {
  arquivo.value = event.target.files[0]
}

async function enviarArquivo() {
  console.log(arquivo_path)
  if (!arquivo_path) return

  loading.value = true
  mensagem.value = ''
  imagens.value = []

  try {

    const response = await fetch('http://localhost:8080/iniciar_processamento', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        'input_path': "C:\\Users\\mucar\\OpenIE_grafos\\_data\\1_frases_bia_corpus.txt",
        'output_path': "C:\\Users\\mucar\\Área de Trabalho\\teste extractionsss"
      })
    })

    // const data = await response.json()

    // mensagem.value = data.mensagem || 'Processamento concluído.'
    // imagens.value = data.imagens || []
    const data = await response.json()

    mensagem.value = data.mensagem || 'Processamento concluído.'

    if (Array.isArray(data.imagens)) {
      imagens.value = data.imagens.map(imgBase64 =>
        `data:image/jpeg;base64,${imgBase64}`
      )
    }

  } catch (e) {
    mensagem.value = 'Erro ao se comunicar com o backend.'
  } finally {
    loading.value = false
  }
}

import { io } from "socket.io-client"
const socket = io("ws://localhost:8080", {
      transports: ["websocket"]
    });

    socket.on("connect", () => {
      console.log('CONECTADOOO')
      // status.textContent = "Conectado ao servidor";
    });

    socket.on("disconnect", () => {
      // status.textContent = "Desconectado";
    });

    socket.on("resultados", (data) => {
    mensagem.value = data.mensagem || 'Processamento concluído.'

    if (Array.isArray(data.imagens)) {
      imagens.value = data.imagens.map(imgBase64 =>
        `data:image/jpeg;base64,${imgBase64}`
      )
    }



    // socket.on("resultados", (data) => {
    //   console.log( data);

      // if (!data || !Array.isArray(data.imagens)) {
      //   console.warn("Formato inválido");
      //   return;
      // }

      // // limpa imagens anteriores
      // galeria.innerHTML = "";

      // data.imagens.forEach((imgSrc) => {
      //   const img = document.createElement("img");
      //   img.src = imgSrc;
      //   galeria.appendChild(img);
      // });
    });

</script>
