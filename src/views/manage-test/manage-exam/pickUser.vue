<template>
  <el-transfer v-model="value" :data="UserList"></el-transfer>
</template>

<script>
import {getAllUser} from "@/api/test/exam";
import user from "@/store/modules/user";

export default {
  data() {
    const generateData = _ => {

    }
    return {
      testOptionId: -1,
      list: null,
      UserList: [],
    }
  },

  methods: {

  },
  name: "pickUser"
}
</script>

<style scoped>

</style>
