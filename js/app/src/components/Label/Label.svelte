<script lang="ts">
	import { createEventDispatcher } from "svelte";
	import { Label } from "@gradio/label";
	import { LineChart as LabelIcon } from "@gradio/icons";
	import { Block, BlockLabel, Empty } from "@gradio/atoms";
	import StatusTracker from "../StatusTracker/StatusTracker.svelte";
	import type { LoadingStatus } from "../StatusTracker/types";

	export let elem_id: string = "";
	export let elem_classes: Array<string> = [];
	export let visible: boolean = true;
	export let color: undefined | string = undefined;
	export let value: {
		label?: string;
		confidences?: Array<{ label: string; confidence: number }>;
	} = {};
	export let label: string = "Label";
	export let container: boolean = false;
	export let scale: number | null = null;
	export let min_width: number | undefined = undefined;
	export let loading_status: LoadingStatus;
	export let show_label: boolean;
	export let selectable: boolean = false;

	const dispatch = createEventDispatcher<{ change: undefined }>();

	$: ({ confidences, label: _label } = value);
	$: _label, confidences, dispatch("change");
</script>

<Block
	test_id="label"
	{visible}
	{elem_id}
	{elem_classes}
	{container}
	{scale}
	{min_width}
	padding={false}
>
	<StatusTracker {...loading_status} />
	{#if show_label}
		<BlockLabel Icon={LabelIcon} {label} disable={container === false} />
	{/if}
	{#if _label !== undefined && _label !== null}
		<Label on:select {selectable} {value} {show_label} {color} />
	{:else}
		<Empty unpadded_box={true}><LabelIcon /></Empty>
	{/if}
</Block>
