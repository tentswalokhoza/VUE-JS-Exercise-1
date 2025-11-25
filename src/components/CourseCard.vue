<template>
	<article class="card">
		<div class="media">
			<div class="thumb" :aria-hidden="true">
				<div class="thumb-placeholder">{{ chefInitials }}</div>
			</div>
			<div class="meta">
				<h3 class="title">{{ course.title }}</h3>
				<div class="chef">by {{ course.chef }}</div>
				<div class="details">
					<span class="level">{{ course.level }}</span>
					<span class="price">{{ formattedPrice }}</span>
				</div>
			</div>
		</div>

		<div class="actions">
			<button
				class="save-btn"
				:class="{ saved: saved }"
				:disabled="!course.available"
				@click="onToggle"
			>
				<span v-if="course.available">{{ saved ? 'Saved' : 'Save' }}</span>
				<span v-else>Sold Out</span>
			</button>
			<span v-if="!course.available" class="sold">Sold Out</span>
		</div>
	</article>
</template>

<script>
export default {
	name: 'CourseCard',
	props: {
		course: { type: Object, required: true },
		saved: { type: Boolean, default: false }
	},
	emits: ['toggle-save'],
	computed: {
		formattedPrice() {
			try {
				return new Intl.NumberFormat(undefined, {
					style: 'currency',
					currency: this.course.currency || 'USD'
				}).format(this.course.price)
			} catch {
				return '$' + (this.course.price ?? '')
			}
		},
		chefInitials() {
			if (!this.course.chef) return ''
			return this.course.chef
				.split(' ')
				.map(n => n[0])
				.slice(0, 2)
				.join('')
				.toUpperCase()
		}
	},
	methods: {
		onToggle() {
			this.$emit('toggle-save', this.course.id)
		}
	}
}
</script>

<style scoped>
.card {
	background: var(--color-background-soft);
	border: 1px solid var(--color-border);
	padding: 1rem;
	border-radius: 8px;
	display: flex;
	flex-direction: column;
	gap: 0.75rem;
}
.media {
	display: flex;
	gap: 0.75rem;
	align-items: center;
}
.thumb {
	width: 72px;
	height: 72px;
	border-radius: 8px;
	background: linear-gradient(135deg,#fde68a,#fca5a5);
	display:flex;
	align-items:center;
	justify-content:center;
	color: #333;
	font-weight:700;
}
.thumb-placeholder { font-size: 1.25rem }
.meta .title { margin: 0; font-size: 1rem }
.chef { color: var(--color-text); font-size: 0.9rem }
.details { display:flex; gap:0.75rem; font-size:0.9rem; color:var(--color-text) }
.actions { display:flex; align-items:center; gap:0.5rem }
.save-btn { padding: 0.5rem 0.85rem; border-radius: 6px; border: none; cursor: pointer; background: #2c9f6d; color: white }
.save-btn.saved { background: #20674a }
.save-btn:disabled { opacity: 0.6; cursor: not-allowed; background: #9aa39a }
.sold { color: #e2574c; font-weight:700 }

@media (min-width: 768px) {
	.card { flex-direction: row; justify-content: space-between; align-items: center }
	.media { align-items: center }
}
</style>

