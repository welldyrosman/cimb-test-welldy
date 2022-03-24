<template>
  <div class="container">
    <h1>DAFTAR PENGGUNA</h1>
    <div>
      <hr />
      <b-button v-b-modal.modal-1 variant="primary" @click="adduser" class="float-right"
        >BUAT PENGGUNA</b-button
      >
    </div>

    <b-table id="my-table" striped hover :items="items" :fields="fields">
      <template v-slot:cell(ACTION)="{ item }">
        <span><b-btn @click="editItem(item)">Detail</b-btn></span>
        <span><b-btn v-b-modal.modal-1 @click="editUser(item)">Edit</b-btn></span>
        <span><b-btn @click="deluser(item)">Delete</b-btn></span>
      </template>
    </b-table>
    <b-pagination
      v-model="header['x-pagination-page']"
      :total-rows="header['x-pagination-total']"
      :per-page="header['x-pagination-limit']"
      prev-text="Prev"
      next-text="Next"
      @change="pageclick"
    ></b-pagination>

    <b-modal v-model="showmodal" @ok="handleOk" id="modal-1" title="BootstrapVue">
      <label>Name</label>
      <b-form-input
        v-model="datauser.name"
        type="text"
        placeholder="Enter your name"
      ></b-form-input>
      <label>Email</label>
      <b-form-input
        v-model="datauser.email"
        type="email"
        placeholder="Enter your name"
      ></b-form-input>
      <label>Gender</label>
      <b-form-select v-model="datauser.gender" :options="options"></b-form-select>
      <label>Status</label>
      <b-form-select v-model="datauser.status" :options="optstatus"></b-form-select>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: "ViewPage",
  data() {
    return {
      showmodal: false,
      optstatus: [
        { value: null, text: "Please select Status" },
        {
          value: "active",
          text: "Active",
        },
        {
          value: "inactive",
          text: "InActive",
        },
      ],
      options: [
        { value: null, text: "Please select gender" },
        { value: "male", text: "MALE" },
        { value: "female", text: "FEMALE" },
      ],
      datauser: {
        name: "",
        email: "",
        gender: "",
        status: "",
      },
      fields: [
        {
          key: "id",
          sortable: true,
          label: "ID",
        },
        {
          key: "name",
          sortable: true,
          label: "NAME",
        },
        {
          key: "email",
          label: "EMAIL",
          sortable: true,
        },
        {
          key: "gender",
          label: "GENDER",
          sortable: true,
        },
        {
          key: "status",
          label: "STATUS",
          sortable: true,
        },
        {
          label: "ACTION",
          key: "ACTION",
        },
      ],
      items: [],
      header: {
        "x-pagination-page": 2,
        "x-pagination-total": 200,
        "x-links-next": "",
        "x-links-previous": "",
      },
      currentpage: 1,
      errorfield: [],
      tokenStr: "c5f646a61d5cf69df45d7b6f5665de9426e0745dba41b7d6e598f271be396a68",
      apiurl: "https://gorest.co.in/public/v2/users",
    };
  },
  mounted() {
    this.doQuery();
  },
  methods: {
    adduser() {
      this.datauser = {
        name: "",
        email: "",
        gender: "",
        status: "",
      };
    },
    editUser(row) {
      this.datauser = { ...row };
    },
    deluser(row) {
      this.$axios
        .delete(apiurl + "/" + row.id, {
          headers: {
            Authorization: `Bearer ${this.tokenStr}`,
          },
        })
        .then(() => {
          this.doQuery();
        });
    },
    editItem(e) {
      this.$router.push({ path: "detail/" + e.id });
    },
    pageclick(page) {
      this.currentpage = page;
      this.doQuery();
    },
    recheck(val) {
      var ret = false;
      for (var i = 0; i < this.errorfield.length; i++) {
        if (this.errorfield[i].field == val) {
          ret = true;
          break;
        }
      }
      return ret;
    },
    doQuery() {
      this.$axios
        .get(apiurl + "?page=" + this.currentpage, {
          headers: {
            Authorization: `Bearer ${this.tokenStr}`,
          },
        })
        .then((ret) => {
          console.log(ret);
          this.items = ret.data;
          this.header = ret.headers;
        });
    },
    handleOk(bvModalEvt) {
      bvModalEvt.preventDefault();
      if (!this.datauser.id) {
        this.$axios
          .post(apiurl, this.datauser, {
            headers: {
              Authorization: `Bearer ${this.tokenStr}`,
            },
          })
          .then(() => {
            this.doQuery();
            this.showmodal = false;
          })
          .catch((ret) => {
            this.errorfield = ret;
          });
      } else {
        this.$axios
          .put(apiurl + "/" + this.datauser.id, this.datauser, {
            headers: {
              Authorization: `Bearer ${this.tokenStr}`,
            },
          })
          .then(() => {
            this.doQuery();
            this.showmodal = false;
          })
          .catch((ret) => {
            this.errorfield = ret;
          });
      }
    },
  },
};
</script>

<style></style>
