<template>
  <div>
    <h1>Student List</h1>
    <div><button @click="onShow">List</button></div>
    <table border="1">
      <tr>
        <th>id</th>
        <th>Username</th>
        <th>Password</th>
        <th>dep</th>
        <th>Picture</th>
        <th>Action</th>
      </tr>
      <tr v-for="st in student" :key="st.id">
        <td>{{ st.id }}</td>
        <td>{{ st.username }}</td>
        <td>{{ st.password }}</td>
        <td>{{ st.dep }}</td>
        <td>
          <v-img height="100%" cover :src="`${baseUrl}${st.img}`"> </v-img>
        </td>
        <td>
          <v-icon size="small" class="me-2" @click="editItem(st.id)">
            mdi-pencil
          </v-icon>
          <v-icon size="small" @click="deleteAlert(st.id)"> mdi-delete </v-icon>
        </td>
      </tr>
    </table>
  </div>
  <div class="text-center">
    <v-dialog v-model="dialog" width="500">
      <v-card>
        <v-card-title class="text-h5 grey lighten-2"> สถานะการลบ </v-card-title>
        <v-card-text>
          {{ status }}
        </v-card-text>
        <v-divider></v-divider>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="deleteItem(id)"> ตกลง </v-btn>
          <v-btn color="primary" text @click="dialog = false"> ยกเลิก </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>
<script>
export default {
  data() {
    return {
      status: "",
      baseUrl: "http://localhost:7001/photos/",
      dialog: false,
      name: "alongkorn",
      age: 50,
      student: [
        {
          id: "111",
          username: "6339030001",
          password: "sutaluk",
          dep: "Test1",
        },
        {
          id: "112",
          username: "6339030002",
          password: "Teerapong",
          dep: "Test2",
        },
        {
          id: "113",
          username: "6339030003",
          password: "Wanaruk",
          dep: "Test3",
        },
      ],
    };
  },
  created() {
    this.onShow();
  },
  methods: {
    editItem(id) {
      console.log("id", id);
      //  this.$router.replace('/edit?id='+ id)
      this.$router.push("/edit?id=" + id);
    },
    async deleteItem(id) {
      this.dialog = false;
      console.log("id_true=", id);
      const url = "http://localhost:7001/del?id=" + id;
      const res = await fetch(url);
      const data = await res.json();
      setTimeout(() => {
        this.onShow();
      }, 1500);
    },
    async deleteAlert(id) {
      console.log("id=", id);
      this.dialog = true;
      this.id = id;
      //  this.$router.replace('/edit?id='+ id)
    },
    async onShow() {
      const url = "http://localhost:7001/list";
      const res = await fetch(url);
      const data = await res.json();
      this.student = data.datas;
      console.log("data=>", data.datas);
    },
  },
};
</script>