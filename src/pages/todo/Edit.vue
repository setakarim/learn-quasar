<template>
  <q-layout>
    <q-page padding>
      <div class="row q-mb-sm">
        <div class="col-8">
          <p class="text-h6">Ini Edit To Do {{ $route.params.id }}</p>
        </div>
        <div class="col-4 text-right ">
          <q-btn color="primary" to="/todo">
            Back
          </q-btn>
        </div>
      </div>
      <div class="q-pa-md">
        <q-form @submit="onSubmit" @reset="onReset" class="q-gutter-md">
          <q-input
            filled
            type="number"
            v-model="user_id"
            label="User ID *"
            min="0"
            hint="User ID berupa nomor"
            lazy-rules
            :rules="[
              val => (val !== null && val !== '') || 'Please type user id'
            ]"
          />

          <q-input
            filled
            v-model="title"
            label="Title"
            lazy-rules
            :rules="[
              val => (val !== null && val !== '') || 'Please type title'
            ]"
          />

          <!-- <q-toggle v-model="completed" label="I accept the license and terms" /> -->
          <p>Completed</p>
          <q-radio dense v-model="completed" :val="true" label="Sudah" />
          <q-radio dense v-model="completed" :val="false" label="Belum" />
          <div>
            <q-btn label="Submit" type="submit" color="primary" />
            <q-btn label="Reset" type="reset" color="red" class="q-ml-sm" />
          </div>
        </q-form>
      </div>
    </q-page>
  </q-layout>
</template>

<script>
export default {
  data() {
    return {
      user_id: 0,
      title: "",
      completed: false
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    async getData() {
      try {
        const res = await this.$axios.get(`todos/${this.$route.params.id}`);
        this.user_id = res.data.userId;
        this.title = res.data.title;
        this.completed = res.data.completed;
      } catch (error) {
        const data = this.$q.localStorage.getItem("todo");

        data.map(item => {
          if (item.id == this.$route.params.id) {
            this.user_id = item.userId;
            this.title = item.title;
            this.completed = item.completed;
          }
        });
      }
    },
    async onSubmit() {
      try {
        await this.$axios.patch(`todos/${this.$route.params.id}`, {
          userId: this.user_id,
          title: this.title,
          completed: this.completed
        });
      } catch (error) {
        const data = this.$q.localStorage.getItem("todo");

        data.map(item => {
          if (item.id == this.$route.params.id) {
            item.userId = this.user_id;
            item.title = this.title;
            item.completed = this.completed;
          }
        });

        this.$q.localStorage.set("todo", data);
      }

      this.$router.push("/todo");
    },
    onReset() {
      this.user_id = "";
      this.title = "";
      this.completed = false;
    }
  }
};
</script>

<style></style>
