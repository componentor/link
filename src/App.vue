<template>
	<component
		:is="currentTag"
		ref="box"
		class="vp-box"
		:class="{
			'vp-box--column': columnAtBreakpoint
		}"
		:style="style"
		:data-theme="theme"
		@pointerover="hover=true"
		@pointerleave="hover=false"
	>
		<slot>
			Drop anything here!
		</slot>
	</component>
</template>
<script>
	export default {
		wysiwyg: true,
		inject: ['theme', 'breakpoint', 'maxwidth'],
		props: {
			tag: {
				type: String,
				default: 'div',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			column: {
				type: String,
				default: '',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				control: 'boolean'
			},
			contain: {
				type: String,
				default: '',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				control: 'boolean'
			},
			expand: {
				type: String,
				default: '',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				control: 'boolean'
			},
			reverse: {
				type: String,
				default: '',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				control: 'boolean'
			},
			overflow: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'auto',
					key: 'auto'
				}, {
					value: 'scroll',
					key: 'scroll'
				}, {
					value: 'hidden',
					key: 'hidden'
				}, {
					value: 'visible',
					key: 'visible'
				}],
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
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
			display: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'none',
					key: 'none'
				}, {
					value: 'block',
					key: 'block'
				}, {
					value: 'inline-block',
					key: 'inline-block'
				}, {
					value: 'flex',
					key: 'flex'
				}, {
					value: 'inline-flex',
					key: 'inline-flex'
				}, {
					value: 'flexbox',
					key: 'flexbox'
				}, {
					value: 'table',
					key: 'table'
				}, {
					value: 'inline-flexbox',
					key: 'inline-flexbox'
				}, {
					value: 'unset',
					key: 'unset'
				}],
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			width: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			minWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			maxWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			height: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			minHeight: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			maxHeight: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			gap: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			color: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			backgroundColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			backgroundImage: {
				type: String,
				default: '',
				control: 'media',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			padding: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			paddingTop: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			paddingRight: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			paddingBottom: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			paddingLeft: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			margin: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			marginTop: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			marginRight: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			marginBottom: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			marginLeft: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			fontSize: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			border: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
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
				groups: ['default', 'hover']
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
				groups: ['default', 'hover']
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
				groups: ['default', 'hover']
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
				groups: ['default', 'hover']
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
				groups: ['default', 'hover']
			},
			borderWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderTopWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderRightWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderBottomWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderLeftWidth: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderTopLeftRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderTopRightRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderBottomLeftRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderBottomRightRadius: {
				type: String,
				default: '',
				control: 'slider',
				unit: 'px',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderTopColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderRightColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderBottomColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			borderLeftColor: {
				type: String,
				default: '',
				control: 'color',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover']
			},
			flex: {
				type: String,
				default: '',
				control: 'slider',
				unit: '',
				breakpoints: ['xs', 'sm', 'md', 'lg', 'xl', '2xl'],
				themes: ['light', 'dark'],
				groups: ['default', 'hover'],
				sizer: true
			},
			flexWrap: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'nowrap',
					key: 'nowrap'
				}, {
					value: 'wrap',
					key: 'wrap'
				}, {
					value: 'wrap-reverse',
					key: 'wrap-reverse'
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
			alignItems: {
				type: String,
				default: '',
				options: [{
					value: '',
					key: 'Clear'
				}, {
					value: 'anchor-center',
					key: 'anchor-center'
				}, {
					value: 'normal',
					key: 'normal'
				}, {
					value: 'stretch',
					key: 'stretch'
				}, {
					value: 'center',
					key: 'center'
				}, {
					value: 'start',
					key: 'start'
				}, {
					value: 'end',
					key: 'end'
				}, {
					value: 'self-start',
					key: 'self-start'
				}, {
					value: 'self-end',
					key: 'self-end'
				}, {
					value: 'flex-start',
					key: 'flex-start'
				}, {
					value: 'flex-end',
					key: 'flex-end'
				}, {
					value: 'baseline',
					key: 'baseline'
				}, {
					value: 'first baseline',
					key: 'first baseline'
				}, {
					value: 'last baseline',
					key: 'last baseline'
				}, {
					value: 'safe center',
					key: 'safe center'
				}, {
					value: 'unsafe center',
					key: 'unsafe center'
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
			}
		},
		data: () => ({
			hover: false,
			windowWidth: typeof global !== 'undefined' ? global?.windowWidth || 1280 : 1280,
			colorSchemeMediaQuery: null,
			darkmode: false,
			hasWidth: null,
			currentTag: 'div',
			columnAtBreakpoint: false,
			containAtBreakpoint: false,
			expandAtBreakpoint: false,
			reverseAtBreakpoint: false
		}),
		mounted() {
			this.windowWidth = window.innerWidth;
			document.cookie = `windowWidth=${window.innerWidth}; path=/; Secure; SameSite=None`;
			this.colorSchemeMediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
			this.darkmode = this.colorSchemeMediaQuery.matches;
			this.colorSchemeMediaQuery.addEventListener('change', this.handleColorSchemeChange);
			window.addEventListener('resize', this.handleResize);
			if (!this.$refs?.box?.style?.width) {
				this.hasWidth = false;
			} else {
				this.hasWidth = true;
			}
		},
		beforeUnmount() {
			this.colorSchemeMediaQuery.removeEventListener('change', this.handleColorSchemeChange);
			window.removeEventListener('resize', this.handleResize);
		},
		methods: {
			handleColorSchemeChange(event) {
				this.darkmode = event.matches;
			},
			handleResize() {
				this.windowWidth = window.innerWidth;
				document.cookie = `windowWidth=${window.innerWidth}; path=/; Secure; SameSite=None`;
			}
		},
		computed: {
			themeComputed() {
				if (this.theme) return this.theme;
				return this.darkmode ? 'dark' : 'light';
			},
			bpoint() {
				if (this.breakpoint) return this.breakpoint;
				if (this.windowWidth > 1280) return '2xl';
				if (this.windowWidth > 1024) return 'xl';
				if (this.windowWidth > 768) return 'lg';
				if (this.windowWidth > 640) return 'md';
				if (this.windowWidth > 480) return 'sm';
				return 'xs';
			},
			group() {
				if (this.hover) return 'hover';
				return 'default';
			},
			style() {
				const style = {};
				const props = ['display', 'overflow', 'position', 'flex', 'flexWrap', 'justifyContent', 'alignItems', 'filter', 'zoom', 'transform', 'gap', 'color', 'column', 'tag', 'contain', 'expand', 'reverse', 'width', 'maxWidth', 'minWidth', 'height', 'minHeight', 'maxHeight', 'backgroundColor', 'backgroundImage', 'border', 'borderStyle', 'borderTopStyle', 'borderRightStyle', 'borderBottomStyle', 'borderLeftStyle', 'borderWidth', 'borderTopWidth', 'borderRightWidth', 'borderBottomWidth', 'borderLeftWidth', 'borderRadius', 'borderTopLeftRadius', 'borderTopRightRadius', 'borderBottomLeftRadius', 'borderBottomRightRadius', 'borderColor', 'borderTopColor', 'borderRightColor', 'borderBottomColor', 'borderLeftColor', 'padding', 'paddingTop', 'paddingRight', 'paddingBottom', 'paddingLeft', 'margin', 'marginTop', 'marginRight', 'marginBottom', 'marginLeft', 'fontSize'];
				const groups = ['default', 'hover'];
				const breakpoints = ['xs', 'sm', 'md', 'lg', 'xl', '2xl'];
				const themes = [this.themeComputed, ...['light', 'dark'].filter(t => t !== this.themeComputed)];
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
								merge[group][breakpoint][theme] = p?.[theme] === '' ? null : p?.[theme];
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
						let limit = this?.bpoint || 'xs';
						let match = false;
						for (const breakpoint of breakpoints) {
							for (const theme of themes) {
								let value = merge?.[this.group]?.[breakpoint]?.[theme];
								if (typeof value !== 'undefined' && value !== null && value !== '') {
									match = true;
									style[prop] = value;
									break;
								}
							}
							if (breakpoint === limit) break;
						}
						if (!match && this.group !== 'default') {
							limit = this?.bpoint || 'xs';
							for (const breakpoint of breakpoints) {
								for (const theme of themes) {
									let value = merge?.['default']?.[breakpoint]?.[theme];
									if (typeof value !== 'undefined' && value !== null && value !== '') {
										style[prop] = value;
										break;
									}
								}
								if (breakpoint === limit) break;
							}
						}
					}
				}
				this.containAtBreakpoint = style['contain'] && style['contain'] !== 'false' ? true : false;
				this.expandAtBreakpoint = style['expand'] && style['expand'] !== 'false' ? true : false;
				this.reverseAtBreakpoint = style['reverse'] && style['reverse'] !== 'false' ? true : false;
				this.columnAtBreakpoint = style['column'] && style['column'] !== 'false' ? true : false;
				this.currentTag = style['tag'] || 'div';
				delete style['tag'];
				delete style['contain'];
				delete style['expand'];
				delete style['reverse'];
				delete style['column'];
				if (style['width'] && !style['width'].includes('!important')) {
					style['width'] = style['width'] + '!important';
				}
				if (style['height'] && !style['height'].includes('!important')) {
					style['height'] = style['height'] + '!important';
				}
				if (this.columnAtBreakpoint) {
					style['flex-direction'] = this.reverseAtBreakpoint ? 'column-reverse' : 'column';
					if (this.hasWidth !== null && !this.hasWidth || typeof window === 'undefined') {
						style['flex-shrink'] = 1;
						style['flex-grow'] = 1;
					}
				} else {
					style['flex-direction'] = this.reverseAtBreakpoint ? 'row-reverse' : 'row';
					style['flex-grow'] = this.expandAtBreakpoint ? 1 : 0;
					if (!style['maxWidth']) style['max-width'] = '100%';
					if (!style['flex-wrap'] && !style['flexWrap']) {
						style['flex-wrap'] = 'wrap';
					}
				}
				if (this.containAtBreakpoint) {
					style['width'] = '100%';
					if (!style['maxWidth']) style['max-width'] = this.maxwidth || '1024px';
				}
				if (style?.['filter']?.includes('!important')) {
					style['filter'] = style['filter'].replace('!important', '');
				}
				return style;
			}
		}
	};

</script>
<style scoped>
	.vp-box {
		padding: 20px;
		display: flex;
		overflow: auto;
		flex-shrink: 0;
	}

</style>
<style>
	html,
	body,
	#app {
		position: fixed;
		width: 100%;
		height: 100%;
		margin: 0;
		display: flex;
		flex-direction: column;
	}

</style>