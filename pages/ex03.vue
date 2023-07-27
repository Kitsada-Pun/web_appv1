<template>
    <div>
      <h1>Hello {{ name }}</h1>
      <h1>Hello {{ upperName }}</h1>
      <input type="text" :value="name">
      <div>one-way<input type="text" :value="name"></div>
      <div>two-way<input type="text" v-model="name"></div>
      <div><button @click="onSave">SAVE</button></div>
    </div>
    <div>
      <h1>Student List</h1>
      
  <table border="1">
    <tr>
      <th>user_id</th>
      <th>Fisrtname</th> 
      <th>Email</th>
    </tr>
    <tr v-for="st in student" :key="st.user_id" >
      <td>{{ st.user_id }}</td>
      <td>{{ st.firstname }}</td>
      <td>{{  st.email }}</td>
    </tr>
  </table>
    </div>
  
  </template>
  <script>
  export default {
   data() {
    return {
      name: "kitsada",
      age: 50,
      student: [
          { user_id: '6339030001', firstname: 'sutaluk', email: 'Test1' },
          { user_id: '6339030002', firstname: 'Teerapong', email: 'Test2' },
          { user_id: '6339030003', firstname: 'Wanaruk', email: 'Test3' },
         ]
    }
   }, 
   computed: {
    upperName(){
      return this.name.toUpperCase()
    }
   },
   methods: {
    async onSave(){
      console.log(this.name)
      const url = 'http://localhost:7001/lists';
      const res = await fetch(url);
      const data = await res.json()
      this.student = data.datas
      console.log('data=>',data.datas.firstname)
    }
   },
  }
  </script>