<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <q-input v-model="tempData.name" label="姓名" />
        <q-input v-model.number="tempData.age" label="年齡" />
        <q-btn color="primary" class="q-mt-md" @click="handleCreate()">新增</q-btn>
      </div>

      <q-table flat bordered ref="tableRef" :rows="blockData" :columns="(tableConfig as QTableProps['columns'])"
        row-key="id" hide-pagination separator="cell" :rows-per-page-options="[0]">
        <template v-slot:header="props">
          <q-tr :props="props">
            <q-th v-for="col in props.cols" :key="col.name" :props="props">
              {{ col.label }}
            </q-th>
            <q-th></q-th>
          </q-tr>
        </template>

        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td v-for="col in props.cols" :key="col.name" :props="props" style="min-width: 120px">
              <div>{{ col.value }}</div>
            </q-td>
            <q-td class="text-right" auto-width v-if="tableButtons.length > 0">
              <q-btn @click="handleClickOption(btn, props.row)" v-for="(btn, index) in tableButtons" :key="index"
                size="sm" color="grey-6" round dense :icon="btn.icon" class="q-ml-md" padding="5px 5px">
                <q-tooltip transition-show="scale" transition-hide="scale" anchor="top middle" self="bottom middle"
                  :offset="[10, 10]">
                  {{ btn.label }}
                </q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>

        <template v-slot:no-data="{ icon }">
          <div class="full-width row flex-center items-center text-primary q-gutter-sm" style="font-size: 18px">
            <q-icon size="2em" :name="icon" />
            <span> 無相關資料 </span>
          </div>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios';
import { QTableProps } from 'quasar';
import { ref } from 'vue';
interface btnType {
  label: string;
  icon: string;
  status: string;
}
const blockData = ref([
  {
    name: 'test',
    age: 25,
  },
]);
const tableConfig = ref([
  {
    label: '姓名',
    name: 'name',
    field: 'name',
    align: 'left',
  },
  {
    label: '年齡',
    name: 'age',
    field: 'age',
    align: 'left',
  },
]);
const tableButtons = ref([
  {
    label: '編輯',
    icon: 'edit',
    status: 'edit',
  },
  {
    label: '刪除',
    icon: 'delete',
    status: 'delete',
  },
]);

const tempData = ref({
  name: '',
  age: '',
});

function handleClickOption(btn, data) {
  console.log('btn', btn.status);
  console.log('data', data);
  data.age = 100;
  if (btn.status === 'edit') {
    const defaultName = data.name || '';
    const defaultAge = data.age || '';
    const newName = prompt('Enter new name:', defaultName);
    const newAge = prompt('Enter new age:', defaultAge);

    if (newName !== null) {
      data.name = newName;
    }

    if (newAge !== null) {
      data.age = Number(newAge);
    }
    axios.patch('https://demo.mercuryfire.com.tw:49110/crudTest', data)
      .then(res => {
        handleGet();
      })
      .catch(err => {
        console.log('err', err);
      })
  } else {
    // console.log('data', data.id);
    // return
    axios.delete(`https://demo.mercuryfire.com.tw:49110/crudTest/${data.id}`)
      .then(res => {
        handleGet();
      })
      .catch(err => {
        console.log('err', err);
      })
  }
}
function handleGet() {
  axios.get('https://demo.mercuryfire.com.tw:49110/crudTest/a').then((response) => {
    // console.log(response.data)
    blockData.value = response.data.result;
  })
}
function handleCreate() {
  axios.post('https://demo.mercuryfire.com.tw:49110/crudTest', tempData.value, {
    headers: {
      'Content-Type': 'application/json'
    }
  })
    .then(response => {
      console.log(response);
      handleGet();
    })
    .catch(error => {
      console.error('Error creating:', error);
    });
}

handleGet();
</script>

<style lang="scss" scoped>
.q-table th {
  font-size: 20px;
  font-weight: bold;
}

.q-table tbody td {
  font-size: 18px;
}
</style>
