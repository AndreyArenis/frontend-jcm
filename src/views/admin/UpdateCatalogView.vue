<template>
    <div class="p-5 space-y-3 rounded-lg shadow-md bg-gray-800">
        <h1 class="text-2xl font-black text-green-500 mb-6">Actualizar Catálogo</h1>
        <form @submit.prevent="updateCatalog">
            <div v-for="(service, index) in catalogStore.services" :key="service._id" :class="{
                'p-4 mb-4 border rounded-lg shadow-sm transition-all duration-300': true,
                'bg-gray-700 text-gray-400': !service.isEditable,
                'bg-white text-black border-green-500': service.isEditable
            }">

                <div class="mb-4">
                    <label class="block text-xl font-black"
                        :class="service.isEditable ? 'text-green-600' : 'text-gray-400'">
                        Nombre del Servicio
                    </label>
                    <input type="text" v-model="service.name" :disabled="!service.isEditable"
                        class="w-full p-3 border rounded-lg"
                        :class="service.isEditable ? 'bg-white text-black' : 'bg-gray-700 text-gray-400 cursor-not-allowed'" />
                </div>

                <div class="mb-4">
                    <label class="block text-xl font-black"
                        :class="service.isEditable ? 'text-green-600' : 'text-gray-400'">
                        Precio del Servicio
                    </label>
                    <input type="number" v-model="service.price" :disabled="!service.isEditable"
                        class="w-full p-3 border rounded-lg"
                        :class="service.isEditable ? 'bg-white text-black' : 'bg-gray-700 text-gray-400 cursor-not-allowed'" />
                </div>

                <div class="flex justify-between">
                    <button type="button" @click="toggleEdit(service, index)"
                        class="bg-blue-600 rounded-lg p-3 text-white text-sm uppercase font-black flex-1 md:flex-none mr-3 transition-all duration-300">
                        {{ service.isEditable ? 'Cancelar' : 'Editar' }}
                    </button>

                    <button type="submit" v-if="service.isEditable"
                        :disabled="!service.isEditable || catalogStore.loading"
                        class="bg-green-600 rounded-lg p-3 text-white text-sm uppercase font-black flex-1 md:flex-none transition-all duration-300"
                        :class="{ 'opacity-50 cursor-not-allowed': !service.isEditable || catalogStore.loading }">
                        <div class="flex items-center justify-center">
                            <div v-if="catalogStore.loading"
                                class="spinner-border animate-spin inline-block w-4 h-4 border-4 rounded-full border-t-transparent border-white mr-2">
                            </div>
                            <span v-if="!catalogStore.loading">Actualizar servicio</span>
                            <span v-else>Actualizando...</span>
                        </div>
                    </button>
                </div>
            </div>
        </form>
    </div>
</template>

<script setup>
import { onMounted, inject, ref } from 'vue';
import { useCatalogStore } from '@/stores/catalog';

const toast = inject('toast');
const catalogStore = useCatalogStore();
const originalServices = ref([]);

onMounted(async () => {
    await catalogStore.fetchServices();

    originalServices.value = JSON.parse(JSON.stringify(catalogStore.services));
});

const toggleEdit = (service, index) => {

    catalogStore.services.forEach((s, i) => {
        if (i !== index) {
            s.isEditable = false;
        }
    });

    service.isEditable = !service.isEditable;

    if (!service.isEditable) {
        catalogStore.services[index] = JSON.parse(JSON.stringify(originalServices.value[index]));
    }
};

const hasChanges = (original, edited) => {
    return original.name !== edited.name || original.price !== edited.price;
};

const updateCatalog = async () => {
    try {
        const servicesToUpdate = catalogStore.services.filter((service, index) =>
            service.isEditable && hasChanges(originalServices.value[index], service)
        );

        if (servicesToUpdate.length > 0) {
            await catalogStore.updateServices(servicesToUpdate);
            toast.success('Servicio actualizado correctamente!');
            catalogStore.services.forEach(service => service.isEditable = false);
            // Actualizar la copia de servicios originales
            originalServices.value = JSON.parse(JSON.stringify(catalogStore.services));
        } else {
            toast.warning('No hay cambios para actualizar');
        }
    } catch (error) {
        toast.error('Hubo un problema al actualizar el servicio.');
    }
};
</script>

<style scoped>
.spinner-border {
    border-width: 4px;
    border-style: solid;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}
</style>
