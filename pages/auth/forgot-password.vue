<template>
  <div class="bg-white p-10 rounded-xl shadow-2xl border border-gray-100">
    <div class="mb-8">
      <h1 class="text-center text-4xl font-extrabold tracking-tight text-gray-900">
        TrendAI
      </h1>
      <h2 class="mt-4 text-center text-2xl font-bold tracking-tight text-gray-800">
        Şifrenizi sıfırlayın
      </h2>
      <p class="mt-2 text-center text-sm text-gray-600">
        Şifrenizi sıfırlamak için e-posta adresinizi girin.
      </p>
    </div>
    <form class="mt-8 space-y-6" @submit.prevent="handleForgotPassword">
      <div class="rounded-md shadow-sm">
        <div>
          <label for="email-address" class="sr-only">E-posta adresi</label>
          <input
            id="email-address"
            name="email"
            type="email"
            autocomplete="email"
            required
            class="relative block w-full appearance-none rounded-md border border-gray-300 px-4 py-3 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-primary focus:outline-none focus:ring-primary sm:text-sm transition-all duration-200"
            placeholder="E-posta adresi"
            v-model="email"
          />
        </div>
      </div>

      <div>
        <button
          type="submit"
          class="group relative flex w-full justify-center rounded-md border border-transparent bg-primary py-3 px-4 text-sm font-semibold text-white hover:bg-primary-foreground focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2 transition-all duration-200 transform hover:scale-[1.01]"
        >
          Şifre Sıfırlama Bağlantısı Gönder
        </button>
      </div>
      <div class="text-center text-sm">
        <NuxtLink to="/auth/login" class="font-medium text-primary hover:text-primary-foreground transition-colors duration-200">
          Giriş sayfasına geri dön
        </NuxtLink>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';

definePageMeta({
  layout: 'auth',
});

const email = ref('');

const handleForgotPassword = () => {
  console.log('Forgot password attempt for:', email.value);
  // Burada şifre sıfırlama e-postası gönderme mantığı eklenecek
  alert('Şifre sıfırlama bağlantısı e-postanıza gönderildi (simülasyon)!');
  // navigateTo('/auth/login'); // Kullanıcıyı giriş sayfasına yönlendirebiliriz
};
</script>
