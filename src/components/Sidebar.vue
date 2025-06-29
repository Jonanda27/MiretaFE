<template>
    <div>
        <!-- Overlay untuk layar kecil -->
        <div class="sidebar-overlay" v-if="isOpen" @click="$emit('close')"></div>

        <!-- Sidebar -->
        <aside class="sidebar" :class="{ open: isOpen }">
            <!-- Tombol close hanya tampil di mobile -->
            <button class="close-btn" @click="$emit('close')">✕</button>

            <div class="sidebar-header">
                <img class="user-avatar" src="/images/user2.png" alt="User Avatar" />
                <p class="store-name">PEMPEK DUO</p>
                <p class="owner-role">BUSINESS OWNER</p>
            </div>

            <ul class="nav-menu">
                <li>Dashboard</li>
                <li>Report</li>
                <li>Transactions</li>
                <li>Inventory</li>
                <li>Operational</li>
                <li class="active">Forecasting</li>
            </ul>
        </aside>
    </div>
</template>

<script>
export default {
    name: "Sidebar",
    props: {
        isOpen: {
            type: Boolean,
            default: false,
        },
    },
};
</script>

<style scoped>
/* Base Sidebar */
.sidebar {
    position: fixed;
    left: 0;
    top: 0;
    height: 100%;
    width: 270px;
    background: white;
    z-index: 999;
    transform: translateX(-100%);
    transition: transform 0.3s ease;
}

.sidebar.open {
    transform: translateX(0);
}

/* Overlay gelap */
.sidebar-overlay {
    position: fixed;
    top: 0;
    left: 0;
    height: 100vh;
    width: 100vw;
    z-index: 1100;
}

/* Tombol close */
.close-btn {
    display: block;
    position: absolute;
    top: 12px;
    right: 12px;
    font-size: 22px;
    background: none;
    border: none;
    cursor: pointer;
    color: #333;
}


@media (max-width: 768px) {
    .close-btn {
        display: block;
    }
}

/* Sidebar content */
.sidebar-header {
    margin-bottom: 40px;
    text-align: center;
    margin-top: 75px;
}

.user-avatar {
    width: 65px;
    height: auto;
    border-radius: 50%;
    object-fit: cover;
}

.store-name {
    margin: 8px 0 4px;
    font-size: 16px;
    font-weight: 600;
    color: #000000;
}

.owner-role {
    font-size: 10px;
    font-weight: 500;
    color: #aaa;
}

.nav-menu {
    list-style: none;
    padding: 0;
    margin: 0;
}

.nav-menu li {
    padding: 12px 18px;
    font-weight: 600;
    border-radius: 6px;
    cursor: pointer;
    user-select: none;
    color: #000000;
    transition: background-color 0.3s ease;
}

.nav-menu li:hover,
.nav-menu li.active {
    background-color: #ff6584;
    color: #fff;
    font-weight: 700;
}
</style>
