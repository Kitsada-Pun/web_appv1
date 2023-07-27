<template>
    <div>
      <h1>Hello {{ name }}</h1>
      <input type="text" :value="name">
      <input type="text" :value="age">
    </div>
  </template>
<script>
export default {
    data() {
        return {
            name: "Kitsada",
            age: 10,
        }
    },
}
</script>