<template>
  <div class="combobox">
    <input
      type="text"
      v-model="employee.key"
      placeholder="10 bản ghi trên trang"
      @click="togglePosisons()"
    />
    <div class="StyleCombobox" v-if="isShowPosion">
      <ul
        class="listPosison"
        v-for="employee in data"
        :key="employee.key"
        :value="employee.key"
        :class="{ activeBGC: activeClick === employee.value }"
      >
        <li class="listItem" @click="selectItemDepart(employee.key)">
          {{ employee.value }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "combobox-paging",

  props: {
    data: {
      type: Array,
    },
    propKey: {
      // Key của dữ liệu
      required: false,
      default: "key",
    },
    propValue: {
      // Value của dữ liệu
      required: false,
      default: "value",
    },
    propApi: {
      // Nếu có api thì lấy dữ liệu từ api
      type: String,
      required: false,
      default: null,
    },
    placeholder: {
      type: String,
    },
  },
  data() {
    return {
      dataApi: [], //dữ liệu lấy từ api
      isShowPosion: false,
      employee: {},
    };
  },
  methods: {
    togglePosisons() {
      this.isShowPosion = !this.isShowPosion;
    },
    selectItemDepart(key) {
      this.employee.key = key;
      this.employee.key = this.formatEnum(key);
      this.$emit("update:modelValue", key);

      this.togglePosisons();
    },
    formatEnum(key) {
      switch (key) {
        case 10:
          return "10 bản ghi trên trang";
        case 20:
          return "20 bản ghi trên trang";
        case 50:
          return "50 bản ghi trên trang";
        case 100:
          return "100 bản ghi trên trang";
      }
    },
  },
};
</script>
<style lang="scss" scoped>
.combobox {
  margin-left: 151px;
}
label {
  display: block;
}
input {
  position: relative;
  width: 250px;
  height: 34px;
  border: 1px solid #999;
  outline: none;
  padding: 0 10px;
}
.listItem {
  padding: 10px 20px;
  font-family: sans-serif;
}

ul {
  list-style: none;
  width: 100%;
  z-index: 1;
  li {
    max-width: 260px;
    width: 100%;
    z-index: 1;
  }
}
.StyleCombobox .listPosison {
  list-style: none;
  width: 100%;
  z-index: 1;
}

.StyleCombobox {
  border: 1px solid #bbbbbb;
  width: 250px;
  cursor: pointer;
  position: absolute;
  bottom: 60px;
  z-index: 1;
  background-color: #fff;
  .listPosison {
    &:hover {
      background-color: #109160;
      color: #fff;
    }
  }
}
* {
  padding: 0;
  margin: 0;
}
</style>
