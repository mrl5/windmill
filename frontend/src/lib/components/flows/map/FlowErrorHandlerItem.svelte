<script lang="ts">
	import type { FlowEditorContext } from '../types'
	import { getContext } from 'svelte'
	import Icon from 'svelte-awesome'
	import { faBug } from '@fortawesome/free-solid-svg-icons'
	import { NEVER_TESTED_THIS_FAR } from '$lib/components/flows/flowStateUtils'
	import { classNames, emptyModule, emptySchema } from '$lib/utils'
	import { flowStateStore, type FlowModuleState } from '../flowState'
	import Toggle from '$lib/components/Toggle.svelte'
	import { flowStore } from '../flowStore'

	const { select, selectedId } = getContext<FlowEditorContext>('FlowEditorContext')

	function onToggle() {
		if ($flowStore.value.failure_module) {
			$flowStore.value.failure_module = undefined
			// By default, we return to settings when disabling the failure module
			select('settings')
		} else {
			const failureModule: FlowModuleState = {
				schema: emptySchema(),
				previewResult: NEVER_TESTED_THIS_FAR,
				childFlowModules: []
			}
			$flowStateStore.failureModule = failureModule
			$flowStore.value.failure_module = emptyModule()
			select('failure')
		}
	}
</script>

<div
	on:click={() => {
		if ($flowStore.value.failure_module) {
			select('failure')
		} else {
			onToggle()
		}
	}}
	class={classNames(
		'border rounded-md p-2 bg-white text-sm cursor-pointer mt-4 flex flex-col overflow-x-hidden',
		$selectedId.includes('failure') ? 'outline outline-offset-1 outline-2 outline-slate-900' : ''
	)}
>
	<div class=" flex justify-between items-center flex-wrap">
		<div>
			<Icon data={faBug} class="mr-2" />
			<span class="font-bold">Error handler</span>
		</div>
		<Toggle checked={Boolean($flowStore.value.failure_module)} on:change={onToggle} />
	</div>

	<div class="w-full truncate block">
		{#if Boolean($flowStore.value.failure_module)}
			<span
				>{$flowStore.value.failure_module?.summary ||
					($flowStore.value.failure_module?.value.type === 'rawscript'
						? `Inline ${$flowStore.value.failure_module?.value.language}`
						: 'Select a script')}</span
			>
		{/if}
	</div>
</div>
