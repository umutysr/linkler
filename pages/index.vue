<template>
    <div class="min-h-screen bg-gray-100 flex items-center justify-center" id="body">
      <div class="w-full max-w-md p-4 bg-white rounded-lg shadow text-center" id="listeler">
        <!-- Logo -->
        <div class="mb-6">
          <img src="./95afce.jpg" alt="Logo" class="w-32 h-auto mx-auto rounded-full" />
        </div>
  
        <!-- Şube Listesi -->
        <div class="space-y-4">
          <h2 class="text-lg font-semibold mb-4">Tüm Şubeler</h2>
  
          <p v-if="loading" class="text-gray-500">Yükleniyor...</p>
          <p v-if="error" class="text-red-500">Hata: {{ error }}</p>
  
          <div
            v-for="(branch, index) in branches"
            :key="index"
          >
            <a
              :href="generateBranchUrl(branch)"
              target="_blank"
              rel="noopener noreferrer"
              class="block w-full bg-gray-500 bg-opacity-50 text-white rounded-lg p-3 shadow hover:bg-gray-600 hover:bg-opacity-70 transition"
            >
              <div class="font-semibold text-lg">
                {{ branch?.name?.tr || 'Şube İsmi Yok' }}
              </div>
            </a>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from "vue";
  
  const branches = ref([]);
  const error = ref(null);
  const loading = ref(false);
  const baseUrl = "https://huqqabaz.com/branch/";
  
  // Türkçe karakterleri sluga çevir
  const slugify = (text) => {
    return text
      .toString()
      .toLowerCase()
      .trim()
      .replace(/ç/g, "c")
      .replace(/ğ/g, "g")
      .replace(/ı/g, "i")
      .replace(/ö/g, "o")
      .replace(/ş/g, "s")
      .replace(/ü/g, "u")
      .replace(/[^a-z0-9 -]/g, "")
      .replace(/\s+/g, "-")
      .replace(/-+/g, "-");
  };
  
  // Linki oluştur
  const generateBranchUrl = (branch) => {
    if (!branch?.name?.tr) return "#";
    const slug = slugify(branch.name.tr);
    return baseUrl + slug;
  };
  
  // API'den verileri al
  const getBranches = async () => {
    loading.value = true;
    error.value = null;
    try {
      const res = await fetch("https://api.qfoodbeverage.com.tr/api/branches", {
        headers: {
          "X-Host-Name": "huqqabaz.com",
        },
      });
  
      const json = await res.json();
      branches.value = json?.data || [];
    } catch (e) {
      error.value = e.message;
    } finally {
      loading.value = false;
    }
  };
  
  onMounted(() => {
    getBranches();
  });
  </script>
  