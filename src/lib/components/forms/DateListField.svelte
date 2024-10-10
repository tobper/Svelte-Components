<script lang="ts">
	import { get_date_only_key, get_date_today, is_same_date, sort_dates, type DateOnly } from '@tobper/eon';
	import Button from '../Button.svelte';
	import CalendarMenu from '../CalendarMenu.svelte';
	import AddIcon from '../icons/AddIcon.svelte';
	import Field from './Field.svelte';

	interface DateListField {
		dates?: DateOnly[];
		label?: string;
		name?: string;
		readonly?: boolean;
		required?: boolean;
	}

	let {
		dates = $bindable([]),
		label,
		name,
		readonly,
		required,
	}: DateListField = $props();

	let input_element = $state<HTMLElement>();

	function is_same_date_as(other: DateOnly) {
		return (date: DateOnly) => is_same_date(date, other);
	}
</script>

<Field {label} {name} {required}>
	{#snippet content({ content_id, loading, in_progress })}
		<div bind:this={input_element} class="field-content">
			<div class="field-input" class:skeleton={loading}>
				{#each dates as date, index}
					{@const value = get_date_only_key(date)}

					<input type="hidden" {name} value={value} />

					<Button
						disabled={in_progress || readonly}
						onclick={() => { dates = dates.toSpliced(index, 1); }}
					>
						{value}
					</Button>
				{/each}
			</div>

			<Button
				class="add-date"
				disabled={in_progress || readonly}
				id={content_id}
				variant="add"
			>
				{#snippet icon()}
					<AddIcon />
				{/snippet}
			</Button>
		</div>

		<CalendarMenu
			anchor={input_element}
			modal
			trigger={content_id}
			selected_date={get_date_today()}
			on_select={selected_date => {
				if (selected_date && !dates.some(is_same_date_as(selected_date)))
					dates = sort_dates([...dates, selected_date]);
			}}
		/>
	{/snippet}
</Field>

<style>
	.field-input {
		flex-wrap: wrap;
		padding-left: calc(var(--field-input__padding-left) - .25rem);
		padding-block: .25rem;
		column-gap: var(--space__small);
	}

	.field-input :global(button) {
		height: 2rem;
		margin: 0;
		padding-inline: .25rem;
	}
</style>
