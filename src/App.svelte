<script>
    import { Router, Route, Link } from 'svelte-routing';
    import Home from './Home.svelte';
    import Weather from './Weather.svelte';
    import Forecast from './Forecast.svelte';
</script>
<Router>
<nav>
    <Link to="/">Home</Link> |
    <Link to="/weather">Current Weather</Link> |
    <Link to="/forecast">Forecast</Link>
</nav>

<div>
 <Route path="/" component={Home} />
    <Route path="/weather" component={Weather} />
    <Route path="/forecast" component={Forecast} />


</div>
   

</Router>