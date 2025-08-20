<script setup>
import { ref, computed, watch } from 'vue'
const props = defineProps({
  dialog: Boolean,
  form: Object,
  formValid: Boolean,
  nilaiLabel: String,
})
const emit = defineEmits(['update:dialog', 'submit'])

const formRef = ref(null)
defineExpose({ formRef })
</script>

<template>
  <v-container>
    <v-dialog v-model="props.dialog" max-width="500" @update:modelValue="val => emit('update:dialog', val)">
      <v-card>
        <v-btn icon class="close-btn" @click="emit('update:dialog', false)">
          <v-icon>mdi-close</v-icon>
        </v-btn>
        <v-card-title class="headline">Tambah Diskon</v-card-title>
        <v-card-text>
          <v-form ref="formRef" v-model="props.formValid">
            <v-text-field
              v-model="props.form.namaDiskon"
              label="Nama Diskon"
              :rules="[v => !!v || 'Nama Diskon tidak boleh kosong']"
              required
            ></v-text-field>

            <v-row align="center" dense>
              <v-col cols="8">
                <v-text-field
                  v-model="props.form.nilaiDiskon"
                  :label="props.nilaiLabel"
                  type="number"
                  min="0"
                  :rules="[v => v !== null && v !== '' || 'Nilai Diskon tidak boleh kosong']"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="4">
                <v-switch
                  v-model="props.form.tipeDiskon"
                  :label="props.form.tipeDiskon === 'persen' ? '%' : 'Rp'"
                  :true-value="'persen'"
                  :false-value="'rp'"
                ></v-switch>
              </v-col>
            </v-row>
          <v-card-actions class="pa-0">
          <v-btn
            class="modal-button"
            color="success"
            block
            large
            @click="emit('submit')"
          >
            Simpan
          </v-btn>
        </v-card-actions>
  </v-form>
        </v-card-text>

      </v-card>
    </v-dialog>
  </v-container>
</template>
