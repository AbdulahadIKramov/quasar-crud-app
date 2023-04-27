<template>
  <q-page padding>
    <CrudTable/>
  </q-page>
</template>

<script>
import CrudTable from 'src/components/CrudTable.vue';

export default {
    name: "PageIndex",
    components: { CrudTable }
}
</script>
