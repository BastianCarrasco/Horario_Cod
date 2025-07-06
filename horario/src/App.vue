<template>
  <div class="app">
    <h1 style="color: black">Horario Cod - {{ currentDate }}</h1>

    <div class="month-selector">
      <select v-model="selectedMonth" @change="changeMonth">
        <option v-for="(month, index) in months" :key="index" :value="index">
          {{ month }}
        </option>
      </select>
    </div>

    <!-- La matriz del horario ahora se muestra para cualquier mes -->
    <div class="matrix-container">
      <div class="month-title">
        {{ selectedMonthName }} {{ new Date().getFullYear() }}
      </div>
      <div class="matrix-header">
        <div class="corner-cell">Grupo 4</div>
        <div v-for="day in weekDays" :key="day" class="day-header">
          {{ day }}
        </div>
      </div>
      <div v-for="(week, weekIndex) in weeksInMonth" :key="weekIndex" class="matrix-row">
        <div class="week-header">Semana {{ weekIndex + 1 }}</div>
        <div v-for="day in week" :key="day" :class="[
          'matrix-cell',
          getCellClass(4, day),
          { 'current-day': isCurrentDay(day) && isCurrentMonth },
        ]">
          <div class="day-number">{{ day }}</div>
          <div class="day-value">{{ getCellValue(4, day) }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      weekDays: ["Lun", "Mar", "Mié", "Jue", "Vie", "Sáb", "Dom"],
      months: [
        "Enero",
        "Febrero",
        "Marzo",
        "Abril",
        "Mayo",
        "Junio",
        "Julio",
        "Agosto",
        "Septiembre",
        "Octubre",
        "Noviembre",
        "Diciembre",
      ],
      selectedMonth: new Date().getMonth(), // Mes actual por defecto
      // Nuevo patrón base de 16 días (AAAA,DDDD,CCCC,DDDD)
      grupo4PatternBase: [
        "A",
        "A",
        "A",
        "A", // 4 As
        "D",
        "D",
        "D",
        "D", // 4 Ds
        "C",
        "C",
        "C",
        "C", // 4 Cs
        "D",
        "D",
        "D",
        "D", // 4 Ds
      ],
      currentDate: "",
      currentDay: new Date().getDate(),
      currentMonth: new Date().getMonth(),
    };
  },
  computed: {
    añoActual() {
      return new Date().getFullYear();
    },
    // Calcula las semanas del mes seleccionado para la visualización
    weeksInMonth() {
      const weeks = [];
      let week = [];

      const year = new Date().getFullYear();
      // Obtener el día de la semana del primer día del mes (0=domingo, 1=lunes...)
      const firstDayOfMonth = new Date(year, this.selectedMonth, 1).getDay();
      // Ajuste para que el lunes sea el primer día de la semana (0 para domingo, 6 para lunes)
      const offset = firstDayOfMonth === 0 ? 6 : firstDayOfMonth - 1;

      // Obtiene el número total de días en el mes seleccionado
      const daysInMonth = new Date(year, this.selectedMonth + 1, 0).getDate();

      // Rellenar días vacíos al inicio de la primera semana si es necesario
      for (let i = 0; i < offset; i++) {
        week.push(0); // 0 representa un día vacío
      }

      // Organizar los días del mes en semanas de 7 días
      for (let day = 1; day <= daysInMonth; day++) {
        week.push(day);
        if (week.length === 7) {
          weeks.push(week);
          week = [];
        }
      }

      // Añadir la última semana si no está completa y rellenar con días vacíos al final
      if (week.length > 0) {
        while (week.length < 7) {
          week.push(0); // 0 representa un día vacío
        }
        weeks.push(week);
      }

      return weeks;
    },
    // Determina si el mes seleccionado es el mes actual
    isCurrentMonth() {
      return this.selectedMonth === this.currentMonth;
    },
    // Genera el patrón de turnos para el mes seleccionado, basado en el patrón base cíclico
    grupo4PatternForSelectedMonth() {
      const year = new Date().getFullYear();
      let totalDaysBeforeSelectedMonth = 0;
      // Suma los días de los meses anteriores al seleccionado para calcular el desfase
      for (let i = 0; i < this.selectedMonth; i++) {
        totalDaysBeforeSelectedMonth += new Date(year, i + 1, 0).getDate();
      }

      // Calcula el índice de inicio en el patrón base de 16 días
      const startDayIndex =
        totalDaysBeforeSelectedMonth % this.grupo4PatternBase.length;
      // Obtiene el número de días del mes seleccionado
      const daysInSelectedMonth = new Date(
        year,
        this.selectedMonth + 1,
        0
      ).getDate();

      const pattern = [];
      // Rellena el patrón para el mes actual, aplicando el ciclo
      for (let i = 0; i < daysInSelectedMonth; i++) {
        pattern.push(
          this.grupo4PatternBase[
          (startDayIndex + i) % this.grupo4PatternBase.length
          ]
        );
      }
      return pattern;
    },
    // Nombre del mes seleccionado para mostrar en el título
    selectedMonthName() {
      return this.months[this.selectedMonth];
    },
  },
  created() {
    this.formatCurrentDate();
  },
  methods: {
    // Obtiene el valor del turno para un día específico (D, C, A, etc.)
    getCellValue(group, day) {
      if (day === 0) return ""; // Retorna vacío para días que no pertenecen al mes
      // Usa el patrón dinámicamente generado para el mes seleccionado
      return this.grupo4PatternForSelectedMonth[day - 1] || "D"; // Default a 'D' si hay algún problema (aunque no debería)
    },
    // Asigna clases CSS basadas en el valor del turno para estilizar las celdas
    getCellClass(group, day) {
      if (day === 0) return "empty-cell";
      const value = this.getCellValue(group, day);
      return {
        "cell-D": value === "D",
        "cell-C": value === "C",
        "cell-A": value === "A",
        "cell-B": value === "B", // Incluido por si en el futuro usas 'B'
      };
    },
    // Formatea y establece la fecha actual en el encabezado
    formatCurrentDate() {
      const options = {
        weekday: "long",
        year: "numeric",
        month: "long",
        day: "numeric",
      };
      const today = new Date();
      this.currentDate = today.toLocaleDateString("es-ES", options);
    },
    // Determina si un día es el día actual del mes
    isCurrentDay(day) {
      return day === this.currentDay;
    },
    // Método que se ejecuta cuando se cambia el mes en el selector
    changeMonth() {
      // No se requiere lógica adicional aquí ya que los patrones son computados dinámicamente
    },
  },
};
</script>