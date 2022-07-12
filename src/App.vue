<template>
  <nav>
    <RouterLink to="/">Data</RouterLink>
    <RouterLink to="/about">About</RouterLink>
  </nav>

  <RouterView />
</template>

<script></script>

<style>
@import "@/assets/base.css";

#app {
  display: flex;
}

a {
  text-decoration: none;
  color: white;
  transition: 0.4s;
}

@media (hover: hover) {
  a:hover {
    background-color: #ef4d53;
  }
}

nav {
  background: #16263f;
  width: 10%;
  font-size: 12px;
  text-align: center;
}

@media (min-width: 1024px) {
  nav {
    font-size: 1rem;
  }
}
</style>
