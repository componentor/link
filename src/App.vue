<template>
	<component
		:is="tag || 'div'"
		v-bind="$attrs"
		:class="rootClass"
		:style="computedStyle"
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
			:is="linkTag"
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
			:is="linkTag"
			:to="external ? undefined : (route || '')"
			:href="!external ? undefined : (route || '')"
			:target="target"
			:style="{
				marginLeft: $verticalLeftIndent,
				marginRight: $verticalRightIndent
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
	</component>
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
	import {
		parse,
		getStyle
	} from '@componentor/breakpoint';
	export default {
		inject: {
			path: { default: undefined },
			pathId: { default: undefined },
			setPath: { default: undefined },
			theme: { default: '' },
			breakpoint: { default: '' },
			small: { default: false },
			open: { default: undefined },
			forceOpenProvider: { default: false },
			direction: { default: '' },
			center: { default: '' },
			orientation: { default: '' },
			drop: { default: '' },
			level: { default: 0 },
			order: { default: 'odd' },
			reverseIcon: { default: false },
			childrenIconSizeProvider: { default: '' },
			childrenCaretProvider: { default: '' },
			childrenCaretSizeProvider: { default: '' },
			childrenCstyleProvider: { default: '' },
			wrapperCstyleProvider: { default: '' },
			model: { default: () => ({}) }
		},
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
				childrenCstyleProvider: computed(() => this.childrenCstyle || this.childrenCstyleProvider),
				wrapperCstyleProvider: computed(() => this.wrapperCstyle || this.wrapperCstyleProvider),
				direction: computed(() => this.childrenItemDirection ? this.childrenItemDirection : this.itemDirection ? this.itemDirection : this.direction),
				center: computed(() => false),
				orientation: computed(() => this.orientation),
				small: computed(() => this.small),
				theme: computed(() => this.theme),
				breakpoint: computed(() => this.breakpoint)
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
			forceOpen: {
				type: Boolean,
				default: false
			},
			cstyle: {
				type: [String, Object, Array],
				default: ''
			},
			childrenCstyle: {
				type: [String, Object, Array],
				default: ''
			},
			wrapperCstyle: {
				type: [String, Object, Array],
				default: ''
			}
		},
		data: () => ({
			currentPath: '',
			currentPathId: '',
			show: false,
			hover: false,
			active: false,
			focus: false
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
			linkTag() {
				return this.tag || (this.external ? 'a' : 'router-link');
			},
			cstyleString() {
				const style = this.cstyle || this.childrenCstyleProvider;
				if (!style) return '';
				if (typeof style === 'string') return style;
				if (Array.isArray(style)) {
					return style.map(item => {
							if (typeof item === 'string') return item;
							return Object.entries(item)
								.map(([key, value]) => `${key}:${value}`)
								.join('; ');
						})
						.join('; ');
				}
				return Object.entries(style)
					.map(([key, value]) => `${key}:${value}`)
					.join('; ');
			},
			wrapperCstyleString() {
				const style = this.wrapperCstyle || this.wrapperCstyleProvider;
				if (!style) return '';
				if (typeof style === 'string') return style;
				if (Array.isArray(style)) {
					return style.map(item => {
							if (typeof item === 'string') return item;
							return Object.entries(item)
								.map(([key, value]) => `${key}:${value}`)
								.join('; ');
						})
						.join('; ');
				}
				return Object.entries(style)
					.map(([key, value]) => `${key}:${value}`)
					.join('; ');
			},
			stateArray() {
				const states = [];
				if (this.current) states.push('current');
				if (this.hover) states.push('hover');
				if (this.active) states.push('active');
				if (this.focus) states.push('focus');
				return states;
			},
			computedStyle() {
				const baseStyle = {};
				baseStyle['flex-direction'] = (this.iconReverse === '' ? this.reverseIcon : this.iconReverse === 'true') ? 'row-reverse' : 'row';
				if (!this.cstyleString) return baseStyle;
				const parsed = parse(this.cstyleString);
				const cstyleResult = getStyle(parsed, {
					theme: this.theme,
					breakpoint: this.breakpoint,
					states: this.stateArray
				});
				// Convert CSS string to object for Vue style binding
				const cstyleObject = this.cssStringToObject(cstyleResult);
				return { ...baseStyle, ...cstyleObject };
			},
			wrapperStyle() {
				if (!this.wrapperCstyleString) return {};
				const parsed = parse(this.wrapperCstyleString);
				const cstyleResult = getStyle(parsed, {
					theme: this.theme,
					breakpoint: this.breakpoint,
					states: this.stateArray
				});
				// Convert CSS string to object for Vue style binding
				return this.cssStringToObject(cstyleResult);
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
			$vertical() {
				return !this.horizontal || this.small;
			},
			$verticalLeftIndent() {
				const indent = this.verticalLeftIndent || this.model?.verticalLeftIndent;
				if (!this.$vertical || !this.level || !indent) return undefined;
				return `calc(${indent} * ${this.level})`;
			},
			$verticalRightIndent() {
				const indent = this.verticalRightIndent || this.model?.verticalRightIndent;
				if (!this.$vertical || !this.level || !indent) return undefined;
				return `calc(${indent} * ${this.level})`;
			},
			$iconSize() {
				if (this.iconSize) return this.iconSize;
				if (this.childrenIconSizeProvider) return this.childrenIconSizeProvider;
				return '20px';
			},
			$icon() {
				if (!this.icon) return null;
				if (this.icon?.startsWith('--')) {
					return `var(${this.icon})`;
				} else {
					return `url(${this.icon})`;
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
					'vp-navigator-item--center': this.centerDropdown ? this.centerDropdown === 'true' : this.center === 'true',
					'vp-navigator-item--drop-up': this.drop === 'up',
					'vp-navigator-item--reverse': this.iconReverse === '' ? this.reverseIcon : this.iconReverse === 'true'
				};
				const level = this.level || 0;
				rootClass[`vp-navigator-item--level-${level}`] = true;
				rootClass[`vp-navigator-item--${this.order}`] = true;
				return rootClass;
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
				if (!this.$vertical && this.show && this.$refs.navitem && !this.$refs.navitem.contains(event.target)) {
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
			},
			cssStringToObject(cssString) {
				if (!cssString || typeof cssString !== 'string') return {};
				const result = {};
				// Split by semicolon and process each declaration
				const declarations = cssString.split(';').map(s => s.trim()).filter(s => s.length > 0);
				for (const declaration of declarations) {
					// Find the first colon to split property and value
					const colonIndex = declaration.indexOf(':');
					if (colonIndex === -1) continue;
					const property = declaration.substring(0, colonIndex).trim();
					const value = declaration.substring(colonIndex + 1).trim();
					if (property && value) {
						result[property] = value;
					}
				}
				return result;
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

	.vp-navigator-item--center.vp-navigator-item--horizontal.vp-navigator-item--level-0>.wrapper,
	.vp-navigator-item--center.vp-navigator-item--direction-left.vp-navigator-item--horizontal.vp-navigator-item--level-0>.wrapper,
	.vp-navigator-item--center.vp-navigator-item--direction-right.vp-navigator-item--horizontal.vp-navigator-item--level-0>.wrapper {
		left: 50%;
		right: auto;
		transform: translateX(-50%);
	}

	.vp-navigator-item--center.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0)>.vp-navigator-item--arrow {
		transform: rotate(90deg);
	}

	.vp-navigator-item--center.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0)>.wrapper {
		left: 50% !important;
		right: auto !important;
		transform: translateX(-50%) !important;
		top: 100%;
	}

	.vp-navigator-item--drop-up.vp-navigator-item--center.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0)>.vp-navigator-item--arrow {
		transform: rotate(-90deg);
	}

	.vp-navigator-item--drop-up .vp-navigator-item--center.vp-navigator-item--horizontal:not(.vp-navigator-item--level-0)>.wrapper {
		left: 50% !important;
		right: auto !important;
		transform: translateX(-50%) !important;
		bottom: 100%;
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