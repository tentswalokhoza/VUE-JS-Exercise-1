<template>
  <div class="app">
    <AppHeader :wishlistCount="wishlist.length" @toggle-wishlist="toggleWishlist" />

    <aside class="wishlist-panel" v-if="showWishlist">
      <h2>Wishlist</h2>
      <ul>
        <li v-if="wishlist.length === 0">No saved courses yet.</li>
        <li v-for="id in wishlist" :key="id">
          <span>{{ (courses.find(c => c.id === id) || {}).title }}</span>
          <button @click="toggleSave(id)">Remove</button>
        </li>
      </ul>
    </aside>

    <main class="container">
      <section class="controls">
        <div class="title-area">
          <h1>Cooking Masterclass — Courses</h1>
          <p class="subtitle">Curated workshops from expert chefs</p>
        </div>

        <div class="filters">
          <label>
            <input type="checkbox" v-model="onlyAvailable" />
            Show available only
          </label>
          <label>
            Level:
            <select v-model="levelFilter">
              <option value="">All</option>
              <option value="Beginner">Beginner</option>
              <option value="Intermediate">Intermediate</option>
              <option value="Advanced">Advanced</option>
            </select>
          </label>
        </div>
      </section>

      <section class="catalogue">
        <CourseCard
          v-for="course in filteredCourses"
          :key="course.id"
          :course="course"
          :saved="wishlist.includes(course.id)"
          @toggle-save="toggleSave"
        />
      </section>
    </main>

    <footer class="footer">
      <div>© {{ new Date().getFullYear() }} Cooking Masterclass</div>
      <div class="wishlist-summary">Saved: <strong>{{ wishlist.length }}</strong></div>
    </footer>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import Header from './components/Header.vue'
import CourseCard from './components/CourseCard.vue'

const initialCourses = [
  {
    id: 1,
    title: 'Mastering Sourdough: The Basics',
    chef: 'Ana Gómez',
    level: 'Beginner',
    price: 49.0,
    currency: 'USD',
    available: true,
    image: '/assets/sourdough.jpg'
  },
  {
    id: 2,
    title: 'Modern Vegan Plating',
    chef: 'Marcus Lee',
    level: 'Intermediate',
    price: 75.0,
    currency: 'USD',
    available: true,
    image: '/assets/vegan.jpg'
  },
  {
    id: 3,
    title: 'French Pastry Intensive',
    chef: 'Claire Dubois',
    level: 'Advanced',
    price: 120.0,
    currency: 'USD',
    available: false,
    image: '/assets/pastry.jpg'
  },
  {
    id: 4,
    title: 'Street Food Flavours of Asia',
    chef: 'Ravi Srinivasan',
    level: 'Beginner',
    price: 60.0,
    currency: 'USD',
    available: true,
    image: '/assets/streetfood.jpg'
  },
  {
    id: 5,
    title: 'Seafood Fundamentals',
    chef: 'Lin Wang',
    level: 'Intermediate',
    price: 85.0,
    currency: 'USD',
    available: false,
    image: '/assets/seafood.jpg'
  }
]

export default {
  name: 'App',
  components: { AppHeader: Header, CourseCard },
  setup() {
    const courses = ref(initialCourses)
    const wishlist = ref([])
    const showWishlist = ref(false)

    const onlyAvailable = ref(false)
    const levelFilter = ref('')

    const toggleSave = (courseId) => {
      const idx = wishlist.value.indexOf(courseId)
      if (idx === -1) wishlist.value.push(courseId)
      else wishlist.value.splice(idx, 1)
    }

    const toggleWishlist = () => {
      showWishlist.value = !showWishlist.value
    }

    const filteredCourses = computed(() => {
      return courses.value.filter(c => {
        if (onlyAvailable.value && !c.available) return false
        if (levelFilter.value && c.level !== levelFilter.value) return false
        return true
      })
    })

    return { courses, wishlist, toggleSave, onlyAvailable, levelFilter, filteredCourses, showWishlist, toggleWishlist }
  }
}
</script>
