<template>
  <Head title="About us" />

  <AuthenticatedLayout>
    <template #header>
      Proyectos
    </template>
    <nav class="border-b text-sm flex justify-start -mt-4 mb-3">
      <button class="inline-block px-4 py-2"
        :class="[currentTab === 1 ? 'border-b-2 border-indigo-600 text-indigo-600 font-semibold' : 'text-gray-700 hover:text-black']"
        @click="changeTab(1)">
        Mis proyectos
      </button>
      <button class="inline-block px-4 py-2 border-b-2"
        :class="[currentTab === 2 ? 'border-b-2 border-indigo-600 text-indigo-600 font-semibold' : 'text-gray-700 hover:text-black']"
        @click="changeTab(2)">
        Colaboraciones
      </button>
    </nav>
    <div v-if="currentTab == 1">
      <div class="row justify-end">
        <PrimaryButton @click="showCreateForm">
          <div class="flex">
            <svg xmlns="http://www.w3.org/2000/svg" class="icon" width="24" height="24" viewBox="0 0 24 24"
              stroke-width="2" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">
              <path stroke="none" d="M0 0h24v24H0z" fill="none" />
              <line x1="12" y1="5" x2="12" y2="19" />
              <line x1="5" y1="12" x2="19" y2="12" />
            </svg>
            Crear Proyectot
          </div>
        </PrimaryButton>
      </div>
      <table class="w-full text-sm text-left text-gray-500 mt-5 shadow-sm border">
        <thead class="text-xs text-gray-700 uppercase bg-gray-50">
          <tr>
            <th scope="col" class="px-6 py-3">
              Nombrecc
            </th>
            <th scope="col" class="px-6 py-3">
              Descripción
            </th>
            <th scope="col" class="px-6 py-3">
              <div class="flex items-center">
                Fecha de finalización
              </div>
            </th>
            <th scope="col" class="px-6 py-3">
              <span class="sr-only">Edit</span>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr class="bg-white border-b" v-for="project in myProjects" :key="project.id" v-if="myProjects.length">
            <th scope="row" class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap">
              <a :href="route('projects.show', project.id)" class="hover:text-violet-700"> {{ project.name }}</a>
            </th>
            <td class="px-6 py-4">
              {{ project.description }}
            </td>
            <td class="px-6 py-4">
              {{ project.date_end }}
            </td>
            <td class="px-6 py-4 text-right">
              <button class="font-medium text-blue-600 dark:text-blue-500 hover:underline" @click="showEditForm(project)">
                Editar
              </button>
              <button class="font-medium text-red-600 hover:underline mx-2"
                @click="deleteProject(project.id, project.name)">
                Eliminar
              </button>
            </td>
          </tr>
          <tr class="bg-white border-b" v-else>
            <th scope="row" colspan="3" class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap text-center">
              No hay proyectos colaborativos
            </th>
          </tr>
        </tbody>
      </table>
    </div>
    <div v-if="currentTab == 2">
      <table class="w-full text-sm text-left text-gray-500 mt-5 shadow-sm border">
        <thead class="text-xs text-gray-700 uppercase bg-gray-50">
          <tr>
            <th scope="col" class="px-6 py-3">
              Nombre
            </th>
            <th scope="col" class="px-6 py-3">
              Descripción
            </th>
            <th scope="col" class="px-6 py-3">
              <div class="flex items-center">
                Fecha de finalización
              </div>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr class="bg-white border-b" v-for="project in collaborations" :key="project.id" v-if="collaborations.length">
            <th scope="row" class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap">
              <a :href="route('projects.show', project.id)" class="hover:text-violet-700"> {{ project.name }}</a>
            </th>
            <td class="px-6 py-4">
              {{ project.description }}
            </td>
            <td class="px-6 py-4">
              {{ project.date_end }}
            </td>
          </tr>
          <tr class="bg-white border-b" v-else>
            <th scope="row" colspan="3" class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap text-center">
              No hay proyectos colaborativos
            </th>
          </tr>
        </tbody>
      </table>
    </div>
  </AuthenticatedLayout>
  <div v-if="isCreateFormShow" class="form-cover">
    <ProjectForm :closeForm="closeForm" :user_id="props.user_id" :op="1" />
  </div>
  <div v-if="isEditFormShow" class="form-cover">
    <ProjectForm :closeForm="closeForm" :user_id="props.user_id" :op="2" :project="projectSelected" />
  </div>
</template>

<script setup>
import { Head } from '@inertiajs/vue3';
import { ref, onMounted } from 'vue';
import Swal from 'sweetalert2';
import { useForm } from '@inertiajs/vue3';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import ProjectForm from '@/Components/ProjectForm.vue'
import PrimaryButton from '@/Components/PrimaryButton.vue';

const props = defineProps({
  myProjects: { type: Object },
  user_id: Number,
})

const collaborations = ref([]);

const form = useForm({});
let isCreateFormShow = ref(false)
let isEditFormShow = ref(false)
let projectSelected = ref(null)
let currentTab = ref(1)

function showCreateForm() {
  isCreateFormShow.value = true
}

function showEditForm(project) {
  projectSelected.value = project;
  isEditFormShow.value = true
}

function closeForm() {
  isCreateFormShow.value = false;
  isEditFormShow.value = false;
  projectSelected.value = null;
}

function changeTab(tab) {
  currentTab.value = tab
}

const showAlert = (message) => {
  Swal.fire({
    title: message.text,
    icon: message.type,
    showConfirmButton: false,
    timer: 1500, // Muestra la alerta durante 1.5 segundos
  });
};

const getCollaborations = async () => {
  await axios.get(route('api.users.collaborations', props.user_id))
    .then((response) => {
      collaborations.value = response.data;
    })
    .catch(error => console.log(error))
}

const deleteProject = (id, name) => {
  Swal.fire({
    title: '¿Estás seguro de eliminar el proyecto ' + name + '?',
    text: 'Se eliminará el proyecto y los diagramas asociados al mismo',
    icon: 'question',
    showCancelButton: true,
    confirmButtonText: 'Si, eliminar',
    cancelButtonText: 'Cancelar'
  }).then((result) => {
    if (result.isConfirmed) {
      form.delete(route('projects.destroy', id));
    }
  })
};

onMounted(() => {
  getCollaborations();
});

</script>

<style>
.form-cover {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 999;
  background: rgba(0, 0, 0, 0.5);
}
</style>
