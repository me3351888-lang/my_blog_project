<template>
  <section class="min-h-screen flex items-stretch text-white">
    <div class="lg:flex w-1/2 hidden bg-gray-500 bg-no-repeat bg-cover relative items-center"
         style="background-image: url(https://images.unsplash.com/photo-1577495508048-b635879837f1?auto=format&fit=crop&w=675&q=80);">
      <div class="absolute bg-black opacity-60 inset-0 z-0"></div>
      <div class="w-full px-24 z-10 text-left">
        <h1 class="text-5xl font-bold tracking-wide">Keep it special</h1>
        <p class="text-3xl my-4">Capture your personal memory in unique way, anywhere.</p>
      </div>
    </div>

    <div class="lg:w-1/2 w-full flex items-center justify-center text-center md:px-16 px-0 z-0 relative" style="background-color: #161616;">
      <div class="absolute lg:hidden z-10 inset-0 bg-gray-500 bg-no-repeat bg-cover items-center"
           style="background-image: url(https://images.unsplash.com/photo-1577495508048-b635879837f1?auto=format&fit=crop&w=675&q=80);">
        <div class="absolute bg-black opacity-60 inset-0 z-0"></div>
      </div>

      <div class="w-full py-6 z-20">
        <h1 class="my-6">
           <svg viewBox="0 0 247 31" class="w-auto h-7 sm:h-8 inline-flex">
             <path fill="#06B6D4" d="M25.517 0C18.712 0 14.46 3.382 12.758 10.146c2.552-3.382 5.529-4.65 8.931-3.805 1.941.482 3.329 1.882 4.864 3.432 2.502 2.524 5.398 5.445 11.722 5.445 6.804 0 11.057-3.382 12.758-10.145-2.551 3.382-5.528 4.65-8.93 3.804-1.942-.482-3.33-1.882-4.865-3.431C34.736 2.92 31.841 0 25.517 0z"></path>
           </svg>
        </h1>

        <div class="py-6 space-x-2">
          <span class="w-10 h-10 items-center justify-center inline-flex rounded-full font-bold text-lg border-2 border-white cursor-pointer hover:bg-white hover:text-black transition">f</span>
          <span class="w-10 h-10 items-center justify-center inline-flex rounded-full font-bold text-lg border-2 border-white cursor-pointer hover:bg-white hover:text-black transition">G+</span>
          <span class="w-10 h-10 items-center justify-center inline-flex rounded-full font-bold text-lg border-2 border-white cursor-pointer hover:bg-white hover:text-black transition">in</span>
        </div>

        <p class="text-gray-100">or use email your account</p>

        <form @submit.prevent="handleLogin" class="sm:w-2/3 w-full px-4 lg:px-0 mx-auto">
          <div class="pb-2 pt-4 text-left">
            <input v-model="formData.email" type="email" placeholder="Email"
                   class="block w-full p-4 text-lg rounded-sm bg-black border border-gray-800 focus:border-indigo-500 focus:outline-none transition-colors" required>
          </div>

          <div class="pb-2 pt-4 text-left">
            <input v-model="formData.password" type="password" placeholder="Password"
                   class="block w-full p-4 text-lg rounded-sm bg-black border border-gray-800 focus:border-indigo-500 focus:outline-none transition-colors" required>
          </div>

          <div class="text-right text-gray-400 hover:underline hover:text-gray-100">
            <router-link to="/register">Don't have an account? Register</router-link>
          </div>

          <div class="px-4 pb-2 pt-4">
            <button type="submit" :disabled="loading"
                    class="uppercase block w-full p-4 text-lg rounded-full bg-indigo-500 hover:bg-indigo-600 focus:outline-none transition-all disabled:opacity-50 font-bold">
              {{ loading ? 'Logging in...' : 'Login' }}
            </button>
          </div>

          <p v-if="error" class="text-red-500 mt-2 text-sm font-semibold">{{ error }}</p>
        </form>
      </div>
    </div>
  </section>
</template>

<script setup>
import { reactive, ref } from 'vue';
import { auth } from '@/firebase/config'; // تأكد أن المسار مطابق للي عملناه في الـ Register
import { signInWithEmailAndPassword } from 'firebase/auth';
import { useRouter } from 'vue-router';

const router = useRouter();
const formData = reactive({
  email: '',
  password: ''
});

const loading = ref(false);
const error = ref('');

const handleLogin = async () => {
  loading.value = true;
  error.value = '';

  try {
    // تسجيل الدخول الفعلي في فايربيز
    await signInWithEmailAndPassword(auth, formData.email, formData.password);

    console.log("Login Successful");
    // التحويل لصفحة الهوم بعد النجاح
    router.push('/');
  } catch (err) {
    // معالجة الأخطاء (إيميل غلط أو باسورد غلط)
    if (err.code === 'auth/user-not-found' || err.code === 'auth/wrong-password' || err.code === 'auth/invalid-credential') {
      error.value = "Invalid email or password.";
    } else {
      error.value = "An error occurred. Please try again.";
    }
  } finally {
    loading.value = false;
  }
};
</script>

<style scoped>
input::placeholder {
  color: #4b5563;
}
</style>