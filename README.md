# Cooking Masterclass — Checkout Prototype (Exercise 05)

A Vue.js e-commerce checkout prototype for Cooking Masterclass. Users can browse courses, add items to a cart, adjust quantities, and view live price totals including tax.

## Features
- **Course catalogue** with browse, filter, and wishlist save
- **Shopping cart** with quantity controls and real-time price updates
- **Price summary** including subtotal, 15% tax, and grand total
- **Sold Out handling** — unavailable courses are clearly marked and cannot be added
- **Responsive layout** — two-column desktop view, single-column mobile
- **Persistent cart** — cart state saved to localStorage and restored on page refresh
- **Black & gold theme** with accessible dark mode styling

## Tech Stack
- Vue 3 (Vite)
- Composition API with `ref` and `computed`
- LocalStorage for cart persistence
- No backend or external packages

## Getting Started

### Installation
```powershell
npm install
```

### Run Locally
```powershell
npm run dev
```
Open the Vite dev server URL (usually `http://localhost:5173`).

### Build for Production
```powershell
npm run build
```

## Usage

1. **Browse courses** — view the full catalogue in the left section
2. **Filter** — narrow by availability or skill level
3. **Add to cart** — click "Add to Cart" on any available course
4. **Adjust quantity** — use +/− buttons in the cart to change amounts
5. **Remove items** — click the ✕ button or reduce quantity to zero
6. **View totals** — see subtotal, tax (15%), and grand total automatically calculated
7. **Clear cart** — click "Clear Cart" to empty all items
8. **Wishlist** — click the ♥ icon on courses to save favorites

## Project Structure
```
src/
  App.vue                  Main app: catalogue, cart state, layout
  components/
    Header.vue            Site header with brand
    CourseCard.vue        Individual course card with add-to-cart button
    Cart.vue              Shopping cart panel with summary
  assets/
    base.css              Global styles (black & gold theme)
    main.css              Additional global styles
```

## Success Criteria Met
✓ Users can add courses to the cart from the catalogue  
✓ Cart updates when quantities change or items are removed  
✓ Price summary recalculates automatically  
✓ Sold Out courses are clearly marked and cannot be added  
✓ Interface is organized, responsive, and error-free  
✓ Code shows good component structure and state management  

## Stretch Goals Implemented
✓ Cart persists on page refresh (localStorage)  
✓ Clear Cart button  
✓ Empty cart message  
✓ Responsive design with mobile optimization  

## Screenshot
![screenshot-placeholder](public/screenshot.png)

---
Module 1 — Frontend Web Development | Exercise 05 — Cooking Masterclass Checkout
