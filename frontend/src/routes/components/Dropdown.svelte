<script lang="ts">
	import { clickOutside } from '../../utils'
	import type { DropdownItem } from '../../utils'
	import { createEventDispatcher } from 'svelte'
	import Icon from 'svelte-awesome'
	import { faEllipsisH } from '@fortawesome/free-solid-svg-icons'

	let open = false

	export let dropdownItems: DropdownItem[]
	export let name: string | undefined = undefined
	// The dropdown is positioned versus its first relatively positioned partent
	// By default, the dropdown is positioned relative to its button
	// This can cause the dropdown to be hidden if it is in an overflow-hidden div.
	// In that case, set relative to false and control the dropdown positioning from the div
	export let relative = true

	const dispatch = createEventDispatcher()

	function openMenu() {
		open = true
	}

	function handleClickOutsideMenu(event: Event) {
		open = false
	}
</script>

<div
	class="ml-3 {relative ? 'relative' : ''} {$$props.class}"
	use:clickOutside
	on:click_outside={handleClickOutsideMenu}
>
	<button
		class="text-blue-500 text-right "
		id="dropdown"
		aria-expanded="false"
		aria-haspopup="true"
		on:click={openMenu}
	>
		{#if !name}
			<Icon data={faEllipsisH} scale={1.2} />
		{:else}
			{name}
		{/if}
	</button>
	<div
		class="flex flex-col z-50 origin-top-right absolute right-0 mt-2 w-48 rounded-md shadow-lg py-1 bg-white ring-1 ring-black ring-opacity-5 focus:outline-none {open
			? 'visible'
			: 'hidden'}"
		role="menu"
		tabindex="-1"
	>
		{#if dropdownItems}
			{#each dropdownItems as item, i}
				{#if item.action}
					<button
						on:click={() => {
							if (!item.disabled) {
								open = false
								item.action && item.action()
								dispatch('click', { item: item?.eventName })
							}
						}}
						class="block hover:drop-shadow-sm hover:bg-gray-50 hover:bg-opacity-30 px-4 py-2 text-sm text-gray-700 text-left{item.separatorTop
							? 'border-t'
							: ''} {item.separatorBottom ? 'border-b' : ''} {item.type == 'delete'
							? 'text-red-500'
							: ''}"
						role="menuitem"
						tabindex="-1"
						id="user-menu-item-{name}-{i}}"
						disabled={item.disabled}
						class:disabled={item.disabled}
					>
						{#if item.icon}
							<Icon
								data={item.icon}
								scale={0.6}
								class="inline mr-2 {item.type == 'delete' ? 'text-red-500' : 'text-gray-700'}"
							/>
						{/if}{item.displayName}
					</button>
				{:else if item.href}
					<a
						href={item.href}
						on:click={() => {
							if (!item.disabled) {
								open = false
							}
						}}
						class="block px-4 py-2 text-sm text-gray-700 hover:drop-shadow-sm hover:bg-gray-50 hover:bg-opacity-30"
						role="menuitem"
						tabindex="-1"
						id="user-menu-item-{name}-{i}}"
						class:disabled={item.disabled}
						>{#if item.icon}
							<Icon
								data={item.icon}
								scale={0.6}
								class="inline mr-2 {item.type == 'delete' ? 'text-red-500' : 'text-gray-700'}"
							/>
						{/if}{item.displayName}</a
					>
				{:else}
					<span
						class="block px-4 py-2 text-sm text-gray-700"
						role="menuitem"
						tabindex="-1"
						id="user-menu-item-{name}-{i}}">{item.displayName}</span
					>{/if}
			{/each}
		{/if}
	</div>
</div>
