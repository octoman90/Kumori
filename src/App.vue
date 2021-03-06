<template>
	<div id="app" :style="theme" v-touch:swipe="emitSwipeEvent">
		<svg class="zero"> <!-- Used for blurring NSFW images -->
			<filter id="sharpBlur">
				<feGaussianBlur stdDeviation="5"></feGaussianBlur>
				<feColorMatrix type="matrix" values="1 0 0 0 0, 0 1 0 0 0, 0 0 1 0 0, 0 0 0 9 0"></feColorMatrix>
				<feComposite in2="SourceGraphic" operator="in"></feComposite>
			</filter>
		</svg>

		<router-view />

		<ModalsLayer />
	</div>
</template>

<script setup>
	import ModalsLayer from "./components/layers/ModalsLayer.vue"

	import API from "./api.js"
	import { useTheme } from "./hooks/theme.js"
	import { usePostLinkEventHandler } from "./hooks/postLinkEventHandler.js"
	import { usePageEndReachedEventEmitter } from "./hooks/pageEndReachedEventEmitter.js"

	const theme = useTheme()
	usePostLinkEventHandler()
	usePageEndReachedEventEmitter()

	API.board.requestMany()

	function emitSwipeEvent(direction) {
		emitter.emit(`swipe-${direction}`)
	}
</script>

<script>
	export default {
		created() {
			function updateHTMLLocale(locale) {
				document.documentElement.setAttribute("lang", locale.split("-")[0])
			}

			updateHTMLLocale(this.$store.state.settings.locale)
			this.$store.watch(state => state.settings.locale, (newLocale) => {
				this.$i18n.locale = newLocale
				updateHTMLLocale(newLocale)
			})
		}
	}
</script>

<style>
	#app {
		font-family: 'Noto Sans', 'Avenir', Helvetica, Arial, sans-serif;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		color: var(--text-color);
		display: flex;
		background-color: var(--background-color);
		min-height: 100vh;
	}

	a {
		color: var(--link-over-color);
		cursor: pointer;
		transition: color 0.1s;
		text-decoration: none;
	}

	a:hover {
		color: var(--link-hover-color);
	}

	.icon{
		filter: invert(var(--icon-invert));
	}

	q::before{
		content: ''
	}

	q::after{
		content: ''
	}

	q{
		color: var(--text-green-color)
	}

	span.spoiler{
		background: 0 0;
		transition: text-shadow 0.1s, color 0.1s;
	}

	span.spoiler:not(:hover){
		color: transparent;
		text-shadow: 0 0 .3em var(--text-color);
	}

	code {
		background: var(--background-color);
		padding: .1rem .3rem;
		font-size: 1rem;
		user-select: all;
	}

	pre {
		margin: 0;
		white-space: pre-wrap;
	}

	pre > code {
		display: block;
		padding: var(--gap-size);
		margin: var(--gap-size) 0;
	}

	u {
		text-decoration-skip-ink: none;
	}

	.zero {
		height: 0;
		width: 0;
	}

	button {
		border: none;
		background-color: transparent;
		cursor: pointer;
		vertical-align: middle;
		padding: 0 0.25em;
		outline: none;
	}

	input, textarea {
		outline: none;
	}
</style>
