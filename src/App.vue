<template>
	<Box
		v-bind="$attrs"
		:class="rootClass"
		:expand="expand ? '{`default`:{`xs`:{`light`:true}}}' : ''"
		:style="style"
		:title="route"
		ref="navitem"
		class="vp-navigator-item"
		@click.stop="!tag ? ($event.pointerType !== 'mouse' || $vertical) && $slots.default && !route ? toggle() : go($event, route, external, target) : ''"
		@pointerover="$event.pointerType !== 'mouse' || $vertical ? '' : show=true"
		@pointerover.stop="hover=true"
		@pointerleave="$event.pointerType !== 'mouse' || $vertical ? '' : show=false"
		@pointerleave.stop="hover=false,active=false"
		@focus.stop="focus=true"
		@blur.stop="focus=false"
		@pointerdown.stop="active=true"
		@pointerup.stop="active=false"
	>
		<component
			v-if="icon"
			:is="tag || (external ? 'a' : 'router-link')"
			:to="external ? undefined : (route || '')"
			:href="!external ? undefined : (route || '')"
			:target="target"
			class="vp-navigator-item--link"
			style="display:inline-flex;align-items:center"
		>
			<div
				:style="{
					'background-image': $icon,
					'width': $iconSize
				}"
				style="background-repeat:no-repeat;background-size:contain;background-position:center;aspect-ratio:1/1"
			/>
		</component>
		<component
			class="vp-navigator-item--link"
			:is="tag || (external ? 'a' : 'router-link')"
			:to="external ? undefined : (route || '')"
			:href="!external ? undefined : (route || '')"
			:target="target"
			:style="{
				marginLeft: $verticalLeftIndent,
				marginRight: $verticalRightIndent,
				justifyContent: style.justifyContent,
			}"
			style="white-space: nowrap;display:inline-flex;align-items:center;flex-grow:1"
		>
			{{ title }}
		</component>
		<div
			v-if="$slots.default"
			@click.stop="toggle()"
			class="vp-navigator-item--link vp-navigator-item--arrow"
			style="display:inline-flex;align-items:center"
		>
			<div
				:style="{
					'background-image': $caret,
					'width': $caretSize
				}"
				style="background-repeat:no-repeat;background-size:contain;background-position:center;aspect-ratio:1/1"
			/>
		</div>
		<template v-if="!$vertical && $slots.default && (show || forceOpen || forceOpenProvider)">
			<div
				class="wrapper"
				:class="{
					'vp-navigator-item--up': drop === 'up'
				}"
				:style="{
					'z-index': level ? level + 1 : 1,
					...wrapperStyle
				}"
			>
				<slot />
			</div>
		</template>
	</Box>
	<template v-if="$vertical">
		<div
			v-show="$slots.default && (show || forceOpen || forceOpenProvider)"
			class="wrapper"
			:class="{
				'vp-navigator-item--up': drop === 'up'
			}"
			:style="{
				'z-index': level ? level + 1 : 1,
				...wrapperStyle
			}"
		>
			<slot />
		</div>
	</template>
</template>
<script>
	import {
		computed
	} from 'vue';
	import Box from '@vueplayio/box';
	export default {
		inject: ['path', 'pathId', 'setPath', 'theme', 'breakpoint', 'small', 'open', 'forceOpenProvider', 'direction', 'center', 'orientation', 'drop', 'level', 'order', 'reverseIcon', 'childrenIconSizeProvider', 'childrenCaretProvider', 'childrenCaretSizeProvider', 'borderRadiusDropProvider', 'backgroundDropAreaProvider', 'model'],
		provide() {
			const self = this;
			return {
				setPath(path, id) {
					if (self.currentPathId !== id) {
						self.currentPath = path;
						self.currentPathId = id;
						if (typeof self.setPath === 'function') {
							self.setPath(path, id);
						}
					}
				},
				open: computed(() => this.show),
				forceOpenProvider: computed(() => this.forceOpen || this.forceOpenProvider),
				level: computed(() => this.level ? this.level + 1 : 1),
				order: computed(() => this.order === 'odd' ? 'even' : 'odd'),
				reverseIcon: computed(() => !this.iconReverse && !this.childrenIconReverse ? this.reverseIcon : this.childrenIconReverse ? this.childrenIconReverse === 'true' : this.iconReverse === 'true'),
				childrenIconSizeProvider: computed(() => this.childrenIconSize || this.childrenIconSizeProvider),
				childrenCaretProvider: computed(() => this.childrenCaret || this.childrenCaretProvider),
				childrenCaretSizeProvider: computed(() => this.childrenCaretSize || this.childrenCaretSizeProvider),
				borderRadiusDropProvider: computed(() => this.borderRadiusDrop || this.borderRadiusDropProvider),
				backgroundDropAreaProvider: computed(() => this.backgroundDropArea || this.backgroundDropAreaProvider),
				direction: computed(() => this.childrenItemDirection ? this.childrenItemDirection : this.itemDirection ? this.itemDirection : this.direction),
				center: computed(() => this.childrenCenterDropdown ? this.childrenCenterDropdown : this.centerDropdown ? this.centerDropdown : this.center),
				model: computed(() => {
					const model = this.childModel;
					if (this.childrenBorderRadius) {
						try {
							model.borderRadius = JSON.parse(this.childrenBorderRadius.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenGap) {
						try {
							model.gap = JSON.parse(this.childrenGap.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenVerticalLeftIndent) {
						try {
							model.verticalLeftIndent = JSON.parse(this.childrenVerticalLeftIndent.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenVerticalRightIndent) {
						try {
							model.verticalRightIndent = JSON.parse(this.childrenVerticalRightIndent.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenFontSize) {
						try {
							model.fontSize = JSON.parse(this.childrenFontSize.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenFontWeight) {
						try {
							model.fontWeight = JSON.parse(this.childrenFontWeight.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenColor) {
						try {
							model.color = JSON.parse(this.childrenColor.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenBackgroundColor) {
						try {
							model.backgroundColor = JSON.parse(this.childrenBackgroundColor.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenBackgroundImage) {
						try {
							model.backgroundImage = JSON.parse(this.childrenBackgroundImage.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenBorder) {
						try {
							model.border = JSON.parse(this.childrenBorder.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenMargin) {
						try {
							model.margin = JSON.parse(this.childrenMargin.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenPadding) {
						try {
							model.padding = JSON.parse(this.childrenPadding.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenTransform) {
						try {
							model.transform = JSON.parse(this.childrenTransform.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenWidth) {
						try {
							model.width = JSON.parse(this.childrenWidth.replaceAll('`', '"'));
						} catch (e) {}
					}
					if (this.childrenHeight) {
						try {
							model.height = JSON.parse(this.childrenHeight.replaceAll('`', '"'));
						} catch (e) {}
					}
					return model;
				})
			};
		},
		props: {
			title: {
				type: String,
				default: 'Title'
			},
			route: {
				type: String,
				default: ''
			},
			external: {
				type: Boolean,
				default: false
			},
			tag: {
				type: String,
				default: ''
			},
			target: {
				type: String,
				default: '',
				options: [{
					key: 'Default',
					value: ''
				}, {
					key: 'Blank',
					value: '_blank'
				}, {
					key: 'Self',
					value: '_self'
				}, {
					key: 'Parent',
					value: '_parent'
				}, {
					key: 'Top',
					value: '_top'
				}]
			},
			icon: {
				type: String,
				control: 'media',
				default: '',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			iconSize: {
				type: String,
				default: '',
				control: 'slider'
			},
			childrenIconSize: {
				type: String,
				default: '',
				control: 'slider'
			},
			caret: {
				type: String,
				control: 'media',
				default: ''
			},
			childrenCaret: {
				type: String,
				control: 'media',
				default: ''
			},
			caretSize: {
				type: String,
				default: '',
				control: 'slider'
			},
			childrenCaretSize: {
				type: String,
				default: '',
				unit: 'px',
				control: 'slider'
			},
			verticalLeftIndent: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px'
			},
			childrenVerticalLeftIndent: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px'
			},
			verticalRightIndent: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px'
			},
			childrenVerticalRightIndent: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px'
			},
			iconReverse: {
				type: String,
				default: '',
				options: [{
					key: 'Inherit',
					value: ''
				}, {
					key: 'Yes',
					value: 'true'
				}, {
					key: 'No',
					value: 'false'
				}]
			},
			childrenIconReverse: {
				type: String,
				default: '',
				options: [{
					key: 'Inherit',
					value: ''
				}, {
					key: 'Yes',
					value: 'true'
				}, {
					key: 'No',
					value: 'false'
				}]
			},
			itemDirection: {
				type: String,
				default: '',
				options: [{
					key: 'Inherit',
					value: ''
				}, {
					key: 'Left',
					value: 'left'
				}, {
					key: 'Right',
					value: 'right'
				}]
			},
			childrenItemDirection: {
				type: String,
				default: '',
				options: [{
					key: 'Inherit',
					value: ''
				}, {
					key: 'Left',
					value: 'left'
				}, {
					key: 'Right',
					value: 'right'
				}]
			},
			centerDropdown: {
				type: String,
				default: '',
				options: [{
					key: 'Inherit',
					value: ''
				}, {
					key: 'Yes',
					value: 'true'
				}, {
					key: 'No',
					value: 'false'
				}]
			},
			childrenCenterDropdown: {
				type: String,
				default: '',
				options: [{
					key: 'Inherit',
					value: ''
				}, {
					key: 'Yes',
					value: 'true'
				}, {
					key: 'No',
					value: 'false'
				}]
			},
			expand: {
				type: Boolean,
				default: null
			},
			forceOpen: {
				type: Boolean,
				default: false
			},
			fontSize: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenFontSize: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			fontWeight: {
				type: String,
				default: '',
				control: 'slider',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenFontWeight: {
				type: String,
				default: '',
				control: 'slider',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			color: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			backgroundDropArea: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			backgroundColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			backgroundColorDrop: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenBackgroundColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			backgroundImage: {
				type: String,
				default: '',
				control: 'media',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenBackgroundImage: {
				type: String,
				default: '',
				control: 'media',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			gap: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenGap: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			width: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			minWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			maxWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			height: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenHeight: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			minHeight: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			maxHeight: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			border: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenBorder: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderTopColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderRightColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderBottomColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderLeftColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderTopWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderRightWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderBottomWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderLeftWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderTopStyle: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'none',
					key: 'none'
				}, {
					value: 'hidden',
					key: 'hidden'
				}, {
					value: 'solid',
					key: 'solid'
				}, {
					value: 'dashed',
					key: 'dashed'
				}, {
					value: 'dotted',
					key: 'dotted'
				}, {
					value: 'double',
					key: 'double'
				}, {
					value: 'groove',
					key: 'groove'
				}, {
					value: 'ridge',
					key: 'ridge'
				}, {
					value: 'inset',
					key: 'inset'
				}, {
					value: 'outset',
					key: 'outset'
				}],
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderStyle: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'none',
					key: 'none'
				}, {
					value: 'hidden',
					key: 'hidden'
				}, {
					value: 'solid',
					key: 'solid'
				}, {
					value: 'dashed',
					key: 'dashed'
				}, {
					value: 'dotted',
					key: 'dotted'
				}, {
					value: 'double',
					key: 'double'
				}, {
					value: 'groove',
					key: 'groove'
				}, {
					value: 'ridge',
					key: 'ridge'
				}, {
					value: 'inset',
					key: 'inset'
				}, {
					value: 'outset',
					key: 'outset'
				}],
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderRightStyle: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'none',
					key: 'none'
				}, {
					value: 'hidden',
					key: 'hidden'
				}, {
					value: 'solid',
					key: 'solid'
				}, {
					value: 'dashed',
					key: 'dashed'
				}, {
					value: 'dotted',
					key: 'dotted'
				}, {
					value: 'double',
					key: 'double'
				}, {
					value: 'groove',
					key: 'groove'
				}, {
					value: 'ridge',
					key: 'ridge'
				}, {
					value: 'inset',
					key: 'inset'
				}, {
					value: 'outset',
					key: 'outset'
				}],
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderBottomStyle: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'none',
					key: 'none'
				}, {
					value: 'hidden',
					key: 'hidden'
				}, {
					value: 'solid',
					key: 'solid'
				}, {
					value: 'dashed',
					key: 'dashed'
				}, {
					value: 'dotted',
					key: 'dotted'
				}, {
					value: 'double',
					key: 'double'
				}, {
					value: 'groove',
					key: 'groove'
				}, {
					value: 'ridge',
					key: 'ridge'
				}, {
					value: 'inset',
					key: 'inset'
				}, {
					value: 'outset',
					key: 'outset'
				}],
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderLeftStyle: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'none',
					key: 'none'
				}, {
					value: 'hidden',
					key: 'hidden'
				}, {
					value: 'solid',
					key: 'solid'
				}, {
					value: 'dashed',
					key: 'dashed'
				}, {
					value: 'dotted',
					key: 'dotted'
				}, {
					value: 'double',
					key: 'double'
				}, {
					value: 'groove',
					key: 'groove'
				}, {
					value: 'ridge',
					key: 'ridge'
				}, {
					value: 'inset',
					key: 'inset'
				}, {
					value: 'outset',
					key: 'outset'
				}],
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenBorderRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderRadiusDrop: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderTopLeftRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderTopRightRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderBottomRightRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			borderBottomLeftRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			padding: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenPadding: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			paddingTop: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			paddingRight: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			paddingBottom: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			paddingLeft: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			margin: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			childrenMargin: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			marginTop: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			marginRight: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			marginBottom: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			marginLeft: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			justifyContent: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'flex-start',
					key: 'flex-start'
				}, {
					value: 'flex-end',
					key: 'flex-end'
				}, {
					value: 'center',
					key: 'center'
				}, {
					value: 'space-between',
					key: 'space-between'
				}, {
					value: 'space-around',
					key: 'space-around'
				}, {
					value: 'space-evenly',
					key: 'space-evenly'
				}, {
					value: 'stretch',
					key: 'stretch'
				}, {
					value: 'normal',
					key: 'normal'
				}, {
					value: 'start',
					key: 'start'
				}, {
					value: 'end',
					key: 'end'
				}, {
					value: 'left',
					key: 'left'
				}, {
					value: 'right',
					key: 'right'
				}, {
					value: 'inherit',
					key: 'inherit'
				}, {
					value: 'initial',
					key: 'initial'
				}, {
					value: 'revert',
					key: 'revert'
				}, {
					value: 'revert-layer',
					key: 'revert-layer'
				}, {
					value: 'unset',
					key: 'unset'
				}],
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover', 'current', 'active', 'focus']
			},
			position: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'relative',
					key: 'relative'
				}, {
					value: 'absolute',
					key: 'absolute'
				}, {
					value: 'fixed',
					key: 'fixed'
				}, {
					value: 'sticky',
					key: 'sticky'
				}, {
					value: 'revert',
					key: 'revert'
				}, {
					value: 'static',
					key: 'static'
				}, {
					value: 'initial',
					key: 'initial'
				}, {
					value: 'inherit',
					key: 'inherit'
				}, {
					value: 'unset',
					key: 'unset'
				}],
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			filter: {
				type: String,
				default: '',
				control: 'slider',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			zoom: {
				type: String,
				default: '',
				control: 'slider',
				unit: '%',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			transform: {
				type: String,
				default: '',
				control: 'slider',
				unit: '%',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			childrenTransform: {
				type: String,
				default: '',
				control: 'slider',
				unit: '%',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			}
		},
		components: {
			Box: Box
		},
		data: () => ({
			currentPath: '',
			currentPathId: '',
			show: false,
			childModel: {},
			hover: false,
			active: false,
			focus: false,
			wrapperStyle: {
				borderRadius: undefined,
				background: undefined
			},
			windowWidth: typeof global !== 'undefined' ? global?.windowWidth || 1280 : 1280
		}),
		mounted() {
			document.addEventListener('click', this.handleClickOutside);
			if (this.$vertical && this.route && (this.$route?.path === this.route || this.$route?.path.startsWith((this.route + '/')
					.replace('//', '/'))) && !this.show) {
				this.toggle(true);
			}
		},
		beforeUnmount() {
			document.removeEventListener('click', this.handleClickOutside);
		},
		computed: {
			bpoint() {
				if (this.breakpoint) return this.breakpoint;
				if (this.windowWidth < 640) return 'xs';
				if (this.windowWidth < 768) return 'sm';
				if (this.windowWidth < 1024) return 'md';
				if (this.windowWidth < 1280) return 'lg';
				if (this.windowWidth < 1536) return 'xl';
				return '2xl';
			},
			current() {
				let route = this.route?.replace(/^\/|\/$/g, '');
				let path = this.$route?.path?.replace(/^\/|\/$/g, '');
				if (typeof this.setPath === 'undefined') return false;
				if (!route && this.$slots.default) return false;
				if (route && path.startsWith((route + '/')
						.replace('//', '/'))) return true;
				return route && route === path || !route && this.$route?.path === '/' || route === '/' && !this.$route?.path;
			},
			group() {
				if (this.current) return 'current';
				if (this.active) return 'active';
				if (this.hover) return 'hover';
				if (this.focus) return 'focus';
				return 'default';
			},
			$vertical() {
				return !this.horizontal || this.small;
			},
			$verticalLeftIndent() {
				if (!this.$vertical || !this.level || !this.verticalLeftIndent && !this.model?.verticalLeftIndent) return undefined;
				return `calc(${this.verticalLeftIndent || this.model.verticalLeftIndent} * ${this.level})`;
			},
			$verticalRightIndent() {
				if (!this.$vertical || !this.level || !this.verticalRightIndent && !this.model?.verticalRightIndent) return undefined;
				return `calc(${this.verticalRightIndent || this.model.verticalRightIndent} * ${this.level})`;
			},
			$iconSize() {
				if (this.iconSize) return this.iconSize;
				if (this.childrenIconSizeProvider) return this.childrenIconSizeProvider;
				return '20px';
			},
			$icon() {
				if (!this.$style?.icon) return null;
				if (this.$style.icon?.startsWith('--')) {
					return `var(${this.$style.icon})`;
				} else {
					return `url(${this.$style.icon})`;
				}
			},
			$caret() {
				const caret = this.caret || this.childrenCaretProvider || '--vp-nav-caret-icon';
				if (caret?.startsWith('--')) {
					return `var(${caret})`;
				} else {
					return `url(${caret})`;
				}
			},
			$caretSize() {
				if (this.caretSize) return this.caretSize;
				if (this.childrenCaretSizeProvider) return this.childrenCaretSizeProvider;
				return '20px';
			},
			$borderRadiusDrop() {
				if (this.borderRadiusDrop) return this.borderRadiusDrop;
				if (this.borderRadiusDropProvider) return this.borderRadiusDropProvider;
				return '';
			},
			$backgroundDropArea() {
				if (this.backgroundDropArea) return this.backgroundDropArea;
				if (this.backgroundDropAreaProvider) return this.backgroundDropAreaProvider;
				return '';
			},
			horizontal() {
				return this.orientation === 'Row';
			},
			rootClass() {
				const rootClass = {
					'vp-navigator-item--small': this.small,
					'vp-navigator-item--horizontal': !this.$vertical,
					'vp-navigator-item--vertical': this.$vertical,
					'vp-navigator-item--show': this.show || this.forceOpen || this.forceOpenProvider,
					'vp-navigator-item--hide': !this.show && !this.forceOpen && !this.forceOpenProvider,
					'vp-navigator-item--direction-left': this.itemDirection ? this.itemDirection === 'left' : this.direction === 'left',
					'vp-navigator-item--direction-right': this.itemDirection ? this.itemDirection === 'right' : this.direction === 'right',
					'vp-navigator-item--center': this.centerDropdown ? this.centerDropdown === 'true' : this.center === 'true',
					'vp-navigator-item--drop-up': this.drop === 'up',
					'vp-navigator-item--reverse': this.iconReverse === '' ? this.reverseIcon : this.iconReverse === 'true'
				};
				const level = this.level || 0;
				rootClass[`vp-navigator-item--level-${level}`] = true;
				rootClass[`vp-navigator-item--${this.order}`] = true;
				return rootClass;
			},
			style() {
				this.childModel = {
					verticalLeftIndent: this.verticalLeftIndent || this.model?.verticalLeftIndent,
					verticalRightIndent: this.verticalRightIndent || this.model?.verticalRightIndent
				};
				const style = {};
				const props = ['$borderRadiusDrop', '$backgroundDropArea', 'filter', 'position', 'justifyContent', 'fontSize', 'fontWeight', 'color', 'backgroundColor', 'backgroundColorDrop', 'gap', 'backgroundImage', 'width', 'minWidth', 'maxWidth', 'height', 'minHeight', 'maxHeight', 'border', 'borderColor', 'borderTopColor', 'borderRightColor', 'borderBottomColor', 'borderLeftColor', 'borderWidth', 'borderTopWidth', 'borderRightWidth', 'borderBottomWidth', 'borderLeftWidth', 'borderStyle', 'borderTopStyle', 'borderRightStyle', 'borderBottomStyle', 'borderLeftStyle', 'borderRadius', 'borderTopLeftRadius', 'borderTopRightRadius', 'borderBottomRightRadius', 'borderBottomLeftRadius', 'padding', 'paddingTop', 'paddingRight', 'paddingBottom', 'paddingLeft', 'margin', 'marginTop', 'marginRight', 'marginBottom', 'marginLeft', 'zoom', 'transform'];
				const groups = ['default', 'hover', 'current', 'active', 'focus'];
				const breakpoints = ['xs', 'sm', 'md', 'lg', 'xl', '2xl'];
				const themes = [this.theme || 'light', ...['light', 'dark'].filter(t => t !== (this.theme || 'light'))];
				for (let prop of props) {
					let priority = {};
					if (this[prop]) {
						try {
							priority = JSON.parse(this[prop].replaceAll('`', '"'));
						} catch (e) {}
					}
					const fallback = this.model?.[prop] || {};
					const merge = {};
					for (const group of [...new Set([...Object.keys(priority), ...Object.keys(fallback)])]) {
						const pri = priority?.[group] || {};
						const fb = fallback?.[group] || {};
						merge[group] = {};
						for (const breakpoint of [...new Set([...Object.keys(pri), ...Object.keys(fb)])]) {
							const p = pri?.[breakpoint] || {};
							const f = fb?.[breakpoint] || {};
							merge[group][breakpoint] = {};
							for (const theme of [...new Set([...Object.keys(p), ...Object.keys(f)])]) {
								const value = p?.[theme]?.toString() || f?.[theme]?.toString() || null;
								merge[group][breakpoint][theme] = value;
							}
						}
					}
					const groups = Object.keys(merge);
					if (groups.length) {
						for (const theme of themes) {
							style[prop] = merge?.['default']?.['xs']?.[theme];
							if (typeof style[prop] !== 'undefined' && style[prop] !== null && style[prop] !== '') {
								break;
							}
						}
						let limit = this.breakpoint || '2xl';
						let match = false;
						for (const breakpoint of breakpoints) {
							for (const theme of themes) {
								let value = merge?.[this.group]?.[breakpoint]?.[theme]?.toString();
								if (typeof value !== 'undefined' && value !== null && value !== '') {
									let important = priority?.[this.group]?.[breakpoint]?.[theme]?.toString() === value;
									match = true;
									style[prop] = value + (important ? '!important' : '');
									style[prop] = style[prop].replace('!important!important', '!important');
									break;
								}
							}
							if (breakpoint === limit) break;
						}
						if (!match && this.group !== 'default') {
							limit = this.breakpoint || '2xl';
							for (const breakpoint of breakpoints) {
								for (const theme of themes) {
									let value = merge?.['default']?.[breakpoint]?.[theme]?.toString();
									if (typeof value !== 'undefined' && value !== null && value !== '') {
										let important = priority?.['default']?.[breakpoint]?.[theme]?.toString() === value;
										match = true;
										style[prop] = value + (important ? '!important' : '');
										style[prop] = style[prop].replace('!important!important', '!important');
										break;
									}
								}
								if (breakpoint === limit) break;
							}
						}
						this.childModel[prop] = merge;
					}
				}
				style['flex-direction'] = (this.iconReverse === '' ? this.reverseIcon : this.iconReverse === 'true') ? 'row-reverse' : 'row';
				if (style['backgroundColorDrop'] && !style['backgroundColor']?.includes('!important')) {
					let fill = false;
					if (this.level < 1 && (this.forceOpen || this.forceOpenProvider || this.small)) {
						const bg = style['backgroundColor'];
						const isTransparent = !bg || bg === 'transparent' || /^rgba?\(\s*\d+\s*,\s*\d+\s*,\s*\d+\s*,\s*0\s*\)$/i.test(bg) || /^hsla?\(\s*\d+\s*,\s*[\d.]+%\s*,\s*[\d.]+%\s*,\s*0\s*\)$/i.test(bg) || /^#(?:[0-9a-f]{2}){3}00$/i.test(bg) || /^#(?:[0-9a-f]{4}|[0-9a-f]{8})$/i.test(bg) && bg.slice(-2)
							.toLowerCase() === '00';
						fill = isTransparent;
					}
					if (this.level >= 1 || fill) {
						style['backgroundColor'] = style['backgroundColorDrop'];
					}
					delete style['backgroundColorDrop'];
				}
				if (style?.['filter']?.includes('!important')) {
					style['filter'] = style['filter'].replace('!important', '');
				}
				if (style?.$borderRadiusDrop) {
					this.wrapperStyle.borderRadius = style.$borderRadiusDrop;
				}
				if (style?.$backgroundDropArea) {
					this.wrapperStyle.background = style.$backgroundDropArea;
				} else {
					this.wrapperStyle.background = style.backgroundColor;
				}
				delete style.$borderRadiusDrop;
				delete style.$backgroundDropArea;
				return style;
			},
			$style() {
				const style = {};
				const props = ['icon'];
				const groups = ['default', 'hover'];
				const breakpoints = ['xs', 'sm', 'md', 'lg', 'xl', '2xl'];
				const themes = ['light', 'dark'];
				for (const prop of props) {
					let priority = {};
					if (this[prop]) {
						try {
							priority = JSON.parse(this[prop].replaceAll('`', '"'));
						} catch (e) {}
					}
					const merge = {};
					for (const group of Object.keys(priority)) {
						const pri = priority?.[group] || {};
						merge[group] = {};
						for (const breakpoint of Object.keys(pri)) {
							const p = pri?.[breakpoint] || {};
							merge[group][breakpoint] = {};
							for (const theme of Object.keys(p)) {
								const value = p?.[theme]?.toString() || null;
								merge[group][breakpoint][theme] = value;
							}
						}
					}
					const groups = Object.keys(merge);
					if (groups.length) {
						style[prop] = merge?.['default']?.['xs']?.['light'];
						let limitReached = false;
						let limit = this.bpoint || 'xs';
						let match = false;
						for (const breakpoint of breakpoints) {
							if (!limitReached) {
								const firstPriority = merge?.[this.group]?.[breakpoint]?.[this.theme]?.toString();
								const secondPriority = merge?.[this.group]?.[breakpoint]?.['light']?.toString();
								const value = firstPriority || secondPriority;
								if (value) {
									style[prop] = value;
									match = true;
								}
								limitReached = breakpoint === limit;
							}
						}
						if (!match && this.group !== 'default') {
							limitReached = false;
							limit = this.bpoint || 'xs';
							for (const breakpoint of breakpoints) {
								if (!limitReached) {
									const firstPriority = merge?.['default']?.[breakpoint]?.[this.theme]?.toString();
									const secondPriority = merge?.['default']?.[breakpoint]?.['light']?.toString();
									const value = firstPriority || secondPriority;
									if (value) {
										style[prop] = value;
									}
									limitReached = breakpoint === limit;
								}
							}
						}
					}
				}
				return style;
			}
		},
		watch: {
			pathId(id) {
				if (this.currentPathId !== id && this.show) {
					this.show = false;
				} else if (this.currentPathId === id) {
					this.show = true;
				}
			}
		},
		methods: {
			toggle(forceOpen = false) {
				if (forceOpen) {
					this.show = true;
				} else {
					this.show = !this.show;
				}
				if (this.show) {
					this.currentPath = this.route;
					this.currentPathId = [...Array(8)].map(() => Math.random()
							.toString(36)[2])
						.join('');
					if (typeof this.setPath === 'function') {
						this.setPath(this.currentPath, this.currentPathId);
					}
				}
			},
			handleClickOutside(event) {
				if (!this.$vertical && this.show && this.$refs.navitem && !this.$refs.navitem.$el.contains(event.target)) {
					this.show = false;
				}
			},
			go(event, route, external, target = '') {
				const shouldOpenInNewTab = !target && (event.metaKey || event.ctrlKey);
				if (!external) {
					if (!this.$router) return;
					if (shouldOpenInNewTab) {
						window.open(this.$router.resolve(route)
							.href, '_blank');
					} else {
						this.$router.push(route);
					}
				} else {
					window.open(route, target || (shouldOpenInNewTab ? '_blank' : '_self'));
				}
			}
		}
	};

</script>
<style scoped>
	* {
		--vp-nav-caret-icon: url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIGZpbGw9Im5vbmUiIHZpZXdCb3g9IjAgMCAyNCAyNCIgc3Ryb2tlLXdpZHRoPSIxLjUiIHN0cm9rZT0iY3VycmVudENvbG9yIiBjbGFzcz0ic2l6ZS02Ij4KICA8cGF0aCBzdHJva2UtbGluZWNhcD0icm91bmQiIHN0cm9rZS1saW5lam9pbj0icm91bmQiIGQ9Im04LjI1IDQuNSA3LjUgNy41LTcuNSA3LjUiIC8+Cjwvc3ZnPg==);
	}

	.vp-navigator-item,
	.vp-box.vp-navigator-item {
		display: flex;
		overflow: visible;
		padding: 0;
		cursor: pointer;
		align-items: stretch;
		transition: border-color .3s linear, opacity .3s linear, color .3s linear, background .3s linear, background-color .3s linear;
		flex-wrap: nowrap !important;
	}

	.vp-navigator-item--vertical.vp-navigator-item--show .vp-navigator-item--arrow {
		transform: rotate(90deg);
	}

	.vp-navigator-item--drop-up.vp-navigator-item--vertical.vp-navigator-item--show .vp-navigator-item--arrow {
		transform: rotate(-90deg);
	}

	.vp-navigator-item--vertical.vp-navigator-item--hide .vp-navigator-item--arrow,
	.vp-navigator-item--drop-up.vp-navigator-item--vertical.vp-navigator-item--hide .vp-navigator-item--arrow {
		transform: rotate(0deg);
	}

	.vp-navigator-item--reverse.vp-navigator-item--vertical.vp-navigator-item--hide .vp-navigator-item--arrow,
	.vp-navigator-item--drop-up.vp-navigator-item--reverse.vp-navigator-item--vertical.vp-navigator-item--hide .vp-navigator-item--arrow {
		transform: rotate(-180deg);
	}

	.vp-navigator-item--horizontal.vp-navigator-item--level-0 .vp-navigator-item--arrow,
	.vp-navigator-item--reverse.vp-navigator-item--horizontal.vp-navigator-item--level-0 .vp-navigator-item--arrow {
		transform: rotate(90deg);
	}

	.vp-navigator-item--drop-up.vp-navigator-item--horizontal.vp-navigator-item--level-0 .vp-navigator-item--arrow,
	.vp-navigator-item--drop-up.vp-navigator-item--reverse.vp-navigator-item--horizontal.vp-navigator-item--level-0 .vp-navigator-item--arrow {
		transform: rotate(-90deg);
	}

	.vp-navigator-item--horizontal.vp-navigator-item--level-1 .vp-navigator-item--arrow,
	.vp-navigator-item--drop-up.vp-navigator-item--horizontal.vp-navigator-item--level-1 .vp-navigator-item--arrow {
		transform: rotate(0deg);
	}

	.vp-navigator-item--reverse.vp-navigator-item--horizontal.vp-navigator-item--level-1 .vp-navigator-item--arrow,
	.vp-navigator-item--drop-up.vp-navigator-item--reverse.vp-navigator-item--horizontal.vp-navigator-item--level-1 .vp-navigator-item--arrow {
		transform: rotate(0deg);
	}

	.vp-navigator-item--direction-right.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0)>.vp-navigator-item--arrow,
	.vp-navigator-item--direction-right.vp-navigator-item--reverse.vp-navigator-item--horizontal.vp-navigator-item--level-1 .vp-navigator-item--arrow,
	.vp-navigator-item--direction-right.vp-navigator-item--drop-up.vp-navigator-item--reverse.vp-navigator-item--horizontal.vp-navigator-item--level-1 .vp-navigator-item--arrow {
		transform: rotate(0deg);
	}

	.vp-navigator-item--direction-left.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0)>.vp-navigator-item--arrow,
	.vp-navigator-item--direction-left.vp-navigator-item--reverse.vp-navigator-item--horizontal.vp-navigator-item--level-1 .vp-navigator-item--arrow,
	.vp-navigator-item--direction-left.vp-navigator-item--drop-up.vp-navigator-item--reverse.vp-navigator-item--horizontal.vp-navigator-item--level-1 .vp-navigator-item--arrow {
		transform: rotate(-180deg);
	}

	.vp-navigator-item--horizontal.vp-navigator-item--level-0 .wrapper {
		position: absolute;
		min-width: 100%;
		top: 100%;
		left: 0px;
	}

	.vp-navigator-item--direction-left.vp-navigator-item--horizontal.vp-navigator-item--level-0 .wrapper {
		left: auto;
		right: 0px;
	}

	.vp-navigator-item--center.vp-navigator-item--horizontal.vp-navigator-item--level-0 .wrapper,
	.vp-navigator-item--center.vp-navigator-item--direction-left.vp-navigator-item--horizontal.vp-navigator-item--level-0 .wrapper,
	.vp-navigator-item--center.vp-navigator-item--direction-right.vp-navigator-item--horizontal.vp-navigator-item--level-0 .wrapper {
		left: 50%;
		right: auto;
		transform: translateX(-50%);
	}

	.vp-navigator-item--drop-up.vp-navigator-item--horizontal.vp-navigator-item--level-0 .wrapper {
		top: auto;
		bottom: 100%;
	}

	.vp-navigator-item--up.wrapper {
		display: flex;
		flex-direction: column-reverse;
	}

	.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0) .wrapper {
		position: absolute;
		top: 0%;
		left: 100%;
	}

	.vp-navigator-item--drop-up.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0) .wrapper {
		position: absolute;
		bottom: 0%;
		left: 100%;
	}

	.vp-navigator-item--direction-right.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0)>.wrapper {
		left: 100%;
		right: auto;
	}

	.vp-navigator-item--direction-left.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0)>.wrapper {
		left: auto;
		right: 100%;
	}

	.vp-navigator-item--small,
	.vp-navigator-item--drop-up,
	.vp-navigator-item--odd,
	.vp-navigator-item--even,
	.vp-navigator-item--link {
		cursor: inherit;
	}

</style>