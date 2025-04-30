<template>
  <div id="app">
    <main>
      <router-view />
    </main>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<!-- Font Styling for Overall App -->
<style>
body {
  font-family: 'Roboto', sans-serif;
  margin: 0;
  padding: 0;
}

/* Ensure your #app fills the screen too */
#app {
  min-height: 100vh;
  width: 100%;
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  transition: background-image 0.5s ease-in-out;
}

h1, h2, h3, .location, .temp, .weather {
  font-family: 'Raleway', sans-serif;
}

.nav-links {
  display: flex;
  gap: 20px;
  justify-content: center;
  margin-bottom: 20px;
}

.nav-links a {
  color: white;
  text-decoration: none;
  font-weight: bold;
}

main {
  min-height: 100vh;
  padding: 25px;
}
</style>
