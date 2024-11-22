<template>
  <div>
    <button @click="toggleMenu" class="toggle-btn">
      <i class="bi bi-justify"></i>
    </button>
    <div :class="['menu', { hidden: !menuVisible }]">
      <ul class="nav flex-column p-0">
        <!-- Conditional Menu for Guru and Siswa -->
        <span v-if="profile.role === 'guru'">
          <NuxtLink to="/dashboard" class="menu-item">
            <li class="mb-3">
              <i class="bi bi-house-door-fill"></i> Beranda
            </li>
          </NuxtLink>
          <NuxtLink to="/siswa" class="menu-item">
            <li class="mb-3">
              <i class="bi bi-people"></i> Siswa
            </li>
          </NuxtLink>
          <NuxtLink to="/tabungan" class="menu-item">
            <li class="mb-3">
              <i class="bi bi-bookmarks-fill"></i> Tabungan
            </li>
          </NuxtLink>
          <NuxtLink to="/pemasukan" class="menu-item">
            <li class="mb-3">
              <i class="bi bi-wallet2"></i> Pemasukan
            </li>
          </NuxtLink>
          <NuxtLink to="/penarikan" class="menu-item">
            <li class="mb-3">
              <i class="bi bi-wallet2"></i> Penarikan
            </li>
          </NuxtLink>
        </span>
        <span v-else>
          <NuxtLink to="/murid" class="menu-item">
            <li class="mb-3">
              <i class="bi bi-house-door-fill"></i> Beranda
            </li>
          </NuxtLink>
          <NuxtLink to="/identitas" class="menu-item">
            <li class="mb-3">
              <i class="bi bi-people"></i> Profil
            </li>
          </NuxtLink>
          <NuxtLink to="/riwayats" class="menu-item">
            <li class="mb-3">
              <i class="bi bi-bookmarks-fill"></i> Riwayat
            </li>
          </NuxtLink>
        </span>
        <NuxtLink to="/logout" class="menu-item">
          <li class="mb-3 logout">
            <i class="bi bi-box-arrow-in-left"></i> Logout
          </li>
        </NuxtLink>
      </ul>
    </div>
  </div>
</template>

<script setup>
const user = useSupabaseUser();
const client = useSupabaseClient();
const profile = ref({
  user_id: '',
  role: '',
  fullname: '',
});

const menuVisible = ref(false);

function toggleMenu() {
  menuVisible.value = !menuVisible.value;
  if (menuVisible.value) {
    document.body.classList.add('shift-right');
  } else {
    document.body.classList.remove('shift-right');
  }
}
async function getRole() {
  const { data, error } = await client
    .from('profile')
    .select('*')
    .eq('user_id', user.value.id)
    .single();
  if (data) profile.value = data;
}

onMounted(() => {
  getRole();
});
</script>

<style scoped>
/* Style for the Menu */
.menu {
  background-color: #3e3434;
  min-height: 85vh;
  width: 250px;
  padding: 10px;
  transition: transform 0.3s ease-in-out;
  position: fixed;
  top: 15vh;
  left: 0;
  z-index: 1000;
}

.menu.hidden {
  transform: translateX(-100%);
}

.menu-item {
  display: flex;
  align-items: center;
  color: white;
  text-decoration: none;
  font-size: 18px;
  padding: 10px 15px;
  transition: background 0.3s;
}

.menu-item:hover {
  background-color: #574d4d;
}

.nav li {
  list-style: none;
}

.bi {
  margin-right: 10px;
  font-size: 20px;
}

.toggle-btn {
  background-color: #3e3434;
  color: white;
  border: none;
  padding: 10px;
  cursor: pointer;
  border-radius: 5px;
  position: fixed;
  bottom: 20px;
  left: 20px;
  z-index: 1100;
}

@media (max-width: 768px) {
  .menu {
    width: 200px; /* smaller width on mobile */
    position: absolute;
    top: 0;
    left: 0;
    height: 100%; /* full height */
  }
  
  .menu.hidden {
    transform: translateX(-100%);
  }

  .toggle-btn {
    padding: 8px;
    bottom: 15px;
    left: 15px; /* Keep toggle button on screen */
  }

  .menu-item {
    font-size: 16px; /* Adjust font size */
  }
  
  /* Ensure that body is not hidden on small screens */
  body.shift-right {
    overflow-x: hidden;
  }
}

/* Responsiveness for medium screens (tablets) */
@media (min-width: 768px) and (max-width: 1024px) {
  .menu {
    width: 220px; /* slightly smaller width for tablet */
  }

  .menu-item {
    font-size: 17px; /* Adjust font size */
  }
}

/* Large screen styles (desktops) */
@media (min-width: 1024px) {
  .menu {
    width: 250px; /* default width */
  }

  .menu-item {
    font-size: 18px; /* default font size */
  }
}
</style>
