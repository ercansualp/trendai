<template>
  <div class="bg-white p-10 rounded-xl shadow-2xl border border-gray-100 text-center">
    <div class="mb-8">
      <h1 class="text-center text-4xl font-extrabold tracking-tight text-gray-900">
        TrendAI
      </h1>
      <h2 class="mt-4 text-2xl font-bold tracking-tight text-gray-800">
        E-posta adresinizi onaylayın
      </h2>
      <p class="mt-4 text-base text-gray-600 leading-relaxed">
        Kayıt işlemini tamamlamak için lütfen e-posta adresinize gönderdiğimiz bağlantıya tıklayın.
        <br>
        E-postayı almadıysanız, spam klasörünüzü kontrol edin veya tekrar göndermek için aşağıdaki butona tıklayın.
      </p>
    </div>
    <div class="mt-6 space-y-4">
      <button
        type="button"
        class="group relative flex w-full justify-center rounded-md border border-transparent bg-primary py-3 px-4 text-sm font-semibold text-white hover:bg-primary-foreground focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2 transition-all duration-200 transform hover:scale-[1.01]"
        @click="resendEmail"
      >
        Onay E-postasını Tekrar Gönder
      </button>
      <NuxtLink to="/auth/login" class="inline-flex items-center justify-center w-full py-3 px-4 text-sm font-medium text-primary hover:text-primary-foreground transition-colors duration-200">
        Giriş sayfasına geri dön
      </NuxtLink>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  layout: 'auth',
});

const resendEmail = () => {
  alert('Onay e-postası tekrar gönderildi (simülasyon)!');
  console.log('Resending confirmation email.');
  // Burada e-posta tekrar gönderme mantığı eklenecek
};
</script>
