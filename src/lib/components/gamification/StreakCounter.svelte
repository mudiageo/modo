<script lang="ts">
	import { achievementsStore } from '$lib/data/achievements.svelte.ts';
	import { slide } from 'svelte/transition';

	const accomplishments = $state(achievementsStore.data || {});

	const streak = $state(
		accomplishments.streak || {
			currentStreak: 0,
			longestStreak: 4,
			lastStudyDate: ''
		}
	);
	let streakClass = $derived(
		streak.currentStreak >= 7
			? 'text-primary-600'
			: streak.currentStreak >= 3
				? 'text-amber-600'
				: 'text-gray-600 dark:text-white'
	);
</script>

<div class="flex items-center gap-2" in:slide>
	<svg class="h-6 w-6 {streakClass}" fill="none" stroke="currentColor" viewBox="0 0 24 24">
		<path
			stroke-linecap="round"
			stroke-linejoin="round"
			stroke-width="2"
			d="M17.657 18.657A8 8 0 016.343 7.343S7 9 9 10c0-2 .5-5 2.986-7C14 5 16.09 5.777 17.656 7.343A7.975 7.975 0 0120 13a7.975 7.975 0 01-2.343 5.657z"
		/>
	</svg>
	<div>
		<p class="font-medium {streakClass}">{streak.currentStreak} Day Streak</p>
		<p class="text-xs text-gray-500 dark:text-white">Best: {streak.longestStreak} days</p>
	</div>
</div>
