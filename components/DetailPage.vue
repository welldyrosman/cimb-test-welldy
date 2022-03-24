<template>
  <div class="container">
    <b-card title="LIHAT PENGGUNA" img-top tag="article">
      <hr />
      <b-card-text>
        <h4>Name : {{ user.name }}</h4>
        <h4>Gender : {{ user.gender }}</h4>
        <h4>Email : {{ user.email }}</h4>
        <hr />
      </b-card-text>

      <b-card title="DAFTAR POST" img-top tag="article">
        <b-button v-b-modal.modal-1 variant="primary" @click="addpost" class="float-right"
          >BUAT POST</b-button
        >
        <b-table id="my-table" striped hover :items="posts" :fields="fields">
          <template v-slot:cell(body)="{ item }">
            <span v-html="item.body"></span>
          </template>
          <template v-slot:cell(actions)="{ item }">
            <span><b-btn @click="delItem(item)">Delete</b-btn></span>
          </template>
        </b-table>
      </b-card>
    </b-card>
    <b-modal v-model="showmodal" @ok="handleOk" id="modal-1" title="Post Form">
      <label>title</label>
      <b-form-input
        v-model="postdata.title"
        type="text"
        placeholder="title"
      ></b-form-input>
      <br />
      <label>Body</label>
      <client-only>
        <VueEditor v-model="postdata.body" />
      </client-only>
    </b-modal>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showmodal: false,
      items: [],
      postdata: {
        user: "",
        message: "",
        title: "",
        body: "",
      },
      fields: [
        {
          key: "title",
          sortable: true,
          label: "TITLE",
        },
        {
          key: "body",
          sortable: true,
          label: "BODY",
        },
        {
          key: "actions",
          sortable: true,
          label: " ",
        },
      ],
      user: {
        name: "",
        gender: "",
        email: "",
      },
      posts: [],
      tokenStr: "c5f646a61d5cf69df45d7b6f5665de9426e0745dba41b7d6e598f271be396a68",
      apiurl: "https://gorest.co.in/public/v2/users",
    };
  },
  mounted() {
    this.postdata.user = this.$route.params.id;
    this.doQuery();
    this.doQueryPost();
  },
  methods: {
    addpost() {
      this.postdata = {
        message: "",
        title: "",
        body: "",
      };
    },
    doQuery() {
      this.$axios
        .get(apiurl + "/" + this.$route.params.id, {
          headers: {
            Authorization: `Bearer ${this.tokenStr}`,
          },
        })
        .then((ret) => {
          this.user = ret.data;
          console.log(this.user);
        });
    },
    doQueryPost() {
      this.$axios
        .get(apiurl + "/" + this.$route.params.id + "/posts", {
          headers: {
            Authorization: `Bearer ${this.tokenStr}`,
          },
        })
        .then((ret) => {
          this.posts = ret.data;
        });
    },
    delItem(row) {
      this.$axios
        .delete("https://gorest.co.in/public/v2/posts/" + row.id, {
          headers: {
            Authorization: `Bearer ${this.tokenStr}`,
          },
        })
        .then((ret) => {
          this.doQuery();
          this.doQueryPost();
        });
    },
    handleOk(bvModalEvt) {
      bvModalEvt.preventDefault();
      this.$axios
        .post(apiurl + "/" + this.$route.params.id + "/posts", this.postdata, {
          headers: {
            Authorization: `Bearer ${this.tokenStr}`,
          },
        })
        .then((ret) => {
          this.doQueryPost();
          this.showmodal = false;
        });
    },
  },
};
</script>

<style></style>
