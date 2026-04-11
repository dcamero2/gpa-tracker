<template>
  <div class="container">
    <h1>GPA Tracker</h1>

    <div class="form">
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
        <tr v-for="(a, index) in assignments" :key="index">
          <td>{{ a.name }}</td>
          <td>{{ a.grade }}%</td>
          <td>{{ a.weight }}%</td>
          <td>
            <button @click="deleteAssignment(index)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    <h2 :class="gradeColor">
      Final Grade: {{ finalGrade }}%
    </h2>
  </div>
</template>