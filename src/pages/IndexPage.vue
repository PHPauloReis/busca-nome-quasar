<template>
  <q-page class="q-pa-lg">

    <div class="text-h5 q-mb-lg">Popularidade do seu nome</div>

    <q-form
      @submit="onSubmit()"
    >

      <q-input
        class="q-mb-md"
        filled
        v-model="nome"
        label="Nome"
        :rules="[
          val => val && val.length > 0 || 'Digite o seu nome!',
          val =>
            val && val.length <= 10 && val.length >= 3 ||
            'Seu nome deve ter entre 3 e 10 caracteres!',
        ]"
      />

      <q-btn
        color="primary"
        class="full-width"
        label="Consultar"
        type="submit"
      />

    </q-form>

    <q-dialog v-model="mostrarModal">
      <q-card>
        <q-card-section>
          <div class="text-h6">Veja o seu nome!</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          {{ frase }}
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="OK" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <q-dialog v-model="httpError">
      <q-card>
        <q-card-section>
          <div class="text-h6">Alert</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          Ocorreu um erro em sua requisição!
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="OK" color="primary" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>

  </q-page>
</template>

<script setup>

import axios from 'axios';
import { ref } from 'vue';
import { useQuasar } from 'quasar';

const $q = useQuasar();
const httpError = ref(false);

const nome = ref('');
const mostrarModal = ref(false);
const frase = ref('');

function consultarNome() {
  const baseUrl = 'https://servicodados.ibge.gov.br/api/v2/censos/nomes/';

  $q.loading.show();

  axios.get(baseUrl + nome.value).then((response) => {
    const dados = response.data[0].res;
    const dadosMaisRecentes = dados[dados.length - 1];

    frase.value = `O seu nome foi registrado ${dadosMaisRecentes.frequencia} vezes`;

    mostrarModal.value = true;
    $q.loading.hide();
  }).catch(() => {
    httpError.value = true;
    $q.loading.hide();
  });
}

function onSubmit() {
  consultarNome();
}

</script>
