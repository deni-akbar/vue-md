<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import Empty from './components/Empty.vue'
import List from './components/List.vue'
import Modal from './components/Modal.vue'
import EditModal from './components/EditModal.vue'
import ConfirmModal from './components/ConfirmModal.vue'

const confirmDialog = ref(false)
// Ganti dengan endpoint API kamu dari crudcrud.com
const API_URL = 'https://crudcrud.com/api/e4448f5babfb4c8ab46cd28d79ffc109/discounts'

const selectedOutlet = ref("kopi")
const dialog = ref(false)
const editDialog = ref(false)
const editForm = ref({
  namaDiskon: '',
  nilaiDiskon: null,
  tipeDiskon: 'persen',
  outlet: 'kopi',
  _id: null
})
const editFormRef = ref(null)
const editModalRef = ref(null)
const editFormValid = ref(false)
// State untuk data diskon
const discounts = ref([])
const loadingDiscounts = ref(false)
const checkedDiscounts = ref([])

// Snackbar
const snackbar = ref(false)
const snackbarText = ref('')
const snackbarColor = ref('success')



// Fetch data diskon dari API
const fetchDiscounts = async () => {
  loadingDiscounts.value = true
  try {
    const res = await fetch(API_URL)
    if (!res.ok) throw new Error('Gagal mengambil data diskon!')
    const data = await res.json()
    discounts.value = data.filter(d => d.outlet === selectedOutlet.value)
  } catch (err) {
    discounts.value = []
    console.error(err)
  } finally {
    loadingDiscounts.value = false
  }
}

// Fetch saat komponen mounted dan saat outlet berubah
onMounted(fetchDiscounts)
watch([selectedOutlet], fetchDiscounts)

// Form data
const form = ref({
  namaDiskon: '',
  nilaiDiskon: null,
  tipeDiskon: 'persen',
  outlet: 'kopi'
})

// Label input sesuai tipe
const nilaiLabel = computed(() => form.value.tipeDiskon === 'persen' ? 'Persen Diskon (%)' : 'Nominal Diskon (Rp)')
const editNilaiLabel = computed(() => editForm.value.tipeDiskon === 'persen' ? 'Persen Diskon (%)' : 'Nominal Diskon (Rp)')

// Buka modal tambah
const addDiscount = () => {
  dialog.value = true
}

// Buka modal edit
const openEdit = (diskon) => {
  editForm.value = { ...diskon }
  editDialog.value = true
}

// Tampilkan snackbar
const showSnackbar = (text, color = 'success') => {
  snackbarText.value = text
  snackbarColor.value = color
  snackbar.value = true
}

// Submit form ke API
const modalRef = ref(null)
const formValid = ref(false)

const submitForm = async () => {
  // Tunggu hasil validasi form
  if (!modalRef.value || !modalRef.value.formRef) return;
  const isValid = await modalRef.value.formRef.validate();
  if (!isValid.valid) return; // Hentikan jika form tidak valid

  try {
    const payload = {
      namaDiskon: form.value.namaDiskon,
      nilaiDiskon: form.value.nilaiDiskon,
      tipeDiskon: form.value.tipeDiskon,
      outlet: selectedOutlet.value
    }

    const res = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    })

    if (!res.ok) {
      const errText = await res.text();
      console.error('API Error:', res.status, errText);
      showSnackbar('Gagal menambahkan diskon!', 'error')
      throw new Error('Gagal menambahkan diskon!');
    }

    const data = await res.json()
    showSnackbar(`Diskon "${form.value.namaDiskon}" berhasil ditambahkan!`, 'success')

    form.value = { namaDiskon: '', nilaiDiskon: null, tipeDiskon: 'persen', outlet: selectedOutlet.value }
    dialog.value = false
    await fetchDiscounts() // refresh data setelah tambah
  } catch (err) {
    console.error('Submit Error:', err)
    showSnackbar('Terjadi kesalahan saat menambahkan diskon.', 'error')
  }
}

// Submit edit form ke API
const submitEditForm = async () => {
  if (!editModalRef.value || !editModalRef.value.formRef) return;
  const isValid = await editModalRef.value.formRef.validate();
  if (!isValid.valid) return;

  try {
    const payload = {
      namaDiskon: editForm.value.namaDiskon,
      nilaiDiskon: editForm.value.nilaiDiskon,
      tipeDiskon: editForm.value.tipeDiskon,
      outlet: editForm.value.outlet
    }
    const url = `${API_URL}/${editForm.value._id}`;
    const res = await fetch(url, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload)
    })
    if (!res.ok) {
      showSnackbar('Gagal mengedit diskon!', 'error')
      throw new Error('Gagal mengedit diskon!')
    }
    showSnackbar(`Diskon "${editForm.value.namaDiskon}" berhasil diedit!`, 'success')
    editDialog.value = false
    await fetchDiscounts()
  } catch (err) {
    console.error(err)
    showSnackbar('Terjadi kesalahan saat mengedit diskon.', 'error')
  }
}

const deleteSelected = async () => {
  if (checkedDiscounts.value.length === 0) return;
  if (!confirm('Yakin ingin menghapus diskon terpilih?')) return;
  for (const id of checkedDiscounts.value) {
    try {
      await fetch(`${API_URL}/${id}`, { method: 'DELETE' });
    } catch (err) {
      console.error('Gagal hapus diskon', id, err);
    }
  }
  checkedDiscounts.value = [];
  await fetchDiscounts();
}

const handleConfirmDelete = async () => {
  if (checkedDiscounts.value.length === 0) return;
  for (const id of checkedDiscounts.value) {
    try {
      await fetch(`${API_URL}/${id}`, { method: 'DELETE' });
    } catch (err) {
      console.error('Gagal hapus diskon', id, err);
    }
  }
  checkedDiscounts.value = [];
  await fetchDiscounts();
  showSnackbar('Diskon berhasil dihapus!', 'success');
  confirmDialog.value = false;
}
</script>

<template>
  <div class="container">
<header class="header">
  <div>
    <h2>Daftar Diskon</h2>
    <div class="header-controls">
      <div class="input-group rounded-pill" style="max-width: 550px;"  >
        <span class="input-group-text">
          <i class="bi bi-search"></i> <!-- pakai Bootstrap Icons -->
        </span>
        <input type="text" class="form-control" placeholder="Cari diskon...">
      </div>
      <!-- <md-outlined-select v-model="selectedOutlet" class="custom-select">
        <md-select-option value="kopi" selected>Kopi Anak Bangsa</md-select-option>
        <md-select-option value="link">https://crudcrud.com/api/e4448f5babfb4c8ab46cd28d79ffc109/discounts</md-select-option>
      </md-outlined-select> -->
      <select class="form-select custom-select" style="max-width: 200px;" >
        <option value="kopi" selected>Kopi Anak Bangsa</option>
        <option value="crudcrud">https://crudcrud.com/api/e4448f5babfb4c8ab46cd28d79ffc109/discounts</option>
      </select>
    </div>
  </div>

  <md-filled-button v-if="checkedDiscounts.length === 0" class="add-button" @click="addDiscount">
    &ensp;
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="20"
      height="20"
      viewBox="0 0 24 24"
      fill="currentColor"
    >
      <path d="M19 13H13V19H11V13H5V11H11V5H13V11H19V13Z" />
    </svg>
    <span>Tambah diskon</span>
    &ensp;
  </md-filled-button>
<md-filled-button
  v-else
  class="delete-button"
  @click="confirmDialog = true"
  style="background:#e53935;color:#fff;"
>
  &ensp;
  <svg
    xmlns="http://www.w3.org/2000/svg"
    width="20"
    height="20"
    viewBox="0 0 24 24"
    fill="currentColor"
  >
    <path d="M6 19c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7H6v12zM19 4h-3.5l-1-1h-5l-1 1H5v2h14V4z"/>
  </svg>
  <span>Hapus</span> &ensp;
</md-filled-button>
</header>


    <section>
      <template v-if="loadingDiscounts">
        <p>Memuat data diskon...</p>
      </template>
      <template v-else-if="discounts.length === 0">
        <Empty @add="addDiscount" />
      </template>
      <template v-else>
        <List :discounts="discounts" :selectedOutlet="selectedOutlet" @edit="openEdit" @check="checkedDiscounts = $event" />
      </template>
    </section>

    <footer class="footer">
      2024 © PT Nusantara Berkah Digital
    </footer>
  </div>

  <Modal
    ref="modalRef"
    :dialog="dialog"
    :form="form"
    :formValid="formValid"
    :nilaiLabel="nilaiLabel"
    @update:dialog="val => dialog = val"
    @submit="submitForm"
  />
  <EditModal
    ref="editModalRef"
    :dialog="editDialog"
    :form="editForm"
    :formValid="editFormValid"
    :nilaiLabel="editNilaiLabel"
    @update:dialog="val => editDialog = val"
    @submit="submitEditForm"
  />
  <ConfirmModal
  :dialog="confirmDialog"
  title="Konfirmasi Hapus"
  message="Apakah Anda yakin ingin menghapus diskon terpilih?"
  @update:dialog="val => confirmDialog = val"
  @confirm="handleConfirmDelete"
/>
  <v-snackbar v-model="snackbar" :color="snackbarColor" timeout="3000" location="top right" rounded>
    {{ snackbarText }}
  </v-snackbar>
</template>

<style scoped>
.header-controls {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-top: 0.5rem;
}

.search-field {
  display: flex;
  align-items: center;
  min-width: 220px;
  max-width: 300px;
  flex: 1;
}

.input-group-text {
  background: #f5f5f5;
  border: none;
  padding: 0 8px;
  height: 38px;
  display: flex;
  align-items: center;
}

.form-control {
  flex: 1;
  min-width: 0;
  height: 38px;
}

.custom-select {
  min-width: 140px;
  max-width: 220px;
  height: 38px;
}
</style>





