<!-- components này hiển thị danh sách số bản ghi -->
<template>
  <div class="footer">
    <div class="total-item2">Tổng số: {{ totalRecord }} bản ghi</div>
    <div>
      <v-comboboxApi
        v-model="vPageSize"
        :data="records"
        :propKey="key"
        :propValue="value"
        :placeholder="placeholder"
      />
    </div>

    <div class="vPagingPage">
      <div class="vPagingPage_prev" @click="changePage('prev')">Trước</div>
      <div
        class="vPagingPage_item"
        v-for="(page, index) in pages"
        :key="index"
        @click="changePage(page)"
        :class="{ 'vPagingPage_item--active': page == pageNumber }"
      >
        {{ page }}
      </div>
      <div class="vPagingPage_after" @click="changePage('after')">Sau</div>
    </div>
  </div>
</template>

<script>
import VComboboxApi from "./VComboboxApi.vue";
export default {
  components: { VComboboxApi },
  props: {
    pageNumber: {
      // trang hiện tại
      type: Number,
      default: 1,
    },
    totalRecord: {
      // tổng số bản ghi
      type: Number,
      default: 0,
    },
    pageSize: {
      // số bản ghi trên 1 trang
      type: Number,
      default: 20,
    },
    options: {
      // danh sách số bản ghi trên 1 trang
      type: Array,
      default: () => [],
    },
    autoSetPageNumber: {
      // tự động set lại thành trang 1 khi thay đổi pageSize
      type: Boolean,
      default: true,
    },
    delta: {
      // số trang hiển thị phần 2 bên của trang hiện tại
      type: Number,
      default: 2,
    },
    placeholder: {
      type: String,
    },
  },
  name: "VPanging",
  data() {
    return {
      employee: {},
    };
  },
  computed: {
    // đùng để set và get giá trị pageSize
    vPageSize: {
      get() {
        return this.pageSize;
      },
      set(value) {
        this.$emit("update:pageSize", value);
      },
    },
    // truyền data cho Combobox thông qua props
    // hiển thị list danh sách trên 1 trang
    records() {
      return [
        {
          key: 10,
          value: "10 bản ghi trên trang",
          placeholder: "10 bản ghi trên trang",
        },
        {
          key: 20,
          value: "20 bản ghi trên trang",
          placeholder: "20 bản ghi trên trang",
        },
        {
          key: 50,
          value: "50 bản ghi trên trang",
          placeholder: "50 bản ghi trên trang",
        },
        {
          key: 100,
          value: "100 bản ghi trên trang",
          placeholder: "100 bản ghi trên trang",
        },
      ];
    },

    // tính toán tổng số trang
    totalPage() {
      return Math.ceil(this.totalRecord / this.pageSize);
    },

    // tính toán hiển thị số trang

    pages() {
      let pages = []; // danh sách trang
      for (let i = 1; i <= this.totalPage; i++) {
        if (
          i == 1 ||
          i == this.totalPage ||
          (i >= this.pageNumber - this.delta &&
            i <= this.pageNumber + this.delta)
        ) {
          // nếu là trang đầu hoặc trang cuối, hoặc trang hiện tại và 2 trang xung quanh thì hiển thị phần rìa là 2 trang
          pages.push(i);
        } else if (
          i == this.pageNumber - (this.delta + 1) ||
          i == this.pageNumber + (this.delta + 1)
        ) {
          // nếu là trang xung quanh trang hiện tại thì hiển thị dấu ...
          pages.push("...");
        }
      }
      // trả về mảng các trang
      return pages;
    },
  },
  methods: {
    // click để di chuyển số trang
    changePage(page) {
      if (page == "...") {
        return;
      }
      if (page == "prev" && this.pageNumber <= 1) {
        return;
      }
      if (page == "after" && this.pageNumber >= this.totalPage) {
        return;
      }
      if (page == "prev") {
        page = this.pageNumber - 1;
      }
      if (page == "after") {
        page = this.pageNumber + 1;
      }
      this.$emit("update:pageNumber", page);
    },
  },
};
</script>
<style lang="scss" scoped>
* {
  padding: 0;
  margin: 0;
  font-family: sans-serif;
}

.footer {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-top: 20px;
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
  padding: 0 16px;
}
.vPagingPage_item--active {
  color: #109160;
}
.vPagingPage {
  display: flex;
  align-items: center;
  padding: 0 10px;
  &_prev {
    padding: 0 10px;
  }
  &_item {
    padding: 0 10px;
  }
  &_after {
    padding: 0 10px;
  }
}
#dropDown {
  position: absolute;
  top: 0px;
  right: 0px;
  width: 10px;
  height: 6px;
  padding: 14px;
  border: 1px solid #bbbbbb;
  cursor: pointer;
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
  width: 282px;
  cursor: pointer;
  position: absolute;
  bottom: 40px;
  .listPosison {
    &:hover {
      background-color: #109160;
      color: #fff;
    }
  }
}
.vPagingPage_prev {
  cursor: pointer;
}
.vPagingPage_after {
  cursor: pointer;
}
* {
  padding: 0;
  margin: 0;
}
</style>
