<script setup>
import { computed, onMounted, ref, watch } from 'vue'

const musicas = ref([]);

const adicionarMusica = ref(false);

const nomeMusica = ref("");
const nomeArtista = ref("");
const lancamentoMusica = ref("");
const imagemMusica = ref("");
const generoMusica = ref("");

const salvarMusica = () => {
  musicas.value.push({
    nome: nomeMusica.value,
    artista: nomeArtista.value,
    lancamento: lancamentoMusica.value,
    genero: generoMusica.value,
    imagem: imagemMusica.value

  })
  fecharCriacaoDeMusica();
}
const fecharCriacaoDeMusica = () => {
  nomeMusica.value = "";
  nomeArtista.value = "";
  lancamentoMusica.value = "";
  generoMusica.value = "";
  imagemMusica.value = "";

  adicionarMusica.value = false;
}
const definirLike = (index, like) => {
  musicas.value[index].like = like;

}
const excluirMusica = (index) => {
  const texto = `Tem certeza que deseja excluir ${musicas.value[index].nome}?`
  if (confirm(texto)) {
    musicas.value.splice(index, 1)
  }

}
const filtroSelecionado = ref("todos");

const musicaParaExibir = computed(() => {
  switch (filtroSelecionado.value) {
    case "gostei":
      return musicas.value.filter((item) => item.like === true)
    case "nao-gostei":
      return musicas.value.filter((item) => item.like === false)



    default:
      return musicas.value
  }
})

watch(musicas, (novoValor, antigoValor) => {
  localStorage.setItem("musiflix", JSON.stringify(novoValor))
}, { deep: true }

)

onMounted(() => {
  const dadoslocalStorage = localStorage.getItem("musiflix")
  if (dadoslocalStorage) {
    musicas.value = JSON.parse(dadoslocalStorage)
  }
}
)


</script>
<template>
  <div class="musiflix">
    <div class="acoes-usuario">
      <div class="filtros">
        <div class="titulo">Filtrar</div>
        <div class="opcoes-filtros">
          <button class="botao" @click="filtroSelecionado = 'todos'"
            :class="{ ativo: filtroSelecionado === 'todos' }">Todos</button>
          <button class="botao" @click="filtroSelecionado = 'gostei'"
            :class="{ ativo: filtroSelecionado === 'gostei' }">Gostei</button>
          <button class="botao" @click="filtroSelecionado = 'nao-gostei'"
            :class="{ ativo: filtroSelecionado === 'nao-gostei' }">Não Gostei</button>
        </div>
      </div>

      <div class="novo-musica">
        <div v-if="adicionarMusica" class="adicionar-musica">
          <input v-model="nomeMusica" type="text" autocomplete="off" placeholder="Nome do musica" />
          <input v-model="nomeArtista" type="text" autocomplete="off" placeholder="Nome do Artista" />
          <input v-model="imagemMusica" type="text" autocomplete="off" placeholder="URL da Imagem" />
          <input v-model="lancamentoMusica" type="text" autocomplete="off" placeholder="Lançamento" />
          <input v-model="generoMusica" type="text" autocomplete="off" placeholder="Gênero" />
          <div class="acoes">
            <button class="botao ativo" @click="salvarMusica">Salvar</button>
            <button class="botao danger ativo" @click="fecharCriacaoDeMusica">Cancelar</button>
          </div>
        </div>
        <button v-else class="botao ativo" @click="adicionarMusica = true">Adicionar musica</button>
      </div>
    </div>

    <div class="musicas">
      <div v-for="(musica, index) in musicaParaExibir" class="musica">
        <div class="capa-container">
          <div class="acoes-musica">
            <button class="botao" @click="definirLike(index, true)" :class="{ ativo: musica.like === true }"> Gostei
            </button>
            <button class="botao danger" @click="definirLike(index, false)"
              :class="{ ativo: musica.like === false }">Não
              Gostei</button>
            <button class="botao danger" @click="excluirMusica(index)">Excluir</button>
          </div>
          <img class="capa" :src="musica.imagem" alt="" />
        </div>
        <div class="nome">{{ musica.nome }}</div>
        <div class="nome">{{ musica.artista }}</div>
        <div class="info">{{ musica.lancamento }} - {{ musica.genero }}</div>
      </div>
      <p v-if="musicaParaExibir.length === 0" :style="{ flex: 1, textAlign: 'center', marginTop: '16px' }">Não há musica
        para ser exibida </p>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.musiflix {
  padding: 16px;

  .acoes-usuario {
    display: flex;
    justify-content: space-between;
    align-items: end;
    margin: 30px 0;

    .adicionar-musica {
      display: flex;
    }
  }

  .musicas {
    display: flex;
    gap: 20px;
    flex-wrap: wrap;
    min-height: 350px;

    .musica {
      .capa-container {
        width: 200px;
        height: auto;
        position: relative;

        .capa {
          width: 100%;
          height: 100%;
        }

        .acoes-musica {
          position: absolute;
          bottom: 0;
          padding-bottom: 12px;
          background-image: linear-gradient(to bottom, rgba(255, 0, 0, 0), rgb(0, 0, 0));
          display: none;
          flex-direction: column;
          width: 100%;
          height: 100%;
          justify-content: flex-end;
        }
      }

      .nome {
        font-weight: bold;
      }

      .info {
        font-size: 12px;
      }

      &:hover {
        .acoes-musica {
          display: flex;
        }
      }
    }
  }
}
</style>
