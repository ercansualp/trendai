<template>
  <div class="bg-white p-10 rounded-xl shadow-2xl border border-gray-100">
    <div class="mb-8">
      <h1 class="text-center text-4xl font-extrabold tracking-tight text-gray-900">
        TrendAI
      </h1>
      <h2 class="mt-4 text-center text-2xl font-bold tracking-tight text-gray-800">
        Yeni bir hesap oluşturun
      </h2>
      <p class="mt-2 text-center text-sm text-gray-600">
        Zaten bir hesabınız var mı?
        <NuxtLink to="/auth/login" class="font-medium text-primary hover:text-primary-foreground transition-colors duration-200">
          Giriş yapın
        </NuxtLink>
      </p>
    </div>
    <form class="mt-8 space-y-6" @submit.prevent="handleRegister">
      <div class="rounded-md shadow-sm -space-y-px">
        <div>
          <label for="email-address" class="sr-only">E-posta adresi</label>
          <input
            id="email-address"
            name="email"
            type="email"
            autocomplete="email"
            required
            class="relative block w-full appearance-none rounded-t-md border border-gray-300 px-4 py-3 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-primary focus:outline-none focus:ring-primary sm:text-sm transition-all duration-200"
            placeholder="E-posta adresi"
            v-model="email"
          />
        </div>
        <div>
          <label for="password" class="sr-only">Şifre</label>
          <input
            id="password"
            name="password"
            type="password"
            autocomplete="new-password"
            required
            class="relative block w-full appearance-none rounded-none border border-gray-300 px-4 py-3 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-primary focus:outline-none focus:ring-primary sm:text-sm transition-all duration-200"
            placeholder="Şifre"
            v-model="password"
          />
        </div>
        <div>
          <label for="confirm-password" class="sr-only">Şifreyi Onayla</label>
          <input
            id="confirm-password"
            name="confirm-password"
            type="password"
            autocomplete="new-password"
            required
            class="relative block w-full appearance-none rounded-b-md border border-gray-300 px-4 py-3 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-primary focus:outline-none focus:ring-primary sm:text-sm transition-all duration-200"
            placeholder="Şifreyi Onayla"
            v-model="confirmPassword"
          />
        </div>
      </div>

      <div>
        <button
          type="submit"
          class="group relative flex w-full justify-center rounded-md border border-transparent bg-primary py-3 px-4 text-sm font-semibold text-white hover:bg-primary-foreground focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2 transition-all duration-200 transform hover:scale-[1.01]"
        >
          Kaydol
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

const email = ref('');
const password = ref('');
const confirmPassword = ref('');

const handleRegister = () => {
  if (password.value !== confirmPassword.value) {
    alert('Şifreler eşleşmiyor!');
    return;
  }
  console.log('Register attempt:', { email: email.value, password: password.value });
  // Burada kayıt mantığı eklenecek (örn. API çağrısı)
  alert('Kayıt yapıldı (simülasyon)!');
  // Başarılı kayıt sonrası yönlendirme yapılabilir
  // navigateTo('/auth/confirm-email');
};
</script>
