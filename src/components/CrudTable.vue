<template>
  <q-page>
    <div class="row">
      <q-table
        title="Uz"
        :data="product"
        :columns="columns"
        row-key="id"
        class="col-sm-12 col-md-12 col-lg-12"
        separator="cell"
        style="height: calc(100vh - 350px);"
      >
        <template v-slot:top>
          <div class="q-pa-md q-gutter-sm row items-center full-width">
            <q-input
              class="q-mr-lg col-md-4"
              bottom-slots
              v-model="filter.name_uz"
              label="Mahsulot nomi"
              dense
              @input="filterProduct('name_uz')"
            >
              <template v-slot:append>
                <q-icon
                  v-if="filter.name_uz"
                  name="close"
                  @click="
                    filter.name_uz = null;
                    filterProduct('name_uz');
                  "
                  class="cursor-pointer"
                />
                <q-icon name="search" />
              </template>
            </q-input>

            <q-input
              class="q-px-ms col-md-4"
              bottom-slots
              v-model="filter.address"
              @input="filterProduct('address')"
              label="Manzili"
              dense
            >
              <template v-slot:append>
                <q-icon
                  v-if="filter.address"
                  name="close"
                  @click="
                    filter.address = null;
                    filterProduct('address');
                  "
                  class="cursor-pointer col-md-4"
                />
                <q-icon name="search" />
              </template>
            </q-input>

            <q-space />
            <q-btn
              icon="add"
              color="primary"
              @click="addProduct()"
              class="bg-primary text-white"
              dense
            >
              <q-tooltip content-class="bg-primary"> Qo'shish </q-tooltip>
            </q-btn>
          </div>
        </template>

        <template v-slot:body-cell-created_date="props">
          <q-td :props="props">
            {{ $dateutil.formatDate(props.row.created_date, "DD.MM.YYYY") }}
          </q-td>
        </template>
        <template v-slot:body-cell-action="props">
          <q-td :props="props">
            <q-btn
              icon="create"
              color="primary"
              dense
              @click.stop="editProduct(props.row)"
            >
              <q-tooltip content-class="bg-primary"> Tahrirlash </q-tooltip>
            </q-btn>

            <q-btn
              icon="delete"
              color="negative"
              dense
              class="q-ml-sm"
              @click="deleteProduct(props.row.id)"
            >
              <q-tooltip content-class="bg-negative"> O'chirish </q-tooltip>
            </q-btn>
          </q-td>
        </template>
      </q-table>
      <template>
        <q-dialog
          v-model="persistent"
          transition-show="scale"
          transition-hide="scale"
        >
          <q-card style="width: 500px">
            <q-card-section>
              <div class="text-h6">
                {{ bean.id != null ? "Tahrirlash" : "Yangi" }}
              </div>
            </q-card-section>
            <q-separator />

            <q-card-actions align="right" class="bg-white text-teal">
              <q-form @submit.prevent="onSubmit()">
                <div class="q-pa-sm full-width q-mt-sm row">
                  <q-input
                    type="number"
                    outlined
                    class="col-xs-12 col-lg-6 col-md-12"
                    v-model="bean.product_type_id"
                    label="Mahsulotlar turi"
                    lazy-rules
                    :rules="[
                      (val) =>
                        (val && val.length > 0) || 'Please type something',
                    ]"
                  />
                  <q-input
                    type="text"
                    outlined
                    class="col-xs-12 col-lg-12 col-md-12"
                    v-model="bean.name_uz"
                    label="Mahsulot nomi"
                    lazy-rules
                    :rules="[
                      (val) =>
                        (val && val.length > 0) || 'Please type something',
                    ]"
                  />
                  <q-input
                    type="number"
                    outlined
                    class="col-xs-12 col-lg-12 col-md-12"
                    v-model="bean.cost"
                    label="Mahsulot narxi"
                    lazy-rules
                    :rules="[
                      (val) =>
                        (val && val.length > 0) || 'Please type something',
                    ]"
                  />
                  <q-input
                    type="text"
                    outlined
                    class="col-xs-12 col-lg-12 col-md-12"
                    v-model="bean.address"
                    label="Manzili"
                    lazy-rules
                    :rules="[
                      (val) =>
                        (val && val.length > 0) || 'Please type something',
                    ]"
                  />
                </div>

                <div class="q-ma-md text-right">
                  <q-btn
                    class="bg-negative text-white q-mr-sm"
                    flat
                    @click="persistent = !persistent"
                    label="Bekor qilish"
                  />
                  <q-btn
                    class="bg-primary text-white q-mr-sm"
                    flat
                    label="Qo'shish"
                    type="submit"
                  />
                </div>
              </q-form>
            </q-card-actions>
          </q-card>
        </q-dialog>
      </template>
    </div>
  </q-page>
</template>

<script>
let date = new Date().getTime();

export default {
  data() {
    return {
      filter: {
        product_type_id: null,
        name_uz: null,
        cots: null,
        address: null,
      },
      persistent: false,
      text: "",
      columns: [
        {
          name: "id",
          label: "ID",
          field: "id",
          align: "center",
          sortable: true,
          style: "width: 1rem",
        },
        {
          name: "product_type_id",
          label: "Mahsulot turi",
          field: "product_type_id",
          align: "left",
          sortable: false,
        },
        {
          name: "name_uz",
          label: "Mahsulot nomi",
          field: "name_uz",
          align: "left",
          sortable: true,
        },
        {
          name: "cost",
          label: "Mahsulot narxi",
          field: "cost",
          align: "left",
          sortable: true,
        },
        {
          name: "address",
          label: "Manzili",
          field: "address",
          align: "left",
          sortable: true,
        },
        {
          name: "created_date",
          label: "Yaritilgan sanasi",
          field: "created_date",
          align: "left",
          sortable: true,
        },
        {
          name: "action",
          label: "Harakatlar",
          field: "created_date",
          align: "left",
          sortable: true,
          style: "width: 1rem",
        },
      ],
      product: [],
      beanDefault: {
        product_type_id: null,
        name_uz: null,
        cost: null,
        address: null,
        created_date: date,
      },
      bean: {},
    };
  },

  methods: {
    getProduct() {
      this.$axios
        .get("http://94.158.54.194:9092/api/product")
        .then((res) => {
          this.product.splice(0, this.product.length, ...res.data);
        })
        .catch((err) => {
          console.log(err);
        });
    },

    editProduct(row) {
      for (let key in row) {
        this.$set(this.bean, key, row[key]);
      }
      this.persistent = true;
    },
    deleteProduct(id) {
      this.$axios
        .delete("http://94.158.54.194:9092/api/product/" + id)
        .then((response) => {
          this.getProduct();
        });
    },

    onSubmit() {
      if (this.bean.id != null) {
        this.$axios
          .put("http://94.158.54.194:9092/api/product", this.bean)
          .then((response) => {
            this.persistent = false
            this.bean = {};
            this.getProduct();
          });
      } else {
        this.$axios
          .post("http://94.158.54.194:9092/api/product", this.bean)
          .then((response) => {
            this.persistent = false
            this.bean = {};
            this.getProduct();
          });
      }
    },
    addProduct() {
      this.bean = Object.assign({}, this.beanDefault);
      console.log(this.bean);
      this.persistent = true;
    },
    filterProduct(value) {
      if (this.filter[`${value}`] === null) {
        this.getProduct();
      }
      let a = this.product.filter(
        (item) =>
          item[`${value}`].toLowerCase().indexOf(this.filter[`${value}`]) > -1
      );
      this.product.splice(0, this.product.length, ...a);
    },
  },

  mounted() {
    this.getProduct();
  },
};
</script>

<style lang="sass"></style>
