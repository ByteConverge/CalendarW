<template>
  <div>
    <div class="flex items-center justify-between mb-3">
      <button @click="prevMonth" class="p-2 hover:bg-gray-100 rounded" aria-label="Previous month">
        <PrevIcon class="text-gray-700" />
      </button>
      <div class="text-lg font-medium">{{ monthLabel }}</div>
      <button @click="nextMonth" class="p-2 hover:bg-gray-100 rounded" aria-label="Next month">
        <NextIcon class="text-gray-700" />
      </button>
    </div>

    <div class="grid grid-cols-7 gap-1 text-center text-xs text-gray-600 mb-1">
      <div v-for="d in weekdayLabels" :key="d" :class="['font-medium', d && d.toString().toLowerCase() === 'sun' ? 'text-red-500' : '', 'hover:text-[1rem]','ease-in-out', 'transition-all','duration-1000','cursor-pointer']">{{ d }}</div>
    </div>

    <div class="grid grid-cols-7 gap-1 ">
      <template v-for="(day, idx) in days" :key="idx">
        <button
          @click="selectDay(day)"
          :class="[
            'p-1 rounded',
            isSameDate(day, today) ? 'text-white bg-green-500' : '',
            isSameDate(day, selected) ? 'ring-2 ring-green-400' : '',
            day.getMonth() !== view.getMonth() ? 'text-gray-400' : 'text-gray-800'
          ]"
          class="h-12 flex items-center justify-center"
        >
          <span class="calendar-day">{{ day.getDate() }}</span>
        </button>
      </template>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import PrevIcon from './icons/PrevIcon.vue'
import NextIcon from './icons/NextIcon.vue'

const props = defineProps({ modelValue: { type: Date, default: null } })
const emit = defineEmits(['update:modelValue'])

const today = new Date()
const view = ref(props.modelValue ? new Date(props.modelValue) : new Date(today.getFullYear(), today.getMonth(), 1))
const selected = ref(props.modelValue ? new Date(props.modelValue) : null)

watch(() => props.modelValue, (v) => {
  selected.value = v ? new Date(v) : null
})

function startOfMonth(d){ return new Date(d.getFullYear(), d.getMonth(), 1) }
function endOfMonth(d){ return new Date(d.getFullYear(), d.getMonth() + 1, 0) }

function getMonthMatrix(d){
  const start = startOfMonth(d)
  const startDay = start.getDay() // Sunday=0
  const matrix = []
  let cur = new Date(start)
  cur.setDate(cur.getDate() - startDay)
  for (let i = 0; i < 42; i++) {
    matrix.push(new Date(cur))
    cur = new Date(cur)
    cur.setDate(cur.getDate() + 1)
  }
  return matrix
}

const days = computed(() => getMonthMatrix(view.value))
const monthLabel = computed(() => view.value.toLocaleString(undefined, { month: 'long', year: 'numeric' }))
const weekdayLabels = ['Sun','Mon','Tue','Wed','Thu','Fri','Sat']

function prevMonth(){ view.value = new Date(view.value.getFullYear(), view.value.getMonth() - 1, 1) }
function nextMonth(){ view.value = new Date(view.value.getFullYear(), view.value.getMonth() + 1, 1) }

function selectDay(d){
  const chosen = new Date(d.getFullYear(), d.getMonth(), d.getDate())
  selected.value = chosen
  emit('update:modelValue', chosen)
}

function isSameDate(a, b){
  if (!a || !b) return false
  return a.getFullYear() === b.getFullYear() && a.getMonth() === b.getMonth() && a.getDate() === b.getDate()
}
</script>
