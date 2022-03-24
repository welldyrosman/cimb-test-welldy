<template>
  <div class="container">
    <h1>DAFTAR PENGGUNA</h1>
    <div>
      <hr />
      <b-button v-b-modal.modal-1 variant="primary" class="float-right"
        >BUAT PENGGUNA</b-button
      >
    </div>

    <b-table id="my-table" striped hover :items="items" :fields="fields"></b-table>
    <b-pagination
      v-model="header['X-Pagination-Page']"
      :total-rows="header['X-Pagination-Total']"
      :per-page="header['X-Pagination-Limit']"
      first-text="First"
      prev-text="Prev"
      next-text="Next"
      last-text="Last"
      @page-click="pageclick"
    ></b-pagination>

    <b-modal v-model="showmodal" @ok="handleOk" id="modal-1" title="BootstrapVue">
      <label>Name</label>
      <b-form-input
        v-model="datauser.name"
        :class="{ invalid: recheck('name') }"
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
      <b-form-select v-model="datauser.gender" :options="optstatus"></b-form-select>
      <label>Status</label>
      <b-form-select v-model="datauser.status" :options="options"></b-form-select>
    </b-modal>
  </div>
</template>

<script lang="ts">
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
          sortable: false,
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
          label: "Action",
        },
      ],
      items: [],
      header: {
        "X-Pagination-Page": 2,
      },
      errorfield: [],
      tokenStr: "620cc4d1804a47f47b746644b42ff930b73e13f5332bc664c515c910322a5fc2",
    };
  },
  mounted() {
    this.$axios
      .get(
        "https://gorest.co.in/public/v2/users?page=" + this.header["X-Pagination-Page"]
      )
      .then((ret) => {
        this.items = ret.data;
      });
  },
  methods: {
    pageclick() {
      alert();
    },
    recheck(val: Array<any>) {
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
      this.$axios.get("https://gorest.co.in/public/v2/users").then((ret) => {
        console.log(ret);
        this.items = ret.data;
        this.header = ret.headers;
      });
    },
    handleOk(bvModalEvt: { preventDefault: () => void }) {
      bvModalEvt.preventDefault();
      this.$axios
        .post("https://gorest.co.in/public/v2/users", this.datauser, {
          headers: {
            Authorization: `Bearer ${this.tokenStr}`,
          },
        })
        .then(() => {
          this.showmodal = false;
        })
        .catch((ret) => {
          this.errorfield = ret;
        });
    },
  },
};
</script>

<style></style>
