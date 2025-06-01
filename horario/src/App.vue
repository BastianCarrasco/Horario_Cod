<template>
  <div class="app">
    <h1>Horario Cod - {{ currentDate }}</h1>
    <div class="schedule-container">
      <div class="mobile-schedule">
        <div class="mobile-header">
          <div class="header-cell">Grupo \ Día</div>
          <div 
            v-for="day in daysInJune" 
            :key="day"
            :class="['day-cell', { 'current-day': isCurrentDay(day) }]"
          >
            {{ day }}
          </div>
        </div>
        <div class="mobile-row">
          <div class="group-header">Grupo 4</div>
          <div 
            v-for="day in daysInJune" 
            :key="`4-${day}`" 
            :class="['mobile-cell', getCellClass(4, day)]"
          >
            {{ getCellValue(4, day) }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      daysInJune: Array.from({ length: 30 }, (_, i) => i + 1),
      grupo4Pattern: [
        'D', 'C', 'C', 'C', 'C', 
        'D', 'D', 'D', 'D', 
        'A', 'A', 'A', 'A', 
        'D', 'D', 'D', 'D', 
        'C', 'C', 'C', 'C', 
        'D', 'D', 'D', 'D', 
        'A', 'A', 'A', 'A', 
        'D'
      ],
      currentDate: '',
      currentDay: new Date().getDate()
    };
  },
  created() {
    this.formatCurrentDate();
  },
  methods: {
    getCellValue(group, day) {
      return this.grupo4Pattern[day - 1] || 'D';
    },
    getCellClass(group, day) {
      const value = this.getCellValue(group, day);
      return {
        'cell-D': value === 'D',
        'cell-C': value === 'C',
        'cell-A': value === 'A',
        'cell-B': value === 'B'
      };
    },
    formatCurrentDate() {
      const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
      const today = new Date();
      this.currentDate = today.toLocaleDateString('es-ES', options);
    },
    isCurrentDay(day) {
      return day === this.currentDay;
    }
  }
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
  background-color: #f9f9f9;
  min-height: 100vh;
  width: 100%;
}

h1 {
  font-size: 1.3em;
  margin-bottom: 0.5em;
  color: #333;
  text-align: center;
}

.schedule-container {
  width: 100%;
  max-width: 100vw;
  overflow-x: auto;
  margin: 0 auto;
  padding: 0;
}

/* Estilos para móvil */
.mobile-schedule {
  display: flex;
  flex-direction: column;
  width: 100%;
  min-width: fit-content;
}

.mobile-header, .mobile-row {
  display: flex;
  width: 100%;
  min-width: fit-content;
}

.header-cell, .day-cell, .group-header, .mobile-cell {
  min-width: 40px;
  width: 40px;
  height: 40px;
  border: 1px solid #ddd;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 5px;
  font-size: 0.9em;
  color: black;
  flex-shrink: 0;
}

.header-cell, .group-header {
  font-weight: bold;
  background-color: #f2f2f2;
  position: sticky;
  left: 0;
  z-index: 2;
}

.day-cell {
  background-color: #f2f2f2;
}

.current-day {
  border: 2px solid #2196F3 !important;
}

/* Colores de celdas */
.cell-A { background-color: white; }
.cell-B { background-color: #e6f7ff; }
.cell-C { background-color: #fff9c4; }
.cell-D { background-color: #ffe0b2; }

/* Estilos para pantallas más grandes (tablets y desktop) */
@media (min-width: 768px) {
  .app {
    padding: 20px;
  }
  
  h1 {
    font-size: 1.8em;
    margin-bottom: 20px;
  }
  
  .header-cell, .day-cell, .group-header, .mobile-cell {
    min-width: 50px;
    width: 50px;
    height: 50px;
    font-size: 1em;
  }
  
  .mobile-schedule {
    width: auto;
    margin: 0 auto;
  }
}

@media (min-width: 1024px) {
  .header-cell, .day-cell, .group-header, .mobile-cell {
    min-width: 60px;
    width: 60px;
    height: 60px;
    font-size: 1.1em;
  }
}
</style>