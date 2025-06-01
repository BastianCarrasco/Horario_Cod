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

    <div v-if="selectedMonth === 5" class="matrix-container">
      <!-- 5 = Junio -->
      <div class="month-title">Junio {{ new Date().getFullYear() }}</div>
      <div class="matrix-header">
        <div class="corner-cell">Grupo 4</div>
        <div v-for="day in weekDays" :key="day" class="day-header">
          {{ day }}
        </div>
      </div>
      <div
        v-for="(week, weekIndex) in weeksInMonth"
        :key="weekIndex"
        class="matrix-row"
      >
        <div class="week-header">Semana {{ weekIndex + 1 }}</div>
        <div
          v-for="day in week"
          :key="day"
          :class="[
            'matrix-cell',
            getCellClass(4, day),
            { 'current-day': isCurrentDay(day) && isCurrentMonth },
          ]"
        >
          <div class="day-number">{{ day }}</div>
          <div class="day-value">{{ getCellValue(4, day) }}</div>
        </div>
      </div>
    </div>

    <div v-else class="construction-message">
      <div class="construction-icon">🚧</div>
      <h2>Horario en construcción</h2>
      <p>Estamos trabajando en el horario para {{ months[selectedMonth] }}.</p>
      <p>Por favor, consulta el horario de junio mientras tanto.</p>
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
      selectedMonth: 5, // Junio por defecto (0 = Enero, 5 = Junio)
      daysInJune: Array.from({ length: 30 }, (_, i) => i + 1),
      grupo4Pattern: [
        "D",
        "C",
        "C",
        "C",
        "C",
        "D",
        "D",
        "D",
        "D",
        "A",
        "A",
        "A",
        "A",
        "D",
        "D",
        "D",
        "D",
        "C",
        "C",
        "C",
        "C",
        "D",
        "D",
        "D",
        "D",
        "A",
        "A",
        "A",
        "A",
        "D",
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
    weeksInMonth() {
      const weeks = [];
      let week = [];

      // Obtener el primer día del mes seleccionado
      const firstDay = new Date(
        new Date().getFullYear(),
        this.selectedMonth,
        1
      ).getDay();
      const offset = firstDay === 0 ? 6 : firstDay - 1; // Ajuste para domingo

      // Días en el mes seleccionado
      const daysInMonth =
        this.selectedMonth === 5
          ? 30
          : new Date(
              new Date().getFullYear(),
              this.selectedMonth + 1,
              0
            ).getDate();

      // Rellenar días vacíos al inicio si es necesario
      for (let i = 0; i < offset; i++) {
        week.push(0);
      }

      // Organizar los días en semanas
      for (let day = 1; day <= daysInMonth; day++) {
        week.push(day);
        if (week.length === 7) {
          weeks.push(week);
          week = [];
        }
      }

      // Añadir la última semana si no está completa
      if (week.length > 0) {
        while (week.length < 7) {
          week.push(0);
        }
        weeks.push(week);
      }

      return weeks;
    },
    isCurrentMonth() {
      return this.selectedMonth === this.currentMonth;
    },
  },
  created() {
    this.formatCurrentDate();
  },
  methods: {
    obtenerAñoActual() {
      return new Date().getFullYear();
    },
    getCellValue(group, day) {
      if (day === 0) return "";
      // Solo aplicamos el patrón para junio
      if (this.selectedMonth === 5) {
        return this.grupo4Pattern[day - 1] || "D";
      }
      return "";
    },
    getCellClass(group, day) {
      if (day === 0) return "empty-cell";
      const value = this.getCellValue(group, day);
      return {
        "cell-D": value === "D",
        "cell-C": value === "C",
        "cell-A": value === "A",
        "cell-B": value === "B",
      };
    },
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
    isCurrentDay(day) {
      return day === this.currentDay;
    },
    changeMonth() {
      // Aquí podrías cargar datos diferentes para cada mes si los tuvieras
    },
  },
};
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

.app {
  font-family: Arial, sans-serif;
  padding: 15px;
  background-color: #aeaeae;
  min-height: 100vh;
  width: 100%;
  color: black;
}

h1 {
  font-size: 1.3em;
  margin-bottom: 1em;
  color: #333;
  text-align: center;
}

.month-selector {
  text-align: center;
  margin: 20px 0;
}

.month-selector select {
  padding: 8px 15px;
  border-radius: 4px;
  border: 1px solid #ddd;
  font-size: 1em;
  background: white;
}

.matrix-container {
  display: flex;
  flex-direction: column;
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  overflow: hidden;
}

.month-title {
  text-align: center;
  font-size: 1.5em;
  font-weight: bold;
  padding: 15px;
  background: #f2f2f2;
  border-bottom: 1px solid #ddd;
}

.matrix-header {
  display: flex;
  background: #f2f2f2;
  font-weight: bold;
}

.matrix-row {
  display: flex;
  border-bottom: 1px solid #eee;
}

.matrix-row:last-child {
  border-bottom: none;
}

.corner-cell,
.day-header,
.week-header,
.matrix-cell {
  flex: 1;
  min-height: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 8px;
  text-align: center;
}

.corner-cell {
  background: #f2f2f2;
  font-weight: bold;
  min-width: 80px;
  justify-content: flex-start;
  padding-left: 15px;
}

.day-header {
  font-weight: bold;
  background: #f2f2f2;
}

.week-header {
  background: #f2f2f2;
  font-weight: bold;
  min-width: 80px;
  justify-content: flex-start;
  padding-left: 15px;
}

.matrix-cell {
  flex-direction: column;
  position: relative;
}

.empty-cell {
  background: #fafafa;
}

.day-number {
  font-size: 0.8em;
  color: #666;
  margin-bottom: 3px;
}

.day-value {
  font-size: 1.2em;
  font-weight: bold;
}

/* Colores de celdas */
.cell-A {
  background-color: white;
}
.cell-B {
  background-color: #e6f7ff;
}
.cell-C {
  background-color: #fff9c4;
}
.cell-D {
  background-color: #ffe0b2;
}

.current-day {
  border: 2px solid #2196f3 !important;
}

.construction-message {
  text-align: center;
  max-width: 600px;
  margin: 30px auto;
  padding: 30px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.construction-icon {
  font-size: 3em;
  margin-bottom: 20px;
}

.construction-message h2 {
  color: #333;
  margin-bottom: 10px;
}

.construction-message p {
  color: #666;
  margin-bottom: 10px;
}

/* Estilos responsivos */
@media (max-width: 600px) {
  .corner-cell,
  .week-header {
    min-width: 60px;
    font-size: 0.9em;
    padding-left: 10px;
  }

  .day-header {
    font-size: 0.8em;
  }

  .day-number {
    font-size: 0.7em;
  }

  .day-value {
    font-size: 1em;
  }

  .construction-message {
    padding: 20px;
  }
}
</style>
