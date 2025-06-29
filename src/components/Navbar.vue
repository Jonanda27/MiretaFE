<template>
  <nav class="navbar">
    <div class="navbar-left">
      <img src="/images/logo.png" alt="Mireta Logo" class="logo" />
      <span class="brand-text">Mireta POS</span>
    </div>

    <!-- Bagian ini bisa aktif kalau kamu mau pakai profile info di layar besar -->
    <!-- <div class="navbar-right">
      <div class="profile-info">
        <span class="profile-name">John Doe</span>
        <img class="profile-avatar" src="https://i.pravatar.cc/40" alt="Profile" />
      </div>
    </div> -->
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
  flex-wrap: wrap; /* Tambahkan agar konten bisa melipat di layar kecil */
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
  white-space: nowrap; /* Hindari pecah teks */
}

.navbar-left {
  display: flex;
  align-items: center;
  flex: 1;
  min-width: 0; /* Hindari overflow */
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
</style>
