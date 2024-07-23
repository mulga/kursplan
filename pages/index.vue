<template>
  <div class="container mx-auto px-4 py-8">
    <h1 class="text-3xl font-bold mb-6">Kursbeginn- und Enddatum berechnen</h1>

    <form @submit.prevent="calculateEndDate" class="space-y-4">
      <div>
        <label for="state" class="block font-semibold">Bundesland</label>
        <select
          v-model="selectedState"
          id="state"
          class="form-input mt-1 block w-full"
        >
          <option
            v-for="state in states"
            :key="state.value"
            :value="state.value"
          >
            {{ state.text }}
          </option>
        </select>
      </div>

      <div>
        <label for="start-date" class="block font-semibold">Kursbeginn</label>
        <input
          type="date"
          v-model="startDate"
          id="start-date"
          class="form-input mt-1 block w-full"
          required
        />
      </div>

      <div>
        <label for="weekly-days" class="block font-semibold"
          >Anzahl der Unterrichtstage pro Woche</label
        >
        <select
          v-model.number="weeklyDays"
          id="weekly-days"
          class="form-input mt-1 block w-full"
        >
          <option v-for="n in 5" :key="n" :value="n">
            {{ n }}
          </option>
        </select>
      </div>

      <div>
        <label for="daily-hours" class="block font-semibold"
          >Tägliche Unterrichtsstunden</label
        >

        <input
          type="number"
          v-model.number="dailyHours"
          id="daily-hours"
          min="1"
          max="8"
          class="form-input mt-1 block w-full"
          required
        />
      </div>

      <div>
        <label for="total-hours" class="block font-semibold"
          >Gesamtanzahl der Unterrichtsstunden</label
        >
        <input
          type="number"
          v-model.number="totalHours"
          id="total-hours"
          min="1"
          max="1000"
          class="form-input mt-1 block w-full"
          required
        />
      </div>

      <Calendar
        :holidays="holidays[selectedState]"
        :teacherAbsences="teacherAbsences"
      />

      <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded">
        Enddatum berechnen
      </button>
    </form>

    <div v-if="endDate" class="mt-6">
      <h2 class="text-2xl font-bold">Kurs Bitiş Tarihi</h2>
      <p class="text-xl">{{ formatDate(endDate) }}</p>
    </div>

    <Schedule
      :startDate="startDate"
      :endDate="endDate"
      :dailyHours="dailyHours"
      :totalHours="totalHours"
      :weeklyDays="weeklyDays"
      :holidays="holidays[selectedState]"
      :teacherAbsences="teacherAbsences"
    />
  </div>
</template>

<script>
import Calendar from "@/components/Calendar.vue";
import Schedule from "@/components/Schedule.vue";

export default {
  components: {
    Calendar,
    Schedule,
  },
  data() {
    return {
      states: [
        { value: "rlp", text: "RLP" },
        { value: "bw", text: "BW" },
        { value: "sar", text: "SAR" },
      ],
      selectedState: "rlp",
      startDate: "",
      weeklyDays: 5, // Haftalık ders gün sayısı
      dailyHours: 4,
      totalHours: 500,
      endDate: null,
      holidays: {
        rlp: [
          "2024-10-03",
          "2024-11-01",
          "2024-12-24",
          "2024-12-25",
          "2024-12-26",
          "2024-12-31",
          "2025-01-01",
          "2025-01-06",
        ],
        bw: ["2024-01-01", "2024-03-15", "2024-08-30"],
        sar: ["2024-01-01", "2024-07-01", "2024-10-29"],
      },
      teacherAbsences: ["2024-05-10", "2024-06-15"],
    };
  },
  methods: {
    calculateEndDate() {
      let hoursLeft = this.totalHours;
      let currentDate = new Date(this.startDate);
      const maxWeeklyDays = [1, 2, 3, 4, 5]; // Haftalık ders günleri (Pazartesi - Cuma)
      const activeDays = maxWeeklyDays.slice(0, this.weeklyDays);

      while (hoursLeft > 0) {
        currentDate.setDate(currentDate.getDate() + 1);
        const dayOfWeek = currentDate.getDay();
        const formattedDate = currentDate.toISOString().split("T")[0];

        if (
          activeDays.includes(dayOfWeek) &&
          !this.holidays[this.selectedState].includes(formattedDate) &&
          !this.teacherAbsences.includes(formattedDate)
        ) {
          hoursLeft -= this.dailyHours;
        }
      }

      this.endDate = currentDate;
    },
    formatDate(date) {
      return date.toLocaleDateString();
    },
  },
};
</script>

<style scoped>
.container {
  max-width: 600px;
}

.form-input {
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 0.5rem;
}

.form-input:focus {
  outline: none;
  border-color: #3182ce;
  box-shadow: 0 0 0 1px #3182ce;
}
</style>
