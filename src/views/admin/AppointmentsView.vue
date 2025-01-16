<template>
    <div>
        <p class="text-white text-lg mt-5">
            A continuación podrás administrar tus próximas citas
        </p>

        <!-- Selector de vista -->
        <div class="mt-5 flex space-x-4">
            <button class="bg-blue-500 hover:bg-blue-700 text-white py-2 px-4 rounded"
                :class="{ 'bg-gray-500': viewMode === 'list' }" @click="setView('list')">
                Ver en lista
            </button>
            <button class="bg-green-500 hover:bg-green-700 text-white py-2 px-4 rounded"
                :class="{ 'bg-gray-500': viewMode === 'calendar' }" @click="setView('calendar')">
                Ver en calendario
            </button>
        </div>

        <!-- Vista en lista -->
        <div v-if="viewMode === 'list'" class="mt-10">
            <p v-if="user.loading" class="text-white text-2xl text-center mt-5">Cargando...</p>
            <p v-if="user.noAppointments" class="text-white text-2xl text-center mt-5">
                No hay citas
            </p>
            <div v-else class="grid grid-cols-1 gap-5">
                <AdminAppointment v-for="appointment in user.userAppointment" :key="appointment._id"
                    :appointment="appointment" />
            </div>
        </div>

        <!-- Vista en calendario -->
        <div v-if="viewMode === 'calendar'" class="mt-10">
            <CalendarView :appointments="user.userAppointment" />
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import { useUserStore } from '@/stores/user';
import AdminAppointment from '@/components/AdminAppointment.vue';
import CalendarView from '@/components/CalendarView.vue';

// Store de usuario
const user = useUserStore();

// Vista activa: lista o calendario
const viewMode = ref('list');

// Cambiar entre vista de lista y calendario
const setView = (mode) => {
    viewMode.value = mode;
};
</script>