<script setup lang="ts">
import type { FormInstance } from 'element-plus'

function calculateBMI(height: number, weight: number) {
  // 计算BMI
  height = height / 100
  var bmi = weight / (height * height)
  return bmi.toFixed(1)
}
// BMI正常范围（男性）
function calculateNormalWeight(height: number) {
  height = height / 100
  // 根据BMI范围计算正常体重的区间
  let bmiRange = [18.5, 24]
  let minWeight = bmiRange[0] * height * height
  let maxWeight = bmiRange[1] * height * height

  // 返回正常体重的区间
  return { minWeight: minWeight.toFixed(1), maxWeight: maxWeight.toFixed(1) }
}

// 示例用法
function getBMIType(bmi) {
  var status;
  if (bmi < 18.5) 
    status = '偏瘦';
  else if (bmi >= 18.5 && bmi < 24.0) 
    status = '正常';
  else if (bmi >= 24.0 && bmi < 270) 
    status = '过重';
  else status = '肥胖';
  return status+" ";
}

interface BMI {
  t: string
  r: string
}
const person = reactive({
  weight: 0,
  height: 0,
  type: '',
})

//表格样式
const tableRowClassName = ({ rowIndex }: { row: BMI; rowIndex: number }) => {
  if (rowIndex === 0) {
    return 'bg-stone-300'
  } else if (rowIndex === 1) {
    return 'bg-green-400'
  } else if (rowIndex === 2) {
    return 'bg-red-300'
  } else if (rowIndex === 3) {
    return 'bg-orange-600'
  }
  return ''
}

const tableData: BMI[] = [
  {
    r: '10.≤18.4',
    t: '偏瘦',
  },
  {
    t: '正常',
    r: '18.5≤23.9',
  },
  {
    t: '过重',
    r: '24.0≤27.9',
  },
  {
    t: '肥胖',
    r: '28.0≤32.0',
  },
]

// Data
const ruleFormRef = ref<FormInstance>()
let bmiResult = ref(0)
let startWeight = ref(0)
let endWeight = ref(0)
let bmiResultClass = ref('')
let bmiType = ref('')

watch(bmiResult, (newValue) => {
  if (newValue < 18.5) bmiResultClass.value = 'bg-stone-300'
  else if (newValue >= 18.5 && newValue < 24.0)
    bmiResultClass.value = 'bg-green-400'
  else if (newValue >= 24.0 && newValue < 29.9)
    bmiResultClass.value = 'bg-red-300'
  else bmiResultClass.value = 'bg-orange-600'
})
const submitForm = async (formEl: FormInstance | undefined) => {
  console.log(formEl)

  if (!formEl) return
  await formEl.validate((valid, fields) => {
    if (valid) {
      let bmi = calculateBMI(person.height, person.weight)
      bmiResult.value = bmi
      let a = calculateNormalWeight(person.height)
      startWeight.value = a.minWeight
      endWeight.value = a.maxWeight

      bmiType.value = getBMIType(bmi);
      console.log('submit!')
    } else {
      console.log('error submit!', fields)
    }
  })
}
</script>

<template>
  <div class="m-1">
    <h4 class="text-3xl font-bold p-1 text-center">BMI 指数计算</h4>
    <p class="text-center">身高您已经无法再PB，请合理管理您的体重.</p>
    <div>
      <el-form
        ref="ruleFormRef"
        label-position="left"
        label-width="140px"
        class="mt-5"
        :model="person"
        style="width: 100%"
      >
        <el-form-item label="您的身高（CM）：">
          <el-input v-model="person.height" @change="submitForm" />
        </el-form-item>
        <el-form-item label="您的体重（KG）：">
          <el-input v-model="person.weight" @change="submitForm" />
        </el-form-item>
      </el-form>
    </div>
    <div>
      <el-button
        type="primary"
        class="footer-center w-full mb-3"
        @click="submitForm(ruleFormRef)"
      >
        开始计算
      </el-button>
    </div>
    <div
      class="grid grid-rows-2 grid-cols-3 grid-flow-col gap-1 shadow-xl"
      style="width: 342px; height: 228px"
    >
      <div class="row-span-2 col-span-2 shadow-inner rounded-md border-gray-50">
        <h2
          class="font-bold text-center text-3xl rounded-md"
          :class="bmiResultClass"
        >
          {{bmiType}}{{ bmiResult }}
        </h2>
        <el-table
          :data="tableData"
          size="small"
          :row-class-name="tableRowClassName"
        >
          <el-table-column
            prop="t"
            label="体型分类"
            width="110"
          />
          <el-table-column
            prop="r"
            label="BMI范围"
            width="110"
          />
        </el-table>
      </div>
      <div class="bg-green-200 rounded-md">
        <h2 class="font-bold text-center">建议体重</h2>
        <span class="inline-block align-middle text-center w-full">
          {{ startWeight }}~{{ endWeight }} KG
        </span>
      </div>
      <span class="text-center rounded-md">查看更多
       
      </span>
    </div>
  </div>
</template>

<style>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
