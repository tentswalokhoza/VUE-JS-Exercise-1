<template>
	<header class="site-header">
		<div class="brand">
			<h1>Cooking Masterclass</h1>
			<p class="tag">Curated workshops from expert chefs</p>
		</div>

		<div class="header-controls">
			<div class="wishlist" role="button" tabindex="0" @click="onToggle" aria-pressed="false">
				<svg class="heart" viewBox="0 0 24 24" width="18" height="18" aria-hidden="true">
					<path fill="currentColor" d="M12.1 21.35l-1.1-1.02C5.14 15.36 2 12.28 2 8.5 2 6 4 4 6.5 4c1.54 0 3.04.99 3.57 2.36h1.87C13.46 4.99 14.96 4 16.5 4 19 4 21 6 21 8.5c0 3.78-3.14 6.86-8.9 11.83l-1 1.02z"/>
				</svg>
				<span class="label">Saved</span>
				<span class="count">{{ wishlistCount }}</span>
			</div>
		</div>
	</header>
</template>

<script>
export default {
	name: 'AppHeader',
	emits: ['toggle-wishlist'],
	props: {
		wishlistCount: {
			type: Number,
			default: 0
		}
	}
,
  methods: {
    onToggle() {
      this.$emit('toggle-wishlist')
    }
  }
}
</script>


<style scoped>
.site-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
	padding: 1rem 0;
	border-bottom: 1px solid var(--color-border);
	margin-bottom: 1rem;
}
.brand h1 {
	margin: 0;
	font-size: 1.25rem;
	color: var(--color-heading);
}
.brand .tag {
	margin: 0;
	font-size: 0.85rem;
	color: var(--color-text);
}
.wishlist {
	display: inline-flex;
	gap: 0.5rem;
	align-items: center;
	font-weight: 600;
}
.wishlist .heart {
	color: #d4af37;
}
.wishlist .count {
	background: rgba(212,175,55,0.12);
	padding: 0.15rem 0.5rem;
	border-radius: 999px;
	font-size: 0.95rem;
	color: #0b0b0b;
}
</style>

