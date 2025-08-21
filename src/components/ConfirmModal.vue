<script setup>
const props = defineProps({
  dialog: Boolean,
  title: {
    type: String,
    default: 'Konfirmasi Hapus'
  },
  message: {
    type: String,
    default: 'Apakah Anda yakin ingin menghapus data ini?'
  }
})
const emit = defineEmits(['update:dialog', 'confirm'])

const close = () => emit('update:dialog', false)
const confirm = () => {
  emit('confirm')
  close()
}
</script>

<template>
  <v-dialog v-model="props.dialog" max-width="400" @update:modelValue="val => emit('update:dialog', val)">
    <v-card>
      <v-card-title class="text-h6">{{ props.title }}</v-card-title>
      <v-card-text>{{ props.message }}</v-card-text>
      <v-card-actions>
        <v-spacer />
        <button class="btn btn-outline-danger rounded-pill" @click="close">Batal</button>
        <button class="btn btn-danger rounded-pill" @click="confirm">Hapus</button>
        <!-- <v-btn color="grey" variant="text" @click="close">Batal</v-btn>
        <v-btn color="red" variant="text" @click="confirm">Hapus</v-btn> -->
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>