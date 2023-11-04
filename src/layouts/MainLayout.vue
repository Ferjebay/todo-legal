<template>
  <q-layout view="hHh lpr fFf" style="background-image: linear-gradient(to bottom, #eef2f5, #e8eef4, #e2ebf3, #dde7f3, #d8e3f2);">

    <q-header class="bg-white text-black" height-hint="98">
      <q-toolbar>
        <q-toolbar-title class="q-ml-md">
          todo-Legal
        </q-toolbar-title>

        <label class="q-mr-md text-h6">
          SmartContractsSuite
        </label>

        <q-btn dense flat color="blue-13" round icon="menu" @click="toggleRightDrawer" />
      </q-toolbar>
    </q-header>
    
    <q-page-container>
      <div class="row q-mt-lg">

        <div v-if="!$q.screen.xs"
          class="col-10 offset-1 bg-white q-py-md">
          <div class="row justify-around text-subtitle1">
            <label class="text-h6 text-weight-medium">
              Firmar nuevo documento
            </label>
            <label>
              <span class="nav-option q-mr-sm nav-option-selected">1</span>
              Cargar Documento
            </label>
            <label>
              <span class="nav-option q-mr-sm">2</span>
              Indicar Firmantes
            </label>
            <label>
              <span class="nav-option q-mr-sm">3</span>
              Personalizaciones opcionales
            </label>
          </div>
        </div>

        <div class="col-10 offset-1">
          <router-view />
        </div>

      </div>
    </q-page-container>

    <q-footer v-if="$q.screen.xs"
     elevated class="bg-grey-8 text-white">
      <q-toolbar>
        <q-toolbar-title>
          <q-avatar>
            <img src="https://cdn.quasar.dev/logo-v2/svg/logo-mono-white.svg">
          </q-avatar>
        </q-toolbar-title>
      </q-toolbar>
    </q-footer>

  </q-layout>
</template>

<script>
import { ref } from 'vue'

export default {
  setup () {
    const rightDrawerOpen = ref(false)

    return {
      rightDrawerOpen,
      tab: ref('mails'),
      toggleRightDrawer () {
        rightDrawerOpen.value = !rightDrawerOpen.value
      }
    }
  }
}
</script>
<style>
.nav-option{
  display: inline-block;
  width: 30px !important;
  height: 30px;
  text-align: center;
  border-radius: 50%;
  border-color: #517dc1;
  color: #3A77EC;
  line-height: 28px;
  border: 1px #517dc1 solid;
}

.nav-option-selected{
  background-color: #517dc1;
  color: white;
}
</style>