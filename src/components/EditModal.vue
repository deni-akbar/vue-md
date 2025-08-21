<script setup>
import { ref, computed, watch } from 'vue'
const props = defineProps({
  dialog: Boolean,
  form: Object,
  formValid: Boolean,
  nilaiLabel: String,
})
const emit = defineEmits(['update:dialog', 'submit'])

const localFormRef = ref(null)
defineExpose({ formRef: localFormRef })
</script>

<template>
  <v-container>
    <v-dialog v-model="props.dialog" max-width="500" @update:modelValue="val => emit('update:dialog', val)">
      <v-card>
        <v-btn icon class="close-btn" @click="emit('update:dialog', false)">
          <v-icon>mdi-close</v-icon>
        </v-btn>
        <v-card-title class="headline">Edit Diskon</v-card-title>
        <v-card-text>
          <v-form ref="localFormRef" v-model="props.formValid">
            <v-text-field
             variant="outlined"
              v-model="props.form.namaDiskon"
              label="Nama Diskon"
              :rules="[v => !!v || 'Nama Diskon tidak boleh kosong']"
              required
            ></v-text-field>

            <v-row align="center" dense>
              <v-col cols="8">
                <v-text-field
                 variant="outlined"
                 class="mt-3"
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
          <!-- <v-btn
            class="modal-button"
            color="primary"
            block
            large
            @click="emit('submit')"
          >
            Simpan Perubahan
          </v-btn> -->
          <button 
            type="button" 
            class="btn btn-success w-100 rounded-pill" 
            @click="$emit('submit')"
          >
           <span class="text-light">Simpan</span> 
          </button>
        </v-card-actions>
  </v-form>
        </v-card-text>

      </v-card>
    </v-dialog>
  </v-container>
</template>
