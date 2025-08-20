<script setup>
import { ref, watch } from 'vue'
const props = defineProps({
  discounts: Array,
  selectedOutlet: String
})
const emit = defineEmits(['edit', 'check'])

const checkedIds = ref([])

watch(checkedIds, (val) => {
  emit('check', val)
}, { deep: true })
</script>

<template>
  <div>
    <v-table class="discount-table">
      <thead>
        <tr>
          <th style="width:40px; text-align:center;"></th>
          <th>Nama Diskon</th>
          <th>Nilai</th>
          <th style="width:60px; text-align:center;"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="diskon in props.discounts" :key="diskon._id">
          <td>
            <v-checkbox
              class="mr-5"
              hide-details
              density="compact"
              color="primary"
              :model-value="checkedIds.includes(diskon._id)"
              @update:model-value="val => {
                if (val) {
                  if (!checkedIds.includes(diskon._id)) checkedIds.push(diskon._id)
                } else {
                  checkedIds.splice(checkedIds.indexOf(diskon._id), 1)
                }
              }"
            />
          </td>
          <td>{{ diskon.namaDiskon }}</td>
          <td>{{ diskon.tipeDiskon === 'persen' ? diskon.nilaiDiskon + '%' : 'Rp ' + diskon.nilaiDiskon }}</td>
          <td style="text-align:center;">
            <v-btn icon size="small" variant="flat" color="white" @click="emit('edit', diskon)">
              <v-icon color="black">mdi-pencil</v-icon>
            </v-btn>
          </td>
        </tr>
      </tbody>
    </v-table>
  </div>
</template>



