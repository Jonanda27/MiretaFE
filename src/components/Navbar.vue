<template>
  <nav class="navbar">
    <div class="navbar-left">
      <button class="sidebar-toggle" @click="$emit('toggle-sidebar')">
        ☰
      </button>
      <img src="/images/logo.png" alt="Mireta Logo" class="logo" />
    </div>
  </nav>
</template>

<style scoped>
.navbar {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1100;

  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 1rem;
  height: 60px;

  background: white;
  border-bottom: 1px solid #ccc;

  box-sizing: border-box;
  flex-wrap: wrap;
  /* Tambahkan agar konten bisa melipat di layar kecil */
}

/* Responsif logo dan teks merek */
.logo {
  height: 28px;
  margin-right: 8px;
  max-width: 100%;
  object-fit: contain;
}

.brand-text {
  font-weight: bold;
  color: #f26a4b;
  font-size: 14px;
  text-transform: lowercase;
  white-space: nowrap;
  /* Hindari pecah teks */
}

.navbar-left {
  display: flex;
  align-items: center;
  flex: 1;
  min-width: 0;
  /* Hindari overflow */
}

/* Responsif bagian kanan navbar jika digunakan nanti */
.navbar-right {
  display: none;
}

@media (min-width: 768px) {
  .navbar-right {
    display: flex;
    align-items: center;
    gap: 20px;
  }
}

.profile-info {
  display: flex;
  align-items: center;
  gap: 10px;
}

.profile-avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  object-fit: cover;
}

.navbar-right .icon-btn {
  background: none;
  border: none;
  margin-left: 10px;
  font-size: 18px;
  cursor: pointer;
}

.sidebar-toggle {
  font-size: 24px;
  margin-right: 10px;
  background: none;
  border: none;
  color: #f26a4b;
  cursor: pointer;
  display: inline-block;
}
</style>
