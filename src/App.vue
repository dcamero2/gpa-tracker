<template>
  <div class="container">
    <h1>GPA Tracker</h1>

    <div class="form">
      <input
        v-model="name"
        placeholder="Assignment Name"
      />

      <input
        v-model="grade"
        type="number"
        placeholder="Grade %"
      />

      <input
        v-model="weight"
        type="number"
        placeholder="Weight %"
      />

      <button @click="addAssignment">
        Add
      </button>
    </div>

    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Grade</th>
          <th>Weight</th>
          <th>Action</th>
        </tr>
      </thead>

      <tbody>
        <tr
          v-for="(a, index) in assignments"
          :key="index"
        >
          <td>{{ a.name }}</td>
          <td>{{ a.grade }}%</td>
          <td>{{ a.weight }}%</td>

          <td>
            <button @click="deleteAssignment(index)">
              Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <h2>
      Final Grade: {{ finalGrade }}%
    </h2>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const name = ref('')
const grade = ref('')
const weight = ref('')

const assignments = ref([])

// load saved assignments
const saved = localStorage.getItem('assignments')

if (saved) {
  assignments.value = JSON.parse(saved)
}

const addAssignment = () => {
  if (
    name.value === '' ||
    grade.value === '' ||
    weight.value === ''
  ) return

  assignments.value.push({
    name: name.value,
    grade: Number(grade.value),
    weight: Number(weight.value)
  })

  name.value = ''
  grade.value = ''
  weight.value = ''
}

const deleteAssignment = (index) => {
  assignments.value.splice(index, 1)
}

const finalGrade = computed(() => {
  let total = 0
  let weightSum = 0

  assignments.value.forEach(a => {
    total += a.grade * a.weight
    weightSum += a.weight
  })

  return weightSum
    ? Number(total / weightSum).toFixed(2)
    : '0.00'
})

// auto-save assignments
watch(assignments, (newVal) => {
  localStorage.setItem(
    'assignments',
    JSON.stringify(newVal)
  )
}, { deep: true })
</script>

<style>
</style>