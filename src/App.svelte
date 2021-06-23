<script>
	export let name;

	import router from 'page'
	import Home from './views/Home.svelte'
	import Login from './views/Login.svelte'
	import SignUp from './views/SignUp.svelte'
	import NotFound from './views/NotFound.svelte'

	let page;
  	let params;
	router('/', () => (page = Home))
	router('/login', () => (page = Login))
	router('/registro', () => (page = SignUp))
	router('*', () => (page = NotFound))

	router.start()

	// Components
	import Header from './components/Header.svelte'
</script>

<main>
	<Header></Header>
	<svelte:component this="{page}" params="{params}" />
</main>

<style global>
	@tailwind base;
	@tailwind components;
	@tailwind utilities;
</style>