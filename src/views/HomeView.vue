<template>
  <div class="min-h-screen bg-[#161616] text-white font-sans">
    <nav class="flex items-center justify-between px-8 py-6 border-b border-gray-800">
      <div class="text-2xl font-bold text-indigo-500 tracking-tighter uppercase">MyBlog</div>

      <div v-if="user" class="hidden md:flex items-center text-gray-300">
        Welcome, <span class="ml-2 text-white font-bold">{{ user.displayName || 'Blogger' }}</span>
      </div>

      <div class="flex items-center space-x-4">
        <button v-if="user" @click="handleLogout" class="text-red-400 hover:text-red-300 text-sm mr-4 font-bold">
          Logout
        </button>

        <router-link v-else to="/login" class="bg-indigo-600 px-5 py-2 rounded-full font-medium hover:bg-indigo-700 transition">
          Login
        </router-link>
      </div>
    </nav>

    <header class="px-8 py-16 md:py-24 max-w-6xl mx-auto text-center">
      <h1 class="text-5xl md:text-7xl font-extrabold mb-6 tracking-tight">
        Share your <span class="text-indigo-500">stories</span> with the world.
      </h1>
      <p class="text-gray-400 text-xl max-w-2xl mx-auto">
        A professional space for creative writers and tech enthusiasts to document their journey.
      </p>

      <div v-if="user" class="mt-8">
        <button class="bg-indigo-500 hover:bg-indigo-600 px-8 py-3 rounded-full font-bold transition shadow-lg shadow-indigo-500/20">
          + Write New Article
        </button>
      </div>
    </header>

    <main class="px-8 pb-20 max-w-7xl mx-auto">
      <div class="flex items-center justify-between mb-12">
        <h2 class="text-2xl font-bold border-l-4 border-indigo-500 pl-4">Latest Stories</h2>
        <div class="flex space-x-2">
          <span class="bg-gray-800 px-3 py-1 rounded-md text-sm text-indigo-400 font-bold">Tech</span>
          <span class="bg-gray-800 px-3 py-1 rounded-md text-sm text-gray-400">Life</span>
          <span class="bg-gray-800 px-3 py-1 rounded-md text-sm text-gray-400">Design</span>
        </div>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-10">
        <article v-for="post in posts" :key="post.id" class="group cursor-pointer">
          <div class="overflow-hidden rounded-xl bg-gray-800 h-64 mb-4">
            <img :src="post.image" alt="Post Cover" class="w-full h-full object-cover group-hover:scale-105 transition duration-500 opacity-80 group-hover:opacity-100">
          </div>
          <div>
            <span class="text-indigo-400 text-sm font-bold uppercase tracking-widest">{{ post.category }}</span>
            <h3 class="text-2xl font-bold mt-2 group-hover:text-indigo-400 transition">{{ post.title }}</h3>
            <p class="text-gray-400 mt-3 line-clamp-2">{{ post.description }}</p>
            <div class="mt-6 flex items-center space-x-3">
              <div class="w-8 h-8 rounded-full bg-indigo-500 flex items-center justify-center text-[10px] font-bold">
                {{ post.author.charAt(0) }}
              </div>
              <span class="text-sm text-gray-300">{{ post.author }} • {{ post.date }}</span>
            </div>
          </div>
        </article>
      </div>
    </main>

    <footer class="border-t border-gray-800 py-10 text-center text-gray-500 text-sm">
      <p>&copy; 2026 MyBlog Platform. Keep it special.</p>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
// لو العلامة @ عاملة مشكلة، جرب تغيرها لـ '../firebase/config'
import { auth } from '@/firebase/config';
import { onAuthStateChanged, signOut } from 'firebase/auth';
import { useRouter } from 'vue-router';

const router = useRouter();
const user = ref(null);

// مراقبة حالة المستخدم أول ما الصفحة تفتح
onMounted(() => {
  onAuthStateChanged(auth, (currentUser) => {
    user.value = currentUser; // لو في مستخدم مسجل هيظهر اسمه
    console.log("Current User:", currentUser);
  });
});

// دالة الخروج
const handleLogout = async () => {
  try {
    await signOut(auth);
    user.value = null;
    router.push('/login');
  } catch (err) {
    console.error("Logout failed", err);
  }
};

// بيانات المقالات (Mock Data)
const posts = ref([
  {
    id: 1,
    title: 'How to start a creative journey',
    description: 'Practical tips for anyone looking to unlock their inner creativity and start writing consistently.',
    category: 'Creativity',
    author: 'Admin',
    date: 'Jan 15, 2026',
    image: 'https://images.unsplash.com/photo-1499750310107-5fef28a66643?auto=format&fit=crop&w=800&q=80'
  },
  {
    id: 2,
    title: 'Modern Web Design Trends',
    description: 'Exploring the shift towards dark mode, bento grids, and minimal typography in 2026.',
    category: 'Design',
    author: 'Danimai',
    date: 'Jan 12, 2026',
    image: 'https://images.unsplash.com/photo-1558655146-d09347e92766?auto=format&fit=crop&w=800&q=80'
  },
  {
    id: 3,
    title: 'Why Vue 3 is still a beast',
    description: 'A deep dive into why the composition API makes frontend development much more enjoyable.',
    category: 'Tech',
    author: 'Developer',
    date: 'Jan 10, 2026',
    image: 'https://images.unsplash.com/photo-1633356122544-f134324a6cee?auto=format&fit=crop&w=800&q=80'
  }
]);
</script>

<style scoped>
.line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
