<!-- src/components/Sidebar.vue -->
<template>
    <aside class="sidebar">
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
</template>

<script>
export default {
    name: 'Sidebar',
};
</script>

<style scoped>
.brand-logo {
    width: 170px;
    height: auto;
    margin-bottom: 10px;
}

.sidebar {
    position: fixed;
    top: 0;
    left: 0;
    width: 270px;
    height: 100vh;
    overflow-y: auto;
    z-index: 1000;
    background-color: #fff;
}

.sidebar-header {
    margin-bottom: 40px;
    text-align: center;
}

.brand {
    font-size: 28px;
    margin: 0;
    font-weight: 700;
    letter-spacing: 1.2px;
    color: #ff6584;
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
    color: #000000;
    padding: 0;
    margin: 0;
    flex-grow: 1;
}

.nav-menu li {
    padding: 12px 18px;
    font-weight: 600;
    border-radius: 6px;
    cursor: pointer;
    user-select: none;
    transition: background-color 0.3s ease;
}

.nav-menu li:hover,
.nav-menu li.active {
    background-color: #ff6584;
    color: #fff;
    font-weight: 700;
}

.brand-logo {
    width: 170px;
    height: auto;
    margin-bottom: 10px;
}

.user-avatar {
    margin-top: 75px;
    width: 65px;
    height: auto;
    border-radius: 50%;
    object-fit: cover;
    margin-bottom: 0px;
}
</style>
