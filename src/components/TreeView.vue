<template>
	<li class="tree-view__list">
		<span
			:class="[isItemOpen(item.id) ? 'tree-view__list-name_is-open' : '', 'tree-view__list-name']"
			@click="openNode(item)"
		>
			<span :class="[isItemOpen(item.id) ? 'tree-view__list-marker_active' : 'tree-view__list-marker']">
				{{ isItemOpen(item.id)? '&#9660;' : '&#9658;' }}
			</span>
			<span v-html="getHighlitedName()"></span>
		</span>
		<ul v-if="isItemOpen(item.id)">
			<div class="tree-view__list-value">{{ getSelectedValue(item.value) }}</div>
			<template>
				<TreeView
					v-for="(child, index) in thisTreeData"
					:key="index"
					:item="child"
					:value="value"
					:treeData="childTreeData"
					:searchInputText="searchInputText"
					:openedNodes="openedNodes"
					@select-another-value="selectAnotherValue"
					@open='open'
					@close='close'
				/>
			</template>
		</ul>
	</li>
</template>

<script>
export default {
	name: 'TreeView',
	props: {
		item: Object,
		value: String,
		treeData: Array,
		searchInputText: String,
		openedNodes: Array
	},
	methods: {
		hasChildren(id) {
			return (!id)
		},
		isItemOpen(id){
			return !!this.openedNodes.find(i => i === id)
		},
		openNode(item) {
			this.$emit('select-another-value', item.value)
			if(!this.openedNodes.find(i => i === item.id))
				this.open(item.id)
			else
				this.close(item.id)
		},
		open(id) {
			this.$emit('open', id)
		},
		close(id) {
			this.$emit('close', id)
		},
		selectAnotherValue(value) {
			this.$emit('select-another-value', value)
		},
		getSelectedValue(value) {
			return this.value === value ? `Value: ${this.value}` : ''
		},
		getHighlitedName() {
			if(!this.searchInputText) {
				return this.item.name;
			}
			return this.item.name.replace(new RegExp(this.searchInputText, "gi"), match => {
				return '<span class="highlightText">' + match + '</span>';
			});
		}
	},
	computed: {
		childTreeData: function () {
			return this.treeData
		},
		thisTreeData: function () {
			return this.treeData.filter(item => item.parentId === this.item.id)
		},
	}
}
</script>

<style>
	.tree-view__list {
		display: flex;
		flex-direction: column;
	}
	.tree-view__list-name {
		width: fit-content;
		border: 1px solid rgba(0,0,0,0.1);
		border-radius: 4px;
		padding: 5px 10px;
		margin-bottom: 6px;
		cursor: pointer;
		user-select: none;
		border: 1px solid rgba(0,153,0,0.2);
	}
	.tree-view__list-name_is-open {
		box-shadow: 2px 4px 2px 0px rgba(0,0,0, 0.25);
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
	.highlightText {
		background-color: yellow;
		border-radius: 5px;
	}
</style>


