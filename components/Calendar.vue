<template>
  <div>
    <h3 class="text-xl font-semibold mb-2">
      Feiertage und Lehrkraftfreistellungstage
    </h3>
    <div class="mb-4">
      <label for="holiday-date" class="block font-semibold"
        >Neuen Feiertag hinzufügen</label
      >
      <input
        type="date"
        v-model="newHoliday"
        id="holiday-date"
        class="form-input mt-1 block w-full"
      />
      <button
        @click="addHoliday"
        class="mt-2 bg-green-500 text-white px-4 py-2 rounded"
      >
        Feiertag hinzufügen
      </button>
    </div>

    <div class="mb-4">
      <label for="absence-date" class="block font-semibold"
        >Neuen Lehrkraftfreistellungstag hinzufügen</label
      >
      <input
        type="date"
        v-model="newAbsence"
        id="absence-date"
        class="form-input mt-1 block w-full"
      />
      <button
        @click="addAbsence"
        class="mt-2 bg-green-500 text-white px-4 py-2 rounded"
      >
        Freistellungstag hinzufügen
      </button>
    </div>

    <div>
      <h4 class="font-semibold mb-2">Feiertage:</h4>
      <ul>
        <li v-for="(holiday, index) in holidays" :key="index">
          {{ holiday }}
          <button
            @click="removeHoliday(index)"
            class="button-remove text-red-500 ml-2"
          >
            Löschen
          </button>
        </li>
      </ul>
    </div>

    <div>
      <h4 class="font-semibold mb-2">Lehrkraftfreistellungstage:</h4>
      <ul>
        <li v-for="(absence, index) in teacherAbsences" :key="index">
          {{ absence }}
          <button
            @click="removeAbsence(index)"
            class="text-red-500 ml-2 text-sm bg-red-300"
          >
            Löschen
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    holidays: Array,
    teacherAbsences: Array,
  },
  data() {
    return {
      newHoliday: "",
      newAbsence: "",
    };
  },
  methods: {
    addHoliday() {
      if (this.newHoliday && !this.holidays.includes(this.newHoliday)) {
        this.holidays.push(this.newHoliday);
        this.newHoliday = "";
      }
    },
    removeHoliday(index) {
      this.holidays.splice(index, 1);
    },
    addAbsence() {
      if (this.newAbsence && !this.teacherAbsences.includes(this.newAbsence)) {
        this.teacherAbsences.push(this.newAbsence);
        this.newAbsence = "";
      }
    },
    removeAbsence(index) {
      this.teacherAbsences.splice(index, 1);
    },
  },
};
</script>

<style scoped>
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
