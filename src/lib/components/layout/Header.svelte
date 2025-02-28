<script lang="ts">
	import { theme } from '$lib/stores/theme';
	import { slide, fade } from 'svelte/transition';
	import { profileStore } from '$lib/data/index.svelte.ts';
  import { signOut } from 'svelte-guardian/client'

	import Icon from '$lib/icons/Icon.svelte';

	const profile = profileStore.data || {};

	function toggleTheme() {
		$theme = $theme === 'light' ? 'dark' : 'light';
	}

	import { clickOutside } from '$lib/actions/clickOutside';

	let isOpen = $state(false);

	const menuItems = [
		{ label: 'Courses', href: '/app/courses', icon: 'cap' },
		{ label: 'Achievements', href: '/app/achievements', icon: 'trophy' },
		{ label: 'Resources', href: '/app/resources', icon: 'folder' },
		{ label: 'Notes', href: '/app/notes', icon: 'note' },
		{ label: 'Settings', href: '/app/settings', icon: 'cog' },
		{ href: '/app/progress', icon: 'chart', label: 'Progress' },
		{ label: 'Help', href: '/app/help', icon: 'help' }
	];
</script>

<!-- Mobile Header -->
<header
	class="fixed left-0 right-0 top-0 z-50 bg-white px-4 py-3 shadow-sm dark:bg-gray-800 md:hidden"
>
	<div class="flex items-center justify-between">
		<div class="flex items-center gap-3">
			<a href="/app/profile" class="text-gray-600 dark:text-gray-300" aria-label="Profile">
				<Icon icon="user" h="6" w="6" />
			</a>
			<span class="font-medium text-gray-900 dark:text-white">
				Hi, {profile?.name?.split(' ')[0] || 'User'}
			</span>
		</div>

		<div class="flex items-center gap-4">
			<button onclick={toggleTheme} class="text-gray-600 dark:text-gray-300">
				<Icon icon={$theme === 'dark' ? 'dark' : 'light'} h="6" w="6" />
			</button>

			<button
				class="flex items-center rounded-full p-2 hover:bg-gray-100 hover:dark:bg-gray-700"
				onclick={() => (isOpen = !isOpen)}
			>
				<svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M12 5v.01M12 12v.01M12 19v.01M12 6a1 1 0 110-2 1 1 0 010 2zm0 7a1 1 0 110-2 1 1 0 010 2zm0 7a1 1 0 110-2 1 1 0 010 2z"
					/>
				</svg>
			</button>

			{#if isOpen}
				<div
					class="absolute right-0 top-10 mt-2 w-48 rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 dark:bg-gray-800"
					use:clickOutside={() => (isOpen = false)}
					in:fade={{ duration: 100 }}
					out:fade={{ duration: 100 }}
				>
					<div class="py-1" role="menu">
						{#each menuItems as item}
							<a
								href={item.href}
								class="flex px-4 py-2 text-sm text-gray-600 hover:text-primary-600 dark:text-gray-300"
								role="menuitem"
							>
								<span class="mr-2 flex h-6 w-6 items-center">
									<Icon icon={item.icon} h="6" w="6" />
								</span>
								<span class="flex items-center">
									{item.label}
								</span>
							</a>
						{/each}
            <button class="flex px-4 py-2 text-sm text-gray-600 hover:text-primary-600 dark:text-gray-300" onclick={signOut}>
              <span class="mr-2 flex h-6 w-6 items-center">
                <svg
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"
                />
              </svg>
              </span>
              <span class="flex items-center">
                Signout
              </span>
            </button>
					</div>
				</div>
			{/if}
		</div>
	</div>
</header>
