<script lang="ts">
  import Router, { link } from 'svelte-spa-router';
  import routes from '@/router';
  // import HomeSv from '@/pages/Home.svelte';
</script>

<div>
  <a use:link={"/home"}>Home</a>

  <Router routes={routes}></Router>

  <!-- <HomeSv></HomeSv> -->
</div>
