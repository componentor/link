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
			:style="{
				...iconWrapperStyle
			}"
		>
			<div
				:style="{
					'background-image': $icon,
					'width': $iconSize,
					...iconStyle
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
				marginRight: $verticalRightIndent,
				...linkStyle
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
			:style="{
				...caretWrapperStyle
			}"
		>
			<div
				:style="{
					'background-image': $caret,
					'width': $caretSize,
					...caretStyle
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
			iconWrapperCstyleProvider: { default: '' },
			iconCstyleProvider: { default: '' },
			linkCstyleProvider: { default: '' },
			caretWrapperCstyleProvider: { default: '' },
			caretCstyleProvider: { default: '' },
			childrenCstyleIconWrapperProvider: { default: '' },
			childrenCstyleIconProvider: { default: '' },
			childrenCstyleLinkProvider: { default: '' },
			childrenCstyleCaretWrapperProvider: { default: '' },
			childrenCstyleCaretProvider: { default: '' },
			childrenCstyleModalProvider: { default: '' },
			model: { default: () => ({}) }
		},
		provide() {
			const self = this;
			return {
				setPath(path, id) {
					if (self.currentPathId !== id) {
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
				childrenCstyleProvider: computed(() => this.childrenCstyleItem || this.childrenCstyle || this.childrenCstyleProvider),
				wrapperCstyleProvider: computed(() => this.wrapperCstyle || this.wrapperCstyleProvider),
				iconWrapperCstyleProvider: computed(() => this.childrenCstyleIconWrapper || this.childrenCstyleIconWrapperProvider || this.iconWrapperCstyle || this.iconWrapperCstyleProvider),
				iconCstyleProvider: computed(() => this.childrenCstyleIcon || this.childrenCstyleIconProvider || this.iconCstyle || this.iconCstyleProvider),
				linkCstyleProvider: computed(() => this.childrenCstyleLink || this.childrenCstyleLinkProvider || this.linkCstyle || this.linkCstyleProvider),
				caretWrapperCstyleProvider: computed(() => this.childrenCstyleCaretWrapper || this.childrenCstyleCaretWrapperProvider || this.caretWrapperCstyle || this.caretWrapperCstyleProvider),
				caretCstyleProvider: computed(() => this.childrenCstyleCaret || this.childrenCstyleCaretProvider || this.caretCstyle || this.caretCstyleProvider),
				childrenCstyleIconWrapperProvider: computed(() => this.childrenCstyleIconWrapper || this.childrenCstyleIconWrapperProvider),
				childrenCstyleIconProvider: computed(() => this.childrenCstyleIcon || this.childrenCstyleIconProvider),
				childrenCstyleLinkProvider: computed(() => this.childrenCstyleLink || this.childrenCstyleLinkProvider),
				childrenCstyleCaretWrapperProvider: computed(() => this.childrenCstyleCaretWrapper || this.childrenCstyleCaretWrapperProvider),
				childrenCstyleCaretProvider: computed(() => this.childrenCstyleCaret || this.childrenCstyleCaretProvider),
				childrenCstyleModalProvider: computed(() => this.childrenCstyleModalProvider),
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
			childrenCstyleItem: {
				type: [String, Object, Array],
				default: ''
			},
			wrapperCstyle: {
				type: [String, Object, Array],
				default: ''
			},
			iconWrapperCstyle: {
				type: [String, Object, Array],
				default: ''
			},
			iconCstyle: {
				type: [String, Object, Array],
				default: ''
			},
			linkCstyle: {
				type: [String, Object, Array],
				default: ''
			},
			caretWrapperCstyle: {
				type: [String, Object, Array],
				default: ''
			},
			caretCstyle: {
				type: [String, Object, Array],
				default: ''
			},
			childrenCstyleIconWrapper: {
				type: [String, Object, Array],
				default: ''
			},
			childrenCstyleIcon: {
				type: [String, Object, Array],
				default: ''
			},
			childrenCstyleLink: {
				type: [String, Object, Array],
				default: ''
			},
			childrenCstyleCaretWrapper: {
				type: [String, Object, Array],
				default: ''
			},
			childrenCstyleCaret: {
				type: [String, Object, Array],
				default: ''
			},
			breakpointStrategy: {
				type: String,
				default: 'mobile-first',
				validator: v => ['exact', 'mobile-first', 'desktop-first'].includes(v)
			},
			themeStrategy: {
				type: String,
				default: 'fallback',
				validator: v => ['strict', 'fallback'].includes(v)
			}
		},
		data: () => ({
			currentPathId: '',
			show: false,
			hover: false,
			active: false,
			focus: false
		}),
		mounted() {
			document.addEventListener('click', this.handleClickOutside);
			const currentPath = this.currentPath;
			if (this.$vertical && this.route && currentPath && (currentPath === this.route || currentPath.startsWith((this.route + '/')
					.replace('//', '/'))) && !this.show) {
				this.toggle(true);
			}
		},
		beforeUnmount() {
			document.removeEventListener('click', this.handleClickOutside);
		},
		computed: {
			caretFallbackIcon() {
				if (this.theme === 'dark') return 'data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIGZpbGw9Im5vbmUiIHZpZXdCb3g9IjAgMCAyNCAyNCIgc3Ryb2tlLXdpZHRoPSIxLjUiIHN0cm9rZT0iI2ZmZiIgY2xhc3M9InNpemUtNiI+PHBhdGggc3Ryb2tlLWxpbmVjYXA9InJvdW5kIiBzdHJva2UtbGluZWpvaW49InJvdW5kIiBkPSJtOC4yNSA0LjUgNy41IDcuNS03LjUgNy41Ii8+PC9zdmc+'
				return 'data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIGZpbGw9Im5vbmUiIHZpZXdCb3g9IjAgMCAyNCAyNCIgc3Ryb2tlLXdpZHRoPSIxLjUiIHN0cm9rZT0iY3VycmVudENvbG9yIiBjbGFzcz0ic2l6ZS02Ij4KICA8cGF0aCBzdHJva2UtbGluZWNhcD0icm91bmQiIHN0cm9rZS1saW5lam9pbj0icm91bmQiIGQ9Im04LjI1IDQuNSA3LjUgNy41LTcuNSA3LjUiIC8+Cjwvc3ZnPg=='
			},
			linkTag() {
				return this.tag || (this.external ? 'a' : 'router-link');
			},
			cstyleString() {
				// Handle both raw values and Vue refs (childrenCstyleProvider is a computed ref from Navigator)
				// Use modal provider when in small/modal mode
				const isSmall = this.small && typeof this.small === 'object' && 'value' in this.small ? this.small.value : this.small;
				let providerValue = isSmall && this.childrenCstyleModalProvider ? this.childrenCstyleModalProvider : this.childrenCstyleProvider;
				// Unwrap if it's a ref
				if (providerValue && typeof providerValue === 'object' && 'value' in providerValue) {
					providerValue = providerValue.value;
				}
				return this.normalizeCstyle(this.cstyle || providerValue);
			},
			wrapperCstyleString() {
				return this.normalizeCstyle(this.wrapperCstyle || this.wrapperCstyleProvider);
			},
			iconWrapperCstyleString() {
				return this.normalizeCstyle(this.iconWrapperCstyle || this.iconWrapperCstyleProvider);
			},
			iconCstyleString() {
				return this.normalizeCstyle(this.iconCstyle || this.iconCstyleProvider);
			},
			linkCstyleString() {
				return this.normalizeCstyle(this.linkCstyle || this.linkCstyleProvider);
			},
			caretWrapperCstyleString() {
				return this.normalizeCstyle(this.caretWrapperCstyle || this.caretWrapperCstyleProvider);
			},
			caretCstyleString() {
				return this.normalizeCstyle(this.caretCstyle || this.caretCstyleProvider);
			},
			stateArray() {
				const states = [];
				if (this.current) states.push('current');
				if (this.hover) states.push('hover');
				if (this.active) states.push('active');
				if (this.focus) states.push('focus');
				return states;
			},
			// Helper to unwrap Vue refs from injected computed values
			$theme() {
				const val = this.theme;
				if (val && typeof val === 'object' && 'value' in val) return val.value;
				return val || '';
			},
			$breakpoint() {
				const val = this.breakpoint;
				if (val && typeof val === 'object' && 'value' in val) return val.value;
				return val || '';
			},
			computedStyle() {
				const baseStyle = {};
				baseStyle['flex-direction'] = (this.iconReverse === '' ? this.reverseIcon : this.iconReverse === 'true') ? 'row-reverse' : 'row';
				const cstyleObject = this.computeCstyleToStyleObject(this.cstyleString);
				return { ...baseStyle, ...cstyleObject };
			},
			wrapperStyle() {
				return this.computeCstyleToStyleObject(this.wrapperCstyleString);
			},
			iconWrapperStyle() {
				return this.computeCstyleToStyleObject(this.iconWrapperCstyleString);
			},
			iconStyle() {
				return this.computeCstyleToStyleObject(this.iconCstyleString);
			},
			linkStyle() {
				return this.computeCstyleToStyleObject(this.linkCstyleString);
			},
			caretWrapperStyle() {
				return this.computeCstyleToStyleObject(this.caretWrapperCstyleString);
			},
			caretStyle() {
				return this.computeCstyleToStyleObject(this.caretCstyleString);
			},
			currentPath() {
				// Get current path from router or window.location
				// Access $route.path directly to ensure reactivity tracking
				if (this.$route?.path) return this.$route.path;
				if (this.$router?.currentRoute?.value?.path) return this.$router.currentRoute.value.path;
				if (typeof window !== 'undefined') return window.location.pathname;
				return '';
			},
			current() {
				const currentPath = this.currentPath;
				if (!currentPath) return false;
				let route = this.route?.replace(/^\/|\/$/g, '');
				let path = currentPath.replace(/^\/|\/$/g, '');
				// If this is a parent link with children and no route, don't mark as current
				if (!route && this.$slots.default) return false;
				// Exact match
				if (route === path) return true;
				// Home route special case
				if (!route && path === '') return true;
				if (route === '/' && path === '') return true;
				// Match parent routes for dynamic/nested routes
				// e.g. /dashboard matches /dashboard/settings
				if (route && path.startsWith(route + '/')) return true;
				return false;
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
					'vp-navigator-item--reverse': this.iconReverse === '' ? this.reverseIcon : this.iconReverse === 'true',
					'vp-navigator-item--current': this.current
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
			normalizeCstyle(cstyle) {
				if (!cstyle) return '';
				if (typeof cstyle === 'string') return cstyle;
				if (Array.isArray(cstyle)) {
					return cstyle.map(item => {
							if (typeof item === 'string') return item;
							return Object.entries(item)
								.map(([key, value]) => `${key}:${value}`)
								.join('; ');
						})
						.join('; ');
				}
				return Object.entries(cstyle)
					.map(([key, value]) => `${key}:${value}`)
					.join('; ');
			},
			computeCstyleToStyleObject(cstyleString) {
				if (!cstyleString) return {};
				const parsed = parse(cstyleString);
				const cstyleResult = getStyle(parsed, {
					theme: this.$theme,
					breakpoint: this.$breakpoint,
					states: this.stateArray,
					breakpointStrategy: this.breakpointStrategy,
					themeStrategy: this.themeStrategy
				});
				return this.cssStringToObject(cstyleResult);
			},
			toggle(forceOpen = false) {
				if (forceOpen) {
					this.show = true;
				} else {
					this.show = !this.show;
				}
				if (this.show) {
					this.currentPathId = [...Array(8)].map(() => Math.random()
							.toString(36)[2])
						.join('');
					if (typeof this.setPath === 'function') {
						this.setPath(this.route, this.currentPathId);
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
			},
			// Extract styles prefixed with 'current:' since @componentor/breakpoint
			// doesn't recognize 'current' as a valid state (only CSS pseudo-classes)
			extractCurrentStyles(cstyleString) {
				if (!cstyleString) return {};
				const result = {};
				const declarations = cstyleString.split(';').map(s => s.trim()).filter(s => s.length > 0);
				const isDark = this.theme === 'dark' || (typeof window !== 'undefined' && window.matchMedia?.('(prefers-color-scheme: dark)').matches);

				for (const declaration of declarations) {
					const parts = declaration.split(':').map(p => p.trim());
					if (parts.length < 3) continue;

					// Check if first part is 'current'
					if (parts[0] !== 'current') continue;

					// Handle current:dark:property:value or current:property:value
					if (parts[1] === 'dark' && parts.length >= 4) {
						// current:dark:property:value - only apply in dark mode
						if (isDark) {
							const property = parts[2];
							const value = parts.slice(3).join(':');
							result[property] = value;
						}
					} else if (parts[1] === 'light' && parts.length >= 4) {
						// current:light:property:value - only apply in light mode
						if (!isDark) {
							const property = parts[2];
							const value = parts.slice(3).join(':');
							result[property] = value;
						}
					} else {
						// current:property:value - apply always when current
						const property = parts[1];
						const value = parts.slice(2).join(':');
						// Don't override if we already have a theme-specific value
						if (!result[property]) {
							result[property] = value;
						}
					}
				}
				return result;
			}
		}
	};

</script>
<style scoped>
	* {
		--vp-nav-caret-icon: url(v-bind(caretFallbackIcon));
	}

	.vp-navigator-item,
	.vp-box.vp-navigator-item {
		display: flex;
		overflow: visible;
		padding: 0;
		cursor: pointer;
		align-items: center;
		transition: border-color .3s linear, opacity .3s linear, color .3s linear, background .3s linear, background-color .3s linear;
		flex-wrap: nowrap !important;
		gap: 6px;
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
	.vp-navigator-item--even {
		cursor: inherit;
	}

	.vp-navigator-item--link {
		cursor: pointer;
	}

	.vp-navigator-item,
	.vp-navigator-item *,
	.vp-navigator-item a,
	.vp-navigator-item a:visited,
	.vp-navigator-item a:hover {
		cursor: pointer !important;
		color: inherit;
		text-decoration: none;
	}

	.wrapper {
		display: flex;
		flex-direction: column;
	}
</style>
