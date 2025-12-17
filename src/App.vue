<template>
	<component
		:is="tag"
		:style="computedStyle"
		@pointerenter="isHover = true"
		@pointerleave="isHover = false"
	>
		<slot>
			{{ tag.toUpperCase() }} Title
		</slot>
	</component>
</template>
<script>
	import {
		parse,
		getStyle
	} from '@componentor/adaptive';
	export default {
		wysiwyg: true,
		inject: {
			theme: {
				default: ''
			},
			breakpoint: {
				default: ''
			}
		},
		data() {
			return {
				isHover: false
			};
		},
		props: {
			tag: {
				type: String,
				default: 'h1',
				options: ['h1', 'h2', 'h3', 'h4', 'h5', 'h6']
			},
			adapt: {
				type: [String, Object, Array],
				default: ''
			},
			state: {
				type: [String, Array],
				default: ''
			},
			themeName: {
				type: String,
				default: ''
			},
			breakpointName: {
				type: String,
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
		computed: {
			adaptString() {
				if (!this.adapt) return '';
				if (typeof this.adapt === 'string') return this.adapt;
				if (Array.isArray(this.adapt)) {
					return this.adapt.map(item => {
							if (typeof item === 'string') return item;
							return Object.entries(item)
								.map(([key, value]) => `${key}:${value}`)
								.join('; ');
						})
						.join('; ');
				}
				return Object.entries(this.adapt)
					.map(([key, value]) => `${key}:${value}`)
					.join('; ');
			},
			stateArray() {
				const native = [];
				if (this.isHover) native.push('hover');
				if (!this.state) return native;
				const custom = Array.isArray(this.state) ? this.state : this.state.split(':');
				return [...native, ...custom];
			},
			computedStyle() {
				if (!this.adaptString) return '';
				const parsed = parse(this.adaptString);
				return getStyle(parsed, {
					theme: this.themeName || this.theme,
					breakpoint: this.breakpointName || this.breakpoint,
					states: this.stateArray,
					breakpointStrategy: this.breakpointStrategy,
					themeStrategy: this.themeStrategy
				});
			}
		}
	};

</script>