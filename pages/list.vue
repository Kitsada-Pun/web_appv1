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
        <th>Action</th>
      </tr>
      <tr v-for="st in student" :key="st.id">
        <td>{{ st.id }}</td>
        <td>{{ st.username }}</td>
        <td>{{ st.password }}</td>
        <td>{{ st.dep }}</td>
        <td>
          <v-icon size="small" class="me-2" @click="editItem(st.id)">
            mdi-pencil
          </v-icon>
          <v-icon size="small" @click="deleteAlert(st.id)">
            mdi-delete
          </v-icon>
        </td>
      </tr>
    </table>
  </div>
  <div class="text-center">
    <v-dialog v-model="dialog" width="500">
      <v-card>
        <v-card-title class="text-h5 grey lighten-2">
          สถานะการลบ
        </v-card-title>

        <v-card-text>
          {{ status }}
        </v-card-text>
        <v-divider></v-divider>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="deleteItem(id)">
            ตกลง
          </v-btn>
          <v-btn color="primary" text @click="dialog = false">
            ยกเลิก
          </v-btn>

        </v-card-actions>

      </v-card>
    </v-dialog>
  </div>

  <v-dialog v-model="deleteSuscess" width="500">
    <v-card>
      <v-card-title class="text-h5 grey lighten-2">
        สถานะการลบ
      </v-card-title>

      <v-card-text>
        ลบข้อมูลสำเร็จ
      </v-card-text>

      <v-divider></v-divider>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="primary" text @click="deleteSuscess = false">
          ตกลง
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>
<script>
const apiUrl = 'http://192.168.30.156:7001'
export default {
  data() {
    return {
      status: '',
      deleteSuscess: false,
      dialog: false,
      name: "alongkorn",
      age: 50,
      student: [
        { id: '111', username: '6339030001', password: 'sutaluk', dep: 'Test1' },
        { id: '112', username: '6339030002', password: 'Teerapong', dep: 'Test2' },
        { id: '113', username: '6339030003', password: 'Wanaruk', dep: 'Test3' },
      ]
    }
  },
  methods: {
    editItem(id) {
      console.log('id', id)
      //  this.$router.replace('/edit?id='+ id)
      this.$router.push('/edit?id=' + id)

    },
    async deleteItem(id) {
      console.log('id_true=', id)
      const url = apiUrl + '/del?id=' + id;
      const res = await fetch(url);
      const data = await res.json()
      this.dialog = false
      this.deleteSuscess = true
      this.onShow()
    },
    async deleteAlert(id) {
      console.log('id=', id)
      this.dialog = true
      this.id = id
      //  this.$router.replace('/edit?id='+ id)

    },
    async onShow() {
      const url = apiUrl + '/list';
      const res = await fetch(url);
      const data = await res.json()
      this.student = data.datas
      console.log('data=>', data.datas)
    }
  },
}
</script>