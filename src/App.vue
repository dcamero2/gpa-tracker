<template>
  <!-- LOGGED OUT VIEW -->
<div v-if="!currentUser" class="container">
  <h1>GPA Tracker</h1>

  <div class="auth-form">
  <input v-model="username" placeholder="Username" />
  <input v-model="password" type="password" placeholder="Password" />

    <div class="auth-buttons">
      <button @click="signup">Sign Up</button>
      <button @click="login">Login</button>
    </div>
  </div>
</div>

  <!-- LOGGED IN VIEW -->
<div v-else class="app-layout">

  <div class="top-bar">
    <div class="welcome">
      Welcome, {{ currentUser }}
    </div>

    <button class="logout" @click="logout">
      Logout
    </button>
  </div>

  <!-- MAIN CONTENT -->
  <div class="container">
    <h1>GPA Tracker</h1>

    <div class="gpa-form">
  <input v-model="name" placeholder="Assignment Name" />
  <input v-model="grade" type="number" placeholder="Grade %" />
  <input v-model="weight" type="number" placeholder="Weight %" />
      <button @click="addAssignment">Add</button>
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
        <tr v-for="a in assignments" :key="a.id">
          <td>{{ a.name }}</td>
          <td>{{ a.grade }}%</td>
          <td>{{ a.weight }}%</td>
          <td>
            <button @click="deleteAssignment(a.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    <div class="grade-container">
      <div :class="['grade-bubble', gradeColor]">
        Final Grade: {{ finalGrade }}%
      </div>
    </div>
  </div>

</div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

/* ---------------- AUTH ---------------- */
const username = ref('')
const password = ref('')
const currentUser = ref(localStorage.getItem('currentUser'))

const signup = () => {
  const users = JSON.parse(localStorage.getItem('users') || '[]')

  const exists = users.find(u => u.username === username.value)
  if (exists) return alert('User already exists')

  users.push({
    username: username.value,
    password: password.value
  })

  localStorage.setItem('users', JSON.stringify(users))
  alert('Account created!')
}

const login = () => {
  const users = JSON.parse(localStorage.getItem('users') || '[]')

  const user = users.find(
    u => u.username === username.value && u.password === password.value
  )

  if (!user) return alert('Invalid login')

  localStorage.setItem('currentUser', user.username)
  currentUser.value = user.username
}

const logout = () => {
  localStorage.removeItem('currentUser')
  currentUser.value = null
}

/* ---------------- GPA DATA ---------------- */
const name = ref('')
const grade = ref('')
const weight = ref('')

const storageKey = computed(() => `assignments_${currentUser.value}`)

const assignments = ref(
  JSON.parse(localStorage.getItem(storageKey.value) || '[]')
)

/* reload when user changes */
watch(currentUser, () => {
  assignments.value = JSON.parse(
    localStorage.getItem(storageKey.value) || '[]'
  )
})

const addAssignment = () => {
  if (!name.value || !grade.value || !weight.value) return

  assignments.value.push({
    id: crypto.randomUUID(),
    name: name.value,
    grade: Number(grade.value),
    weight: Number(weight.value)
  })

  name.value = ''
  grade.value = ''
  weight.value = ''
}

const deleteAssignment = (id) => {
  assignments.value = assignments.value.filter(a => a.id !== id)
}

watch(
  assignments,
  (val) => {
    localStorage.setItem(storageKey.value, JSON.stringify(val))
  },
  { deep: true }
)

/* ---------------- GPA CALC ---------------- */
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

const gradeColor = computed(() => {
  if (finalGrade.value >= 90) return 'green'
  if (finalGrade.value >= 80) return 'lightgreen'
  if (finalGrade.value >= 70) return 'yellow'
  return 'red'
})
</script>
