<script lang="ts">
	import { Button } from "@gradio/button";
	import { createEventDispatcher } from "svelte";
	import type { FileData } from "@gradio/upload";

	export let elem_id: string = "";
	export let elem_classes: Array<string> = [];
	export let visible: boolean = true;
	export let file_count: string;
	export let file_types: Array<string> = ["file"];
	export let include_file_metadata = true;
	export let size: "sm" | "lg" = "lg";
	export let scale: number | null = null;
	export let min_width: number | undefined = undefined;
	export let mode: "static" | "dynamic" = "dynamic";
	export let variant: "primary" | "secondary" | "stop" = "secondary";

	let hidden_upload: HTMLInputElement;
	const dispatch = createEventDispatcher();
	let accept_file_types: string | null;
	if (file_types == null) {
		accept_file_types = null;
	} else {
		file_types = file_types.map((x) => {
			if (x.startsWith(".")) {
				return x;
			} else {
				return x + "/*";
			}
		});
		accept_file_types = file_types.join(", ");
	}

	const openFileUpload = () => {
		hidden_upload.click();
	};

	const loadFiles = (files: FileList) => {
		let _files: Array<File> = Array.from(files);
		if (!files.length) {
			return;
		}
		if (file_count === "single") {
			_files = [files[0]];
		}
		var all_file_data: Array<FileData | File> = [];
		_files.forEach((f, i) => {
			all_file_data[i] = include_file_metadata
				? {
						name: f.name,
						size: f.size,
						data: "",
						blob: f
				  }
				: f;
			if (
				all_file_data.filter((x) => x !== undefined).length === files.length
			) {
				dispatch(
					"load",
					file_count == "single" ? all_file_data[0] : all_file_data
				);
			}
		});
	};

	const loadFilesFromUpload = (e: Event) => {
		const target = e.target as HTMLInputElement;
		if (!target.files) {
			return;
		}
		loadFiles(target.files);
	};

	const clearInputValue = (e: Event) => {
		const target = e.target as HTMLInputElement;
		if (target.value) target.value = "";
	};
</script>

<input
	class="hide"
	accept={accept_file_types}
	type="file"
	bind:this={hidden_upload}
	on:change={loadFilesFromUpload}
	on:click={clearInputValue}
	multiple={file_count === "multiple" || undefined}
	webkitdirectory={file_count === "directory" || undefined}
	mozdirectory={file_count === "directory" || undefined}
/>

<Button
	{size}
	{variant}
	{elem_id}
	{elem_classes}
	{visible}
	on:click={openFileUpload}
	{scale}
	{min_width}
	disabled={mode === "static"}
>
	<slot />
</Button>

<style>
	.hide {
		display: none;
	}
</style>
