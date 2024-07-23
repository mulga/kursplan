<template>
  <div v-if="schedule.length" class="mt-8">
    <h3 class="text-xl font-semibold mb-4">Kurs Programı</h3>
    <div class="space-y-4">
      <div
        v-for="(block, index) in schedule"
        :key="index"
        class="p-4 bg-gray-100 rounded"
      >
        <h4 class="font-semibold">Bölüm {{ index + 1 }}</h4>
        <p>
          {{ block.startDate }} - {{ block.endDate }}
          <span class="font-semibold">(Toplam {{ block.hours }} saat)</span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    startDate: String,
    endDate: Date,
    dailyHours: Number,
    totalHours: Number,
    weeklyDays: Number,
    holidays: Array,
    teacherAbsences: Array,
  },
  computed: {
    schedule() {
      if (!this.endDate) return [];

      const blocks = [];
      let currentDate = new Date(this.startDate);
      let hoursLeft = this.totalHours;
      let blockHours = 50;
      let currentBlockHours = 0;
      let currentBlockStartDate = currentDate.toISOString().split("T")[0];
      const maxWeeklyDays = [1, 2, 3, 4, 5]; // Haftalık ders günleri (Pazartesi - Cuma)
      const activeDays = maxWeeklyDays.slice(0, this.weeklyDays);

      while (hoursLeft > 0) {
        const dayOfWeek = currentDate.getDay();
        const formattedDate = currentDate.toISOString().split("T")[0];

        if (
          activeDays.includes(dayOfWeek) &&
          !this.holidays.includes(formattedDate) &&
          !this.teacherAbsences.includes(formattedDate)
        ) {
          currentBlockHours += this.dailyHours;
          hoursLeft -= this.dailyHours;
        }

        if (currentBlockHours >= blockHours || hoursLeft <= 0) {
          blocks.push({
            startDate: currentBlockStartDate,
            endDate: formattedDate,
            hours: currentBlockHours,
          });
          currentBlockStartDate = formattedDate;
          currentBlockHours = 0;
        }

        currentDate.setDate(currentDate.getDate() + 1);
      }

      return blocks;
    },
  },
};
</script>
