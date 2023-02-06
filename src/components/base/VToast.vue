<template>
  <div class="showToast" v-if="isShow" :class="toast.bgColor">
    <div class="HeaderToast">
      <div class="iconHeader">
        <i :class="toast.icon"></i>
      </div>
      <div class="">
        <div class="toastTile">{{ toast.message }}</div>
      </div>
      <div class="toastClose btn-close-white" @click="isShowCloseToast">
        <!-- <i class="bi bi-x-lg btn-close-white" id="toastClose"></i> -->
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "v-toast",
  props: {
    isShow: {
      type: Boolean,
      default: false,
    },
    toast: {
      type: Object,
      default: function () {
        return {
          toast: {
            icon: "",
            message: "",
            bgColor: "",
          },
          position: "",
        };
      },
    },
  },
  data() {
    return {
      toasts: [], // Danh sách thông báo
      count: 0, // Số lượng thông báo
    };
  },
  watch: {},
  methods: {
    isShowCloseToast() {
      this.$emit("isShowToastt", false);
    },
  },
};
</script>

<style lang="scss" scoped>
.showToast {
  z-index: 1;
  max-width: 200px;
  width: 100%;
  height: 30px;
  background-color: #fff;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12);
  border-radius: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
  top: 10px;
  position: absolute;
  right: 45%;
}
.HeaderToast {
  display: flex;
  justify-content: center;
}
.toastTile {
  display: flex;
  justify-content: center;
  align-items: center;
  color: #fff;
}
.iconHeader {
  padding-right: 10px;
}

.toastClose {
  margin-left: 10px;
  cursor: pointer;
}
#toastClose {
  color: #fff;
}
</style>
