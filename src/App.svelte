<script>
	import { Route, Router } from "svelte-routing";
	import Landingpage from "./components/Landingpage.svelte";
	import Profile from "./components/Profile Components/Profile.svelte";
	import Home from "./components/Home.svelte";
</script>

<div class="App">
	<Router>
		<Route path="/" component={Landingpage} />
		<Route path="/home" component={Home} />
		<Route path="/profile" component={Profile} />
	</Router>
</div>

<style>
</style>
