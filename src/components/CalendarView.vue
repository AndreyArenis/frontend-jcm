<script setup>
import { computed } from 'vue';
import FullCalendar from '@fullcalendar/vue3';
import dayGridPlugin from '@fullcalendar/daygrid';
import interactionPlugin from '@fullcalendar/interaction';

// Recibir las citas como props
const props = defineProps({
    appointments: {
        type: Array,
        required: true,
    },
});

// Transformar las citas en eventos del calendario
const events = computed(() =>
    props.appointments.map((appointment) => ({
        title: `${appointment.user?.name || 'Sin nombre'} - ${appointment.time}`,
        date: appointment.date, // Fecha de la cita
        extendedProps: {
            email: appointment.user?.email || '',
            services: appointment.serviceDetails || [],
            totalAmount: appointment.totalAmount || 0,
        },
    }))
);

// Configuración del calendario
const calendarOptions = computed(() => ({
    plugins: [dayGridPlugin, interactionPlugin],
    initialView: 'dayGridMonth',
    dateClick: (arg) => handleDateClick(arg),
    events: events.value,
}));

// Función para manejar clic en las fechas
const handleDateClick = (arg) => {
    alert('Date clicked: ' + arg.dateStr);
    console.log('Fecha seleccionada:', arg.dateStr);
};
</script>

<template>
    <FullCalendar :options="calendarOptions" />
</template>
