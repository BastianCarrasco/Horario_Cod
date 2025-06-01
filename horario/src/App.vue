<template>
  <div class="app">
    <h1 style="color: black;" >Horario Cod - {{ currentDate }}</h1>
    <div class="matrix-container">
      <div class="matrix-header">
        <div class="corner-cell">Grupo 4</div>
        <div 
          v-for="day in weekDays" 
          :key="day" 
          class="day-header"
        >
          {{ day }}
        </div>
      </div>
      <div 
        v-for="(week, weekIndex) in weeksInJune" 
        :key="weekIndex" 
        class="matrix-row"
      >
        <div class="week-header">Semana {{ weekIndex + 1 }}</div>
        <div 
          v-for="day in week" 
          :key="day" 
          :class="['matrix-cell', getCellClass(4, day), { 'current-day': isCurrentDay(day) }]"
        >
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
      weekDays: ['Lun', 'Mar', 'Mié', 'Jue', 'Vie', 'Sáb', 'Dom'],
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
  computed: {
    weeksInJune() {
      const weeks = [];
      let week = [];
      
      // Ajustar para que el primer día caiga en lunes
      const firstDay = new Date(new Date().getFullYear(), 5, 1).getDay(); // 5 = junio
      const offset = firstDay === 0 ? 6 : firstDay - 1; // Ajuste para domingo
      
      // Rellenar días vacíos al inicio si es necesario
      for (let i = 0; i < offset; i++) {
        week.push(0); // 0 representa día vacío
      }
      
      // Organizar los días en semanas
      this.daysInJune.forEach(day => {
        week.push(day);
        if (week.length === 7) {
          weeks.push(week);
          week = [];
        }
      });
      
      // Añadir la última semana si no está completa
      if (week.length > 0) {
        // Rellenar con días vacíos al final si es necesario
        while (week.length < 7) {
          week.push(0);
        }
        weeks.push(week);
      }
      
      return weeks;
    }
  },
  created() {
    this.formatCurrentDate();
  },
  methods: {
    getCellValue(group, day) {
      if (day === 0) return '';
      return this.grupo4Pattern[day - 1] || 'D';
    },
    getCellClass(group, day) {
      if (day === 0) return 'empty-cell';
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

.matrix-container {
  display: flex;
  flex-direction: column;
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  overflow: hidden;
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

.corner-cell, .day-header, .week-header, .matrix-cell {
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
.cell-A { background-color: white; }
.cell-B { background-color: #e6f7ff; }
.cell-C { background-color: #fff9c4; }
.cell-D { background-color: #ffe0b2; }

.current-day {
  border: 2px solid #2196F3 !important;
}

/* Estilos responsivos */
@media (max-width: 600px) {
  .corner-cell, .week-header {
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
}
</style>