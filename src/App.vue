<template>
	<Box
		v-bind="$attrs"
		:class="rootClass"
		:expand="expand ? '{`default`:{`xs`:{`light`:true}}}' : ''"
		:style="style"
		ref="navitem"
		class="vp-navigator-item"
		@click.stop="($event.pointerType !== 'mouse' || $vertical) && $slots.default && !route ? show=!show : ''"
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
			:is="external ? 'a' : 'router-link'"
			:to="route || ''"
			:href="route || ''"
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
			:is="external ? 'a' : 'router-link'"
			:to="route || ''"
			:href="route || ''"
			:target="target"
			style="white-space: nowrap;display:inline-flex;align-items:center;flex-grow:1"
		>
			{{ title }}
		</component>
		<div
			v-if="$slots.default"
			@click.stop="show=!show"
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
		</div> <template v-if="!$vertical && $slots.default && (show || forceOpen || forceOpenProvider)">
			<div
				class="wrapper"
				:class="{
				'vp-navigator-item--up': drop === 'up'
			}"
				:style="{ 'z-index': level ? level + 1 : 1 }"
			>
				<slot />
			</div>
		</template>
	</Box> <template v-if="$vertical">
		<div
			v-if="$slots.default && (show || forceOpen || forceOpenProvider)"
			class="wrapper"
			:class="{
			'vp-navigator-item--up': drop === 'up'
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
		inject: ['theme', 'breakpoint', 'small', 'open', 'forceOpenProvider', 'direction', 'orientation', 'drop', 'level', 'order', 'reverseIcon', 'expand', 'childrenIconSizeProvider', 'childrenCaretProvider', 'childrenCaretSizeProvider', 'model'],
		provide() {
			return {
				open: computed(() => this.show),
				forceOpenProvider: computed(() => this.forceOpen || this.forceOpenProvider),
				level: computed(() => this.level ? this.level + 1 : 1),
				order: computed(() => this.order === 'odd' ? 'even' : 'odd'),
				reverseIcon: computed(() => this.iconReverse === '' ? this.reverseIcon : this.iconReverse === 'true'),
				childrenIconSizeProvider: computed(() => this.childrenIconSize || this.childrenIconSizeProvider),
				childrenCaretProvider: computed(() => this.childrenCaret || this.childrenCaretProvider),
				childrenCaretSizeProvider: computed(() => this.childrenCaretSize || this.childrenCaretSizeProvider),
				direction: computed(() => this.itemDirection ? this.itemDirection : this.direction),
				model: computed(() => this.childModel)
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
			caretSize: {
				type: String,
				default: '',
				control: 'slider'
			},
			childrenCaret: {
				type: String,
				control: 'media',
				default: ''
			},
			childrenCaretSize: {
				type: String,
				default: '',
				unit: 'px',
				control: 'slider'
			},
			iconReverse: {
				type: String,
				default: '',
				options: [{
					key: 'Default',
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
					key: 'Default',
					value: ''
				}, {
					key: 'Left',
					value: 'left'
				}, {
					key: 'Right',
					value: 'right'
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
			fontWeight: {
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
			backgroundColor: {
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
			gap: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
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
			}
		},
		components: {
			Box: Box
		},
		data: () => ({
			show: false,
			childModel: {},
			hover: false,
			active: false,
			focus: false
		}),
		mounted() {
			document.addEventListener('click', this.handleClickOutside);
		},
		beforeUnmount() {
			document.removeEventListener('click', this.handleClickOutside);
		},
		computed: {
			current() {
				let route = this.route?.replace(/^\/|\/$/g, '');
				let path = this.$route?.path?.replace(/^\/|\/$/g, '');
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
					'vp-navigator-item--drop-up': this.drop === 'up',
					'vp-navigator-item--reverse': this.iconReverse === '' ? this.reverseIcon : this.iconReverse === 'true'
				};
				const level = this.level || 0;
				rootClass[`vp-navigator-item--level-${level}`] = true;
				rootClass[`vp-navigator-item--${this.order}`] = true;
				return rootClass;
			},
			style() {
				this.childModel = {};
				const style = {};
				const props = ['fontWeight', 'color', 'backgroundColor', 'backgroundColorDrop', 'gap', 'backgroundImage', 'borderTopColor', 'borderRightColor', 'borderBottomColor', 'borderLeftColor', 'borderTopWidth', 'borderRightWidth', 'borderBottomWidth', 'borderLeftWidth', 'borderTopStyle', 'borderRightStyle', 'borderBottomStyle', 'borderLeftStyle', 'borderTopLeftRadius', 'borderTopRightRadius', 'borderBottomRightRadius', 'borderBottomLeftRadius', 'paddingTop', 'paddingRight', 'paddingBottom', 'paddingLeft', 'marginTop', 'marginRight', 'marginBottom', 'marginLeft'];
				const groups = ['default', 'hover', 'current', 'active', 'focus'];
				const breakpoints = ['xs', 'sm', 'md', 'lg', 'xl', '2xl'];
				const themes = ['light', 'dark'];
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
						this.childModel[prop] = merge;
						style[prop] = this.childModel[prop]?.['default']?.['xs']?.['light'];
						let limitReached = false;
						let limit = this.breakpoint || 'xs';
						let match = false;
						for (const breakpoint of breakpoints) {
							if (!limitReached) {
								const firstPriority = this.childModel[prop]?.[this.group]?.[breakpoint]?.[this.theme || 'light']?.toString();
								const secondPriority = this.childModel[prop]?.[this.group]?.[breakpoint]?.['light']?.toString();
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
							limit = this.breakpoint || 'xs';
							for (const breakpoint of breakpoints) {
								if (!limitReached) {
									const firstPriority = this.childModel[prop]?.['default']?.[breakpoint]?.[this.theme || 'light']?.toString();
									const secondPriority = this.childModel[prop]?.['default']?.[breakpoint]?.['light']?.toString();
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
				style['flex-direction'] = (this.iconReverse === '' ? this.reverseIcon : this.iconReverse === 'true') ? 'row-reverse' : 'row';
				if (style['backgroundColorDrop']) {
					if (this.level >= 1) {
						style['backgroundColor'] = style['backgroundColorDrop'];
					}
					delete style['backgroundColorDrop'];
				}
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
		methods: {
			handleClickOutside(event) {
				if (this.show && this.$refs.navitem && !this.$refs.navitem.$el.contains(event.target)) {
					this.show = false;
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
		position: relative;
		overflow: visible;
		padding: 0;
		cursor: pointer;
		align-items: stretch;
		transition: border-color .3s linear, opacity .3s linear, color .3s linear, background .3s linear, background-color .3s linear;
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
	}

	.vp-navigator-item--drop-up.vp-navigator-item--horizontal.vp-navigator-item--level-0 .wrapper {
		position: absolute;
		min-width: 100%;
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