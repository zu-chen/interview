<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <q-input v-model="tempData.name" label="姓名" />
        <q-input v-model="tempData.age" label="年齡" />
        <q-btn color="primary" class="q-mt-md" @click="addData">新增</q-btn>
      </div>

      <q-table
        flat
        bordered
        ref="tableRef"
        :rows="blockData"
        :columns="(tableConfig as QTableProps['columns'])"
        row-key="id"
        hide-pagination
        separator="cell"
        :rows-per-page-options="[0]"
      >
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
            <q-td
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              style="min-width: 120px"
            >
              <div>{{ col.value }}</div>
            </q-td>
            <q-td class="text-right" auto-width v-if="tableButtons.length > 0">
              <q-btn
                @click="handleClickOption(btn, props.row)"
                v-for="(btn, index) in tableButtons"
                :key="index"
                size="sm"
                color="grey-6"
                round
                dense
                :icon="btn.icon"
                class="q-ml-md"
                padding="5px 5px"
              >
                <q-tooltip
                  transition-show="scale"
                  transition-hide="scale"
                  anchor="top middle"
                  self="bottom middle"
                  :offset="[10, 10]"
                >
                  {{ btn.label }}
                </q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>
        <template v-slot:no-data="{ icon }">
          <div
            class="full-width row flex-center items-center text-primary q-gutter-sm"
            style="font-size: 18px"
          >
            <q-icon size="2em" :name="icon" />
            <span> 無相關資料 </span>
          </div>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import { QTableProps } from 'quasar';
import { ref, onMounted } from 'vue';
import axios from 'axios';

interface btnType {
  label: string;
  icon: string;
  status: string;
}

//api
const apiUrl = 'https://demo.mercuryfire.com.tw:49110/crudTest';

const blockData = ref([]);
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
  switch (btn) {
    case 'edit':
      updateData(data);
      break;
    case 'delete':
      deleteData(data.id);
      break;
  }
}

// GET
async function fetchData() {
  try {
    const res = await axios.get(`${apiUrl}/a`);
    blockData.value = res.data.result;
  } catch (e) {
    console.log(e);
  }
}

//ADD
async function addData() {
  try {
    const res = await axios.post(apiUrl, {
      name: tempData.value.name,
      age: Number(tempData.value.age),
    });
    console.log('res', res);
    fetchData();
  } catch (e) {
    console.log(e);
  }
}

//UPDATE
async function updateData(data) {
  try {
    const res = await axios.patch(apiUrl, data);
    fetchData();
  } catch (e) {
    console.log(e);
  }
}

//DELETE
async function deleteData(id) {
  try {
    const res = await axios.delete(`${apiUrl}/${id}`);
    fetchData();
  } catch (e) {
    console.log(e);
  }
}

onMounted(() => {
  fetchData();
});
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
