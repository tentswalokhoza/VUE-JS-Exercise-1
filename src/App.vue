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

    <div class="checkout-wrapper">
      <main class="catalogue-section">
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
            @add-to-cart="addToCart"
          />
        </section>
      </main>

      <ShoppingCart
        :items="cartItems"
        :subtotal="subtotal"
        :tax="tax"
        :grandTotal="grandTotal"
        @update-qty="updateCartQty"
        @remove-item="removeFromCart"
        @clear-cart="clearCart"
      />
    </div>

    <footer class="footer">
      <div>© {{ new Date().getFullYear() }} Cooking Masterclass</div>
      <div class="summary">Cart: {{ cartItems.length }} item(s)</div>
    </footer>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import Header from './components/Header.vue'
import CourseCard from './components/CourseCard.vue'
import ShoppingCart from './components/Cart.vue'

const initialCourses = [
  {
    id: 1,
    title: 'Mastering Sourdough: The Basics',
    chef: 'Ana Gómez',
    level: 'Beginner',
    price: 49.0,
    currency: 'ZAR',
    available: true,
    image: '/assets/sourdough.jpg'
  },
  {
    id: 2,
    title: 'Modern Vegan Plating',
    chef: 'Marcus Lee',
    level: 'Intermediate',
    price: 75.0,
    currency: 'ZAR',
    available: true,
    image: '/assets/vegan.jpg'
  },
  {
    id: 3,
    title: 'French Pastry Intensive',
    chef: 'Claire Dubois',
    level: 'Advanced',
    price: 120.0,
    currency: 'ZAR',
    available: false,
    image: '/assets/pastry.jpg'
  },
  {
    id: 4,
    title: 'Street Food Flavours of Asia',
    chef: 'Ravi Srinivasan',
    level: 'Beginner',
    price: 60.0,
    currency: 'ZAR',
    available: true,
    image: '/assets/streetfood.jpg'
  },
  {
    id: 5,
    title: 'Seafood Fundamentals',
    chef: 'Lin Wang',
    level: 'Intermediate',
    price: 85.0,
    currency: 'ZAR',
    available: false,
    image: '/assets/seafood.jpg'
  }
]

export default {
  name: 'App',
  components: { AppHeader: Header, CourseCard, ShoppingCart },
  setup() {
    const courses = ref(initialCourses)
    const wishlist = ref([])
    const showWishlist = ref(false)
    const cart = ref([])

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

    const addToCart = (courseId) => {
      const course = courses.value.find(c => c.id === courseId)
      if (!course || !course.available) return

      const cartItem = cart.value.find(item => item.id === courseId)
      if (cartItem) {
        cartItem.qty += 1
      } else {
        cart.value.push({
          id: course.id,
          title: course.title,
          price: course.price,
          currency: course.currency,
          qty: 1
        })
      }
      saveCartToStorage()
    }

    const removeFromCart = (courseId) => {
      const idx = cart.value.findIndex(item => item.id === courseId)
      if (idx !== -1) {
        cart.value.splice(idx, 1)
      }
      saveCartToStorage()
    }

    const updateCartQty = ({ id, qty }) => {
      const item = cart.value.find(i => i.id === id)
      if (item) {
        item.qty = Math.max(0, item.qty + qty)
        if (item.qty === 0) {
          removeFromCart(id)
        } else {
          saveCartToStorage()
        }
      }
    }

    const clearCart = () => {
      cart.value = []
      saveCartToStorage()
    }

    const saveCartToStorage = () => {
      localStorage.setItem('cooking-masterclass-cart', JSON.stringify(cart.value))
    }

    const loadCartFromStorage = () => {
      try {
        const saved = localStorage.getItem('cooking-masterclass-cart')
        if (saved) {
          cart.value = JSON.parse(saved)
        }
      } catch (e) {
        console.error('Failed to load cart:', e)
      }
    }

    const filteredCourses = computed(() => {
      return courses.value.filter(c => {
        if (onlyAvailable.value && !c.available) return false
        if (levelFilter.value && c.level !== levelFilter.value) return false
        return true
      })
    })

    const cartItems = computed(() => cart.value)

    const subtotal = computed(() => {
      return cart.value.reduce((sum, item) => sum + (item.price * item.qty), 0)
    })

    const tax = computed(() => {
      return subtotal.value * 0.15
    })

    const grandTotal = computed(() => {
      return subtotal.value + tax.value
    })

    loadCartFromStorage()

    return {
      courses,
      wishlist,
      toggleSave,
      onlyAvailable,
      levelFilter,
      filteredCourses,
      showWishlist,
      toggleWishlist,
      addToCart,
      removeFromCart,
      updateCartQty,
      clearCart,
      cartItems,
      subtotal,
      tax,
      grandTotal
    }
  }
}
</script>

<style scoped>
.app {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.wishlist-panel {
  background: var(--color-background-mute);
  border: 1px solid var(--color-border);
  border-radius: 8px;
  margin: 1rem;
  padding: 1.5rem;
  position: fixed;
  top: 80px;
  right: 1rem;
  width: 280px;
  max-height: 60vh;
  overflow-y: auto;
  z-index: 10;
}

.wishlist-panel h2 {
  margin: 0 0 1rem 0;
  color: var(--color-heading);
}

.wishlist-panel ul {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.wishlist-panel li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem;
  background: var(--color-background-soft);
  border-radius: 4px;
  font-size: 0.9rem;
}

.wishlist-panel button {
  background: rgba(212,175,55,0.2);
  border: 1px solid var(--color-border);
  color: var(--color-text);
  padding: 0.25rem 0.5rem;
  border-radius: 3px;
  cursor: pointer;
  font-size: 0.8rem;
}

.wishlist-panel button:hover {
  background: var(--color-border-hover);
}

.checkout-wrapper {
  display: grid;
  grid-template-columns: 1fr 350px;
  gap: 2rem;
  padding: 2rem;
  flex: 1;
  max-width: 1400px;
  margin: 0 auto;
  width: 100%;
}

.catalogue-section {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.controls {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.title-area h1 {
  margin: 0;
  color: var(--color-heading);
  font-size: 1.8rem;
}

.title-area .subtitle {
  margin: 0.5rem 0 0 0;
  color: var(--color-text);
  font-size: 1rem;
}

.filters {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
}

.filters label {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  color: var(--color-text);
  font-size: 0.95rem;
}

.filters input,
.filters select {
  padding: 0.4rem;
  background: var(--color-background-soft);
  border: 1px solid var(--color-border);
  color: var(--color-text);
  border-radius: 4px;
}

.catalogue {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: 1rem;
}

.footer {
  background: var(--color-background-soft);
  border-top: 1px solid var(--color-border);
  padding: 1.5rem 2rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: var(--color-text);
  font-size: 0.95rem;
}

.footer .summary {
  color: var(--color-heading);
  font-weight: 600;
}

@media (max-width: 1200px) {
  .checkout-wrapper {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }

  .wishlist-panel {
    position: static;
    width: 100%;
    max-height: none;
  }
}

@media (max-width: 768px) {
  .checkout-wrapper {
    padding: 1rem;
    gap: 1rem;
  }

  .catalogue {
    grid-template-columns: 1fr;
  }

  .title-area h1 {
    font-size: 1.4rem;
  }

  .filters {
    flex-direction: column;
  }

  .filters label {
    width: 100%;
  }

  .footer {
    flex-direction: column;
    gap: 0.5rem;
    text-align: center;
  }
}
</style>
