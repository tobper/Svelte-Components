<script lang="ts">
	import type { DateOnly } from '@tobper/eon';
	import Calendar from './Calendar.svelte';
	import Menu from './Menu.svelte';

	interface CalendarMenu {
		/**
		 * The id of the currently activated date.  
		 * Used to set active descendant in parent controls.
		 */
		active_descendant?: string | null;
		anchor: string | HTMLElement;
		/**
		 * Element to attach navigation keyboard events to.
		 */
		keyboard_capture?: HTMLElement | string;
		modal?: boolean;
		/**
		 * The currently selected date.
		 */
		selected_date?: DateOnly | null;
		trigger?: string | HTMLElement;
		visible?: boolean;
		/**
		 * Callback is called when a date is selected.
		 */
		 on_select?: (new_date: DateOnly | null) => void;
	}

	let {
		active_descendant = $bindable(null),
		anchor,
		keyboard_capture,
		modal,
		selected_date = $bindable(null),
		trigger,
		visible = $bindable(false),
		on_select,
	}: CalendarMenu = $props();
	let calendar = $state<ReturnType<typeof Calendar>>();
</script>

<Menu
	bind:visible
	{modal}
	{anchor}
	{trigger}
	on_open={() => {
		calendar?.reset();
	}}
>
	<Calendar
		bind:this={calendar}
		bind:active_descendant
		bind:selected_date
		focusable={false}
		keyboard_capture={visible ? keyboard_capture : undefined}
		on_select={new_date => {
			visible = false;
			on_select?.(new_date);
		}}
	/>
</Menu>