<template>
	<div>
		<div contenteditable="true" v-html="theHtml" ref="editArea"></div>
		<button @click="change">大小</button>
	</div>
</template>

<script>
function isTextNode(theNode) {
	return theNode.nodeType === Node.TEXT_NODE;
}

function containerParentIsASpanContainedByEditArea(parent, vm) {
	return parent.tagName === "SPAN" && vm.$refs.editArea.contains(parent);
}

export default {
	name: "App",
	data() {
		return {
			theHtml: `QQ<span>bbc</span>PP`
		};
	},
	methods: {
		change() {
			this.preAction();
		},
		preAction() {
			// 1、获取 range
			let theRange = window.getSelection().getRangeAt(0);
			const startContainer = theRange.startContainer;
			const startContainerParent = startContainer.parentElement;
			const endContainer = theRange.endContainer;
			// 2、判断是否 collapsed
			if (theRange.collapsed === false) {
				// 非 collapsed 表明当前有内容已被选中
				// 3、查看 startContainer 的节点类型
				if (isTextNode(startContainer)) {
					// 表明 startContainer 节点是文本节点
					// 再 startOffset 是否为 0 。为 0 则表明该节点全部选中了。
					if (theRange.startOffset === 0) {
						// 4、检查 startContainer.parentElement 是1个span元素
						// 并且 editArea 包含这个元素
						// if (startContainerParent.tagName === "SPAN" && this.$refs.editArea.contains(startContainerParent)) {
						if (containerParentIsASpanContainedByEditArea(startContainerParent, this)) {
							// 5、将range的start变更成 startContainerParent
							theRange.setStartBefore(startContainer);
						}
					}
				}
				// 6、查看 endContainer 的节点类型
				if (isTextNode(endContainer)) {
					console.log(endContainer);
				}
			}
		}
	}
};
</script>

<style>
body {
	margin: 0;
}
</style>
