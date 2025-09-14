<!-- <template>
    <div class="h-screen w-full flex flex-col px-2 justify-center items-center">
        <p>Todo: Implement dashboard as you want.</p>
    </div>
</template>

<script setup lang="ts">
definePageMeta({
    layout: 'hrm'
})
</script>
 -->

 <template>
  <div class="min-h-screen w-full bg-gray-100 p-6">
    <h1 class="text-3xl font-bold mb-6 text-gray-800">HR Dashboard</h1>

    <!-- Summary Cards -->
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 mb-8">
      <div class="bg-white p-6 rounded-lg shadow flex items-center">
        <div class="bg-blue-100 p-4 rounded-full mr-4">
          <i class="fas fa-users text-blue-600 text-xl"></i>
        </div>
        <div>
          <p class="text-gray-500 text-sm">Total Employees</p>
          <p class="text-2xl font-bold">{{ employees.length }}</p>
        </div>
      </div>

      <div class="bg-white p-6 rounded-lg shadow flex items-center">
        <div class="bg-green-100 p-4 rounded-full mr-4">
          <i class="fas fa-user-check text-green-600 text-xl"></i>
        </div>
        <div>
          <p class="text-gray-500 text-sm">Active Employees</p>
          <p class="text-2xl font-bold">{{ activeCount }}</p>
        </div>
      </div>

      <div class="bg-white p-6 rounded-lg shadow flex items-center">
        <div class="bg-purple-100 p-4 rounded-full mr-4">
          <i class="fas fa-venus-mars text-purple-600 text-xl"></i>
        </div>
        <div>
          <p class="text-gray-500 text-sm">Gender Ratio</p>
          <p class="text-2xl font-bold">{{ genderRatio }}</p>
        </div>
      </div>
    </div>

    <!-- Charts -->
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
      <div class="bg-white p-6 rounded-lg shadow">
        <h2 class="text-lg font-semibold mb-4 text-gray-700">Gender Distribution</h2>
        <canvas ref="genderChart"></canvas>
      </div>

      <div class="bg-white p-6 rounded-lg shadow">
        <h2 class="text-lg font-semibold mb-4 text-gray-700">Status Overview</h2>
        <canvas ref="statusChart"></canvas>
      </div>

      <div class="bg-white p-6 rounded-lg shadow">
        <h2 class="text-lg font-semibold mb-4 text-gray-700">Role Distribution</h2>
        <canvas ref="roleChart"></canvas>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from 'vue'
import Chart from 'chart.js/auto'

definePageMeta({
  layout: 'hrm'
})

// Dữ liệu nhân viên mẫu (có thể thay bằng API hoặc store)
const employees = ref([
  { firstName: 'John', lastName: 'Doe', gender: 'Male', workEmail: 'john@company.com', personalEmail: 'john@gmail.com', dob: '1990-01-01', joinDate: '2023-01-10', roles: 'Developer', status: 'Active' },
  { firstName: 'Jane', lastName: 'Smith', gender: 'Female', workEmail: 'jane@company.com', personalEmail: 'jane@gmail.com', dob: '1992-03-15', joinDate: '2023-02-20', roles: 'HR Manager', status: 'Inactive' },
  { firstName: 'Alice', lastName: 'Brown', gender: 'Female', workEmail: 'alice@company.com', personalEmail: 'alice@gmail.com', dob: '1988-07-22', joinDate: '2023-03-05', roles: 'Developer', status: 'Active' },
  { firstName: 'Bob', lastName: 'Lee', gender: 'Male', workEmail: 'bob@company.com', personalEmail: 'bob@gmail.com', dob: '1995-11-30', joinDate: '2023-04-12', roles: 'Designer', status: 'Active' }
])

// Tính toán dữ liệu
const activeCount = ref(0)
const genderRatio = ref('0:0')

const updateStats = () => {
  const males = employees.value.filter(e => e.gender === 'Male').length
  const females = employees.value.filter(e => e.gender === 'Female').length
  genderRatio.value = `${males}:${females}`
  activeCount.value = employees.value.filter(e => e.status === 'Active').length
}

// Chart refs
const genderChart = ref()
const statusChart = ref()
const roleChart = ref()

const renderCharts = () => {
  const genderData = {
    labels: ['Male', 'Female'],
    datasets: [{
      data: [
        employees.value.filter(e => e.gender === 'Male').length,
        employees.value.filter(e => e.gender === 'Female').length
      ],
      backgroundColor: ['#60A5FA', '#F472B6']
    }]
  }

  const statusData = {
    labels: ['Active', 'Inactive'],
    datasets: [{
      data: [
        employees.value.filter(e => e.status === 'Active').length,
        employees.value.filter(e => e.status === 'Inactive').length
      ],
      backgroundColor: ['#34D399', '#F87171']
    }]
  }

  const roleCounts: Record<string, number> = {}
  employees.value.forEach(e => {
    roleCounts[e.roles] = (roleCounts[e.roles] || 0) + 1
  })

  const roleData = {
    labels: Object.keys(roleCounts),
    datasets: [{
      data: Object.values(roleCounts),
      backgroundColor: ['#FBBF24', '#A78BFA', '#4ADE80', '#F87171']
    }]
  }

  new Chart(genderChart.value, { type: 'doughnut', data: genderData })
  new Chart(statusChart.value, { type: 'doughnut', data: statusData })
  new Chart(roleChart.value, { type: 'bar', data: roleData })
}

onMounted(() => {
  updateStats()
  renderCharts()
})

// Đồng bộ khi dữ liệu thay đổi
watch(employees, () => {
  updateStats()
  renderCharts()
})
</script>