<template>
    <nav class="navbar">
        <div class="navbar-left">
            <img src="/images/logo.png" alt="Mireta Logo" class="logo" />
        </div>
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

    box-sizing: border-box;
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 60px;
    padding: 0 16px;
    background: white;
    border-bottom: 1px solid #ccc;
}

/* .navbar-right {
    display: flex;
    align-items: center;
    gap: 20px;
}

.profile-info {
    display: flex;
    align-items: center;
    gap: 10px;
} */

.logo {
    height: 32px;
    margin-right: 8px;
}

.brand-text {
    font-weight: bold;
    color: #f26a4b;
    font-size: 14px;
    text-transform: lowercase;
}

.navbar-left {
    display: flex;
    align-items: center;
}

.navbar-right .icon-btn {
    background: none;
    border: none;
    margin-left: 10px;
    font-size: 18px;
    cursor: pointer;
}
</style>
