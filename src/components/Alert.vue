<template>
  <div class="alert">
    <h3>
      <slot></slot>
    </h3>
  </div>
</template>

<style scoped>
.alert {
  background-color: #ff5252;
  border-bottom: 2px solid #eee;
  flex-shrink: 0;
}

a {
  color: #eee;
}

h3 {
  font-size: 1.5rem;
  margin: 0.5em 1em;
  text-align: center;
}
</style>
