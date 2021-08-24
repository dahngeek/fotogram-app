<script>
	export let name;

	import router from 'page'
	import Home from './views/Home.svelte'
	import Profile from './views/Profile.svelte'
	import Upload from './views/Upload.svelte'
	import Login from './views/Login.svelte'
	import SignUp from './views/SignUp.svelte'
	import NotFound from './views/NotFound.svelte'
	import Users from './views/Users.svelte'
	import User from './views/User.svelte'

	let page;
  	let params;
	router('/', () => (page = Home))
	router('/me', () => (page = Profile))
	router('/subir', () => (page = Upload))
	router('/login', () => (page = Login))
	router('/registro', () => (page = SignUp))
	router('/alumnos', () => (page = Users))
	router('/user/:user_name', (ctx, next) => {
		params = ctx.params
		next()},  () => page = User)
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