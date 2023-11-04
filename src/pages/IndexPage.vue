<script setup lang="ts">
  import { useQuasar } from 'quasar'
  import { ref, onMounted, computed, watch } from 'vue';
  import dragDrop from "drag-drop";

  const $q = useQuasar();
  const filesSelected = ref([]);

  const openExplorerFile = () => {
    document.getElementsByClassName('q-field')[0].click()
  }

  const checkValidations = (rejectedEntries) => {
    for (let index = 0; index < rejectedEntries.length; index++) {
      const element = rejectedEntries[index];      
      if ( element.failedPropValidation == 'duplicate' ) {
        $q.notify({ type: 'negative', message: `El archivo ${ element.file.name } se encuentra duplicado` })    
        continue;
      }else if( element.failedPropValidation == 'max-files' ){
        $q.notify({ type: 'negative', message: `Se supero el maximo de archivos permitidos` })    
        continue;
      }else if( element.failedPropValidation == 'max-file-size' ){
        $q.notify({ type: 'negative', message: `El archivo ${ element.file.name } supera el tamaño permitido 20Mb` })
        continue;
      }else{
        $q.notify({ type: 'negative', message: `${rejectedEntries.length} archivo no pasó las restricciones de validación` })
        continue;
      }      
    }
  }
  
  onMounted(() => {
    dragDrop('#drag-drop-element', (files, pos, fileList, directories) => {
      for (let index = 0; index < files.length; index++) {
        const element = files[index];    
        if ( element.type == 'application/pdf') { //validar formato
          if ( (element.size / 1024) <= 20480) { //validar peso max: 20Mb
            const exist = filesSelected.value.some( x => x.name == element.name); 
            if ( !exist ) { //validar duplicado
              if (filesSelected.value.length < 5) { //validar max-file : 5
                filesSelected.value.push(element)                     
              }else
                $q.notify({ type: 'negative', message: `Solo se puede subir maximo 5 archivos`})
            }else
              $q.notify({ type: 'negative', message: `El archivo ${ element.name } se encuentra duplicado`})
          }else{
            $q.notify({ type: 'negative', message: `${element.name} supera el peso permitido`})
            continue;
          }
        }else{
          $q.notify({ type: 'negative', message: `${element.name} no es un archivo PDF` })
          continue;
        }
      }
    })
  })

</script>
<template>
  <q-page class="row q-gutter-xs justify-between" style="min-height: auto;">

    <div class="bg-white q-mt-lg" style="border-radius: 3%;width: 48%">
      <div class="text-h4 text-center q-pt-lg q-mt-sm text-weight-bolder text-blue-12" 
        style="letter-spacing: 2px;">
        ¿Como funciona?
      </div>
      <div class="text-center q-mt-xl">
        <img src="/public/img1.JPG">
      </div>
      <div class="text-center q-pt-lg q-pb-xl q-mt-sm">
        Este es un servicio gratuito de TodoLegal
      </div>
    </div>

    <div class="bg-white q-mt-lg" style="border-radius: 3%;width: 48%" id="drag-drop-element">
      <div class="text-subtitle1 q-mt-md q-ml-lg text-weight-bolder text-blue-10">
        <label style="display: block">Carga de Documentos</label>
        <label style="display: block" class="text-weight-regular text-blue-grey-14">
          Sube tus documentos y ordénalos
        </label>
        <div id="inputFile" class="hiddenElement">
          <q-file 
            input-class="inputFileClick"
            accept=".pdf"
            max-files="5"
            max-file-size="20971520"
            @rejected="checkValidations"
            v-model="filesSelected" filled
            multiple append />
        </div>
      </div>

      <div v-if="filesSelected.length == 0" 
        class="row text-center q-mt-xl justify-center" 
        >
        <div class="col-sm-8 q-py-xl rounded-borders" style="border: 3px #448aff dashed">
          <img src="/public/nube.png" style="width: 34%;">
          <label style="display: block" class="q-mt-lg">Arrastra y suelta tus documentos aqui o
            <span @click="openExplorerFile"
              class="text-weight-medium text-indigo-14">
              Buscar archivo
            </span>
          </label>
        </div>        
      </div>

      <div v-else class="row text-center q-mt-xl justify-center" >
        <div class="col-sm-9 q-py-xl q-px-md flex flex-center rounded-borders" 
          style="border: 3px #448aff dashed; background-color: #F4F6FC; min-height: 203px;" 
          id="drag-drop-element">

          <div v-for="(file, index) in filesSelected" :key="index"
            class="row bg-white flex q-mb-sm content-box rounded-borders">

            <div class="flex flex-center" 
              style="border-right: 2px solid #dfdfe7; width: 10%">
              <q-icon size="xs" color="blue-12" class="text-weight-medium" name="reorder" />
            </div>
            <div class="flex justify-left text-blue-grey-13 q-pl-sm" 
              style="width: 80%">
              {{ file.name.split('.')[0] }}
            </div>

            <div @click="filesSelected.splice(index, 1);"
              class="border-rigth flex flex-center" 
              style="border-left: 2px solid #dfdfe7; width: 10%;cursor: pointer">
              <q-icon size="xs" color="blue-12" 
                class="text-weight-medium" name="delete" />
            </div>
          </div>

          <div v-if="filesSelected.length < 5"
            @click="openExplorerFile"
            class="row flex content-box rounded-borders" 
            style="border: 3px white solid;cursor: pointer">

            <div class="flex flex-center" 
              style="border-right: 2px solid #dfdfe7; width: 12%">
              <q-icon size="xs" color="blue-12" class="text-weight-medium" name="add" />
            </div>
            <div class="flex justify-left items-center text-blue-12 text-weight-medium q-pl-sm" 
              style="width: 75%">
              Añadir más documentos
            </div>

            <div @click="filesSelected.splice(index, 1)"
              class="border-rigth flex flex-center column text-weight-bolder" 
              style="border-left: 2px solid #dfdfe7; width: 13%;cursor: pointer">
              <label style="font-size: 10px" class="text-blue-12">5 Máx</label>
              <label style="font-size: 10px" class="text-blue-12">20Mb</label>
            </div>
          </div>

        </div>  
      </div>

      <div v-if="filesSelected.length == 0"
        class="text-center q-mt-sm text-red-9">
        Solo se admiten archivos en formato <span>PDF</span>
      </div>

      <div class="row text-center justify-center q-mt-sm text-red-9">
         <q-btn class="col-sm-6 q-py-sm q-mt-md" color="indigo-12" label="Siguiente" />
      </div>
    </div>

  </q-page>
</template>
<style>
.border-rigth{
  border-top-right-radius: 10%;
    border-bottom-right-radius: 10%;
}
.content-box{
  padding-top: 8px;
  padding-bottom: 8px;
  width: 100%;
  background-color: #F4F6FC;
}
.hiddenElement{
  visibility: collapse;
  position: fixed;
}
</style>