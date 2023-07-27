<template>
  <h1>EDIT DATA ID: {{ id }}</h1>
  <v-form ref="form" v-model="valid" lazy-validation>
    <v-text-field v-model="username" :counter="10" label="UserName" required></v-text-field>

    <v-text-field v-model="password" label="Password" required></v-text-field>

    <v-select v-model="dep" :items="items" label="DEPARTMENT" required></v-select>
    <v-btn color="error" class="mr-4" @click="reset">
      Reset Form
    </v-btn>
    <v-btn color="warning" @click="doUpdate">
      UPDATE
    </v-btn>
  </v-form>


  <div class="text-center">
    <v-dialog v-model="dialog" width="500">
      <v-card>
        <v-card-title class="text-h5 grey lighten-2">
          สถานะการบันทึก
        </v-card-title>

        <v-card-text>
          แก้ไขข้อมูลสำเร็จ 
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="dialog = false">
            ตกลง
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>

  <div class="text-center">
    <v-dialog v-model="dialog_error" width="500">
      <v-card>
        <v-card-title class="text-h5 grey lighten-2">
          สถานะการบันทึก
        </v-card-title>

        <v-card-text>
          แก้ไขข้อมูลไม่สำเร็จ username ซ้ำ
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="dialog_error = false">
            ตกลง
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>
<script>
const apiUrl = 'http://192.168.30.156:7001'
export default {
  data: () => ({
    valid: true,
    id : '',
    username: '',
    password: '',
    dep: null,
    dialog: false,
    dialog_error: false,
    valid: true,
    username: '',
    password: '',
    dep: null,
    dialog: false,
    items: [
      'IT',
      'computer',
      'electronic',
      'electircal power',
    ],
  }),
  async created(){
    console.log('id=', this.$route.query.id)
    this.id = this.$route.query.id 
      const url = apiUrl + '/listedit?id=' + this.id ;
      const res = await fetch(url);
      const data = await res.json()
      console.log('data=', data.datas.username)
      this.username = data.datas.username
      this.password = data.datas.password
      this.dep = data.datas.dep
  },
  methods: {
    async doUpdate() {
      console.log('save data')
      console.log(this.username)
      console.log(this.password)
      console.log(this.dep)

      //http://localhost/7001/insert?name=username&passwd=password&dep=dep
      const url = apiUrl + '/update?username=' + this.username + '&passwd=' + this.password + '&dep=' + this.dep + '&id='+ this.id;
      const res = await fetch(url);
      const data = await res.json()
      console.log(data.ok)
      if (data.ok == 1) {
        console.log('update success')
        this.dialog = true
      }else {
        this.dialog_error = true
      }
    },
    reset() {
      this.$refs.form.reset()
    },
  },
}
</script>