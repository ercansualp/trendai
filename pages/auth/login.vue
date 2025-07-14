<template>
  <div class="bg-white p-10 rounded-xl shadow-2xl border border-gray-100">
    <div class="mb-8">
      <h1 class="text-center text-4xl font-extrabold tracking-tight text-gray-900">
        TrendAI
      </h1>
      <h2 class="mt-4 text-center text-2xl font-bold tracking-tight text-gray-800">
        Hesabınıza giriş yapın
      </h2>
      <p class="mt-2 text-center text-sm text-gray-600">
        Veya
        <NuxtLink to="/auth/register" class="font-medium text-primary hover:text-primary-foreground transition-colors duration-200">
          yeni bir hesap oluşturun
        </NuxtLink>
      </p>
    </div>
    <form class="mt-8 space-y-6" @submit.prevent="handleLogin">
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
            autocomplete="current-password"
            required
            class="relative block w-full appearance-none rounded-b-md border border-gray-300 px-4 py-3 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-primary focus:outline-none focus:ring-primary sm:text-sm transition-all duration-200"
            placeholder="Şifre"
            v-model="password"
          />
        </div>
      </div>

      <div class="flex items-center justify-between">
        <div class="flex items-center">
          <input
            id="remember-me"
            name="remember-me"
            type="checkbox"
            class="h-4 w-4 rounded border-gray-300 text-primary focus:ring-primary transition-colors duration-200"
          />
          <label for="remember-me" class="ml-2 block text-sm text-gray-900">
            Beni hatırla
          </label>
        </div>

        <div class="text-sm">
          <NuxtLink to="/auth/forgot-password" class="font-medium text-primary hover:text-primary-foreground transition-colors duration-200">
            Şifrenizi mi unuttunuz?
          </NuxtLink>
        </div>
      </div>

      <div>
        <button
          type="submit"
          class="group relative flex w-full justify-center rounded-md border border-transparent bg-primary py-3 px-4 text-sm font-semibold text-white hover:bg-primary-foreground focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2 transition-all duration-200 transform hover:scale-[1.01]"
        >
          Giriş Yap
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

const handleLogin = () => {
  console.log('Login attempt:', { email: email.value, password: password.value });
  // Burada giriş mantığı eklenecek (örn. API çağrısı)
  alert('Giriş yapıldı (simülasyon)!');
  // Başarılı giriş sonrası yönlendirme yapılabilir
  // navigateTo('/admin');
};
</script>
