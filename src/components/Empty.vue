

<template>
  <div class="empty-card">
    <img src="/empty.png" alt="No Discount" class="illustration" />
    <h2>Belum Ada Diskon</h2>
    <p>Silahkan tambah diskon untuk menarik pelanggan dan meningkatkan penjualan.</p>
    <md-filled-button class="add-button" @click="$emit('add')">
        &ensp;<svg
    xmlns="http://www.w3.org/2000/svg"
    width="20"
    height="20"
    viewBox="0 0 24 24"
    fill="currentColor"
    
  >
    <path d="M19 13H13V19H11V13H5V11H11V5H13V11H19V13Z" />
  </svg> <span> Tambah diskon </span>&ensp;
    </md-filled-button>
  </div>
</template>
