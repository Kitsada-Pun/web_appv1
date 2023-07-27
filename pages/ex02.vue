<template>
    <div>
      <h1>Hello {{ name }}</h1>
      <h1>Hello {{ age }}</h1>
      <input type="text" :value="name">
      <div>one-way<input type="text" :value="age"></div>
      <div>two-way<input type="text" v-model="age"></div>
      <div>two-way<input type="text" v-model="name"></div>
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