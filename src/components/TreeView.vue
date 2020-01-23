<template>
	<li class="tree-view__list">
		<span
			:class="[hasChildren(treeData) ? 'tree-view__list-name_active' : '', 'tree-view__list-name']"
			@click="openNode(treeData.value)"
		>
			<span :class="[isOpen ? 'tree-view__list-marker_active' : 'tree-view__list-marker']">
				{{ isOpen? '&#9660;' : '&#9658;' }}
			</span>
			{{ treeData.name }}
		</span>
		<ul v-if="isOpen">
			<div class="tree-view__list-value">{{ getSelectedValue(treeData.value) }}</div>
			<template v-if="hasChildren(treeData)">
				<TreeView
					v-for="(child, index) in treeData.phones"
					:key="index"
					:treeData="child"
					@select-another-value="selectAnotherValue"
					:value="value"
				/>
			</template>
		</ul>
	</li>
</template>

<script>
export default {
	name: 'TreeView',
	props: ['treeData', 'value'],
	data() {
		return {
			isOpen: false,
			activeClass: "tree-view__list-name_active"
		}
	},
	methods: {
		hasChildren(item) {
			return (item.phones.length > 1)
		},
		openNode(value) {
			this.$emit('select-another-value', value)
			this.isOpen = !this.isOpen
		},
		selectAnotherValue(value) {
			this.$emit('select-another-value', value)
		},
		getSelectedValue(value) {
			return this.value === value ? `Value: ${this.value}` : 'Value: none'
		}
	}
}
</script>

<style>
	.tree-view__list {
		display: flex;
		flex-direction: column;
	}
	.tree-view__list-name {
		opacity: 0.4;
		width: fit-content;
		border: 1px solid rgba(0,0,0,0.1);
		border-radius: 4px;
		padding: 5px 10px;
		margin-bottom: 4px;
		cursor: pointer;
		user-select: none; 
	}
	.tree-view__list-name_active {
		opacity: 1;
		border: 1px solid rgba(0,153,0,0.2);
	}
	.tree-view__list-name_active:hover {
		background-color: rgba(0,153,0,0.1);
	}
	.tree-view__list-value {

		margin-bottom: 4px;
		width: fit-content;
		padding: 0 5px;
		text-align: center;
		border-radius: 5px;
	}
	.tree-view__list-marker_active {
		color: rgba(0,153,0,1);
	}
</style>