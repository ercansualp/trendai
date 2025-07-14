<template>
  <div class="bg-white p-10 rounded-xl shadow-2xl border border-gray-100">
    <div class="mb-8">
      <h1 class="text-center text-4xl font-extrabold tracking-tight text-gray-900">
        TrendAI
      </h1>
      <h2 class="mt-4 text-center text-2xl font-bold tracking-tight text-gray-800">
        Yeni şifrenizi belirleyin
      </h2>
      <p class="mt-2 text-center text-sm text-gray-600">
        Lütfen yeni şifrenizi girin.
      </p>
    </div>
    <form class="mt-8 space-y-6" @submit.prevent="handleResetPassword">
      <div class="rounded-md shadow-sm -space-y-px">
        <div>
          <label for="new-password" class="sr-only">Yeni Şifre</label>
          <input
            id="new-password"
            name="new-password"
            type="password"
            autocomplete="new-password"
            required
            class="relative block w-full appearance-none rounded-t-md border border-gray-300 px-4 py-3 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-primary focus:outline-none focus:ring-primary sm:text-sm transition-all duration-200"
            placeholder="Yeni Şifre"
            v-model="newPassword"
          />
        </div>
        <div>
          <label for="confirm-new-password" class="sr-only">Yeni Şifreyi Onayla</label>
          <input
            id="confirm-new-password"
            name="confirm-new-password"
            type="password"
            autocomplete="new-password"
            required
            class="relative block w-full appearance-none rounded-b-md border border-gray-300 px-4 py-3 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-primary focus:outline-none focus:ring-primary sm:text-sm transition-all duration-200"
            placeholder="Yeni Şifreyi Onayla"
            v-model="confirmNewPassword"
          />
        </div>
      </div>

      <div>
        <button
          type="submit"
          class="group relative flex w-full justify-center rounded-md border border-transparent bg-primary py-3 px-4 text-sm font-semibold text-white hover:bg-primary-foreground focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2 transition-all duration-200 transform hover:scale-[1.01]"
        >
          Şifreyi Sıfırla
        </button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';

definePageMeta({
  layout: 'auth',
});

const newPassword = ref('');
const confirmNewPassword = ref('');

const handleResetPassword = () => {
  if (newPassword.value !== confirmNewPassword.value) {
    alert('Şifreler eşleşmiyor!');
    return;
  }
  console.log('Reset password attempt with new password.');
  // Burada şifre sıfırlama mantığı eklenecek (örn. API çağrısı)
  alert('Şifreniz başarıyla sıfırlandı (simülasyon)!');
  // Başarılı sıfırlama sonrası giriş sayfasına yönlendirme
  // navigateTo('/auth/login');
};
</script>
