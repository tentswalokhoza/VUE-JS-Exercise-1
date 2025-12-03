<template>
  <aside class="cart-panel">
    <h2>Shopping Cart</h2>

    <div v-if="items.length === 0" class="empty-state">
      <p>Your cart is empty.</p>
      <p>Start adding courses from the catalogue!</p>
    </div>

    <ul v-else class="cart-items">
      <li v-for="item in items" :key="item.id" class="cart-item">
        <div class="item-info">
          <h4>{{ item.title }}</h4>
          <p class="item-price">{{ formatPrice(item.price, item.currency) }} each</p>
        </div>

        <div class="item-controls">
          <button class="qty-btn" @click="decrementQty(item.id)" :disabled="item.qty <= 1">−</button>
          <span class="qty">{{ item.qty }}</span>
          <button class="qty-btn" @click="incrementQty(item.id)">+</button>
        </div>

        <div class="item-total">
          {{ formatPrice(item.price * item.qty, item.currency) }}
        </div>

        <button class="remove-btn" @click="removeItem(item.id)">✕</button>
      </li>
    </ul>

    <div v-if="items.length > 0" class="cart-summary">
      <div class="summary-row">
        <span>Subtotal:</span>
        <span>{{ formatPrice(subtotal, 'ZAR') }}</span>
      </div>
      <div class="summary-row">
        <span>Tax (15%):</span>
        <span>{{ formatPrice(tax, 'ZAR') }}</span>
      </div>
      <div class="summary-row total">
        <span>Grand Total:</span>
        <span>{{ formatPrice(grandTotal, 'ZAR') }}</span>
      </div>

      <button class="clear-cart-btn" @click="clearCart">Clear Cart</button>
    </div>
  </aside>
</template>

<script>
export default {
  name: 'ShoppingCart',
  props: {
    items: { type: Array, required: true },
    subtotal: { type: Number, default: 0 },
    tax: { type: Number, default: 0 },
    grandTotal: { type: Number, default: 0 }
  },
  emits: ['update-qty', 'remove-item', 'clear-cart'],
  methods: {
    incrementQty(itemId) {
      this.$emit('update-qty', { id: itemId, qty: 1 })
    },
    decrementQty(itemId) {
      this.$emit('update-qty', { id: itemId, qty: -1 })
    },
    removeItem(itemId) {
      this.$emit('remove-item', itemId)
    },
    clearCart() {
      this.$emit('clear-cart')
    },
    formatPrice(amount, currency) {
      try {
        return new Intl.NumberFormat(undefined, {
          style: 'currency',
          currency: currency || 'ZAR'
        }).format(amount)
      } catch {
        return `${currency || 'R'} ${amount.toFixed(2)}`
      }
    }
  }
}
</script>

<style scoped>
.cart-panel {
  background: var(--color-background-soft);
  border: 1px solid var(--color-border);
  border-radius: 8px;
  padding: 1.5rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  height: fit-content;
  position: sticky;
  top: 2rem;
}

.cart-panel h2 {
  margin: 0;
  color: var(--color-heading);
  font-size: 1.25rem;
  border-bottom: 2px solid var(--color-border-hover);
  padding-bottom: 0.75rem;
}

.empty-state {
  text-align: center;
  color: var(--color-text);
  padding: 2rem 0;
}

.empty-state p {
  margin: 0.5rem 0;
  font-size: 0.95rem;
}

.cart-items {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.cart-item {
  background: var(--color-background-mute);
  border: 1px solid var(--color-border);
  border-radius: 6px;
  padding: 0.75rem;
  display: grid;
  grid-template-columns: 1fr auto auto auto;
  gap: 0.5rem;
  align-items: center;
}

.item-info h4 {
  margin: 0;
  font-size: 0.9rem;
  color: var(--color-heading);
}

.item-price {
  margin: 0.25rem 0 0 0;
  font-size: 0.8rem;
  color: var(--color-text);
}

.item-controls {
  display: flex;
  gap: 0.25rem;
  align-items: center;
}

.qty-btn {
  background: var(--color-border-hover);
  border: none;
  color: var(--color-text);
  width: 24px;
  height: 24px;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
  transition: background 0.2s;
}

.qty-btn:hover:not(:disabled) {
  background: var(--color-heading);
  color: #0b0b0b;
}

.qty-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.qty {
  min-width: 24px;
  text-align: center;
  color: var(--color-text);
  font-size: 0.85rem;
}

.item-total {
  text-align: right;
  color: var(--color-heading);
  font-weight: 600;
  font-size: 0.9rem;
  min-width: 60px;
}

.remove-btn {
  background: transparent;
  border: 1px solid rgba(212, 175, 55, 0.3);
  color: #888;
  width: 24px;
  height: 24px;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s;
}

.remove-btn:hover {
  background: rgba(212, 175, 55, 0.2);
  color: var(--color-heading);
  border-color: var(--color-heading);
}

.cart-summary {
  border-top: 2px solid var(--color-border);
  padding-top: 1rem;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.summary-row {
  display: flex;
  justify-content: space-between;
  font-size: 0.95rem;
  color: var(--color-text);
}

.summary-row.total {
  font-size: 1.1rem;
  font-weight: 700;
  color: var(--color-heading);
  padding-top: 0.5rem;
  border-top: 1px solid var(--color-border);
}

.clear-cart-btn {
  background: rgba(212, 175, 55, 0.2);
  border: 1px solid var(--color-border-hover);
  color: var(--color-text);
  padding: 0.5rem 1rem;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
  margin-top: 0.5rem;
  transition: all 0.2s;
}

.clear-cart-btn:hover {
  background: var(--color-border-hover);
  color: var(--color-heading);
}

@media (max-width: 768px) {
  .cart-panel {
    position: static;
    width: 100%;
    height: auto;
  }

  .cart-item {
    grid-template-columns: 1fr auto;
    gap: 0.5rem;
  }

  .item-info {
    order: 1;
  }

  .item-controls {
    order: 2;
  }

  .item-total {
    order: 3;
    grid-column: 1 / -1;
    text-align: left;
  }

  .remove-btn {
    order: 4;
    grid-column: 1 / -1;
    width: 100%;
  }
}
</style>
