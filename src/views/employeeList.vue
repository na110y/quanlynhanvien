<template>
  <div class="content">
    <div class="content__employee">
      <div class="content__employee-btn">
        <div class="content__employee-txt">Danh sách nhân viên</div>
        <div
          class="content__employee-add"
          tabindex="1"
          @click="btnAddDetail"
          ref="txtAddEmployee"
        >
          <img
            src="../pages/icon/add.png"
            alt="error add employee"
            id="btn__employee-add"
          />
          <div class="btn__employee-txt">Thêm nhân viên</div>
        </div>
      </div>
      <div class="employee__content">
        <div class="content__employee-search">
          <ul class="employee__listSearch">
            <li class="employee__listSearch-item">
              <input
                type="text"
                placeholder="Tìm kiếm theo mã nhân viên"
                id="search-manv"
                v-model="searchEmployeeCode"
              />
              <img src="../pages/icon/search.png" alt="search" id="search" />
            </li>

            <li id="employee__refresh" @click="refreshData">
              <img src="../../src/pages/icon/refresh.png" alt="refresh" />
            </li>
            <li class="excel" @click="tableToExcel()">
              <img src="../pages/icon/exel.svg" alt="" id="execell" />
            </li>
          </ul>
        </div>
        <div class="employeeL">
          <table id="employeeList" :class="loading" ref="exportable_table">
            <thead>
              <tr>
                <th class="text-left">MÃ NHÂN VIÊN</th>
                <th class="text-left">TÊN NHÂN VIÊN</th>
                <th class="text-left">GIỚI TÍNH</th>
                <th class="text-center">NGÀY SINH</th>
                <th class="text-left">SỐ CMND</th>
                <th class="text-left">CHỨC DANH</th>
                <th class="text-left">TÊN ĐƠN VỊ</th>
                <th class="text-center">CHỨC NĂNG</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="employee in employees"
                :key="employee.EmployeeId"
                @click="activeClickTable(employee.EmployeeId)"
                :class="{ activeBGC: activeClick === employee.EmployeeId }"
                @dblclick="rowOnClick(employee)"
                @change="papingTest"
              >
                <td class="text-left">{{ employee.EmployeeCode }}</td>
                <td class="text-left">{{ employee.FullName }}</td>
                <td class="text-left">{{ formatGender(employee.Gender) }}</td>
                <td class="text-center">
                  {{ formatDate(employee.DateOfBirth) }}
                </td>
                <td class="text-left">{{ employee.IdentityNumber }}</td>
                <td class="text-left">
                  {{ formatChucDanh(employee.PositionId) }}
                </td>
                <td class="text-left">
                  {{ formatDonVi(employee.DepartmentId) }}
                </td>

                <td class="">
                  <div class="actionEdit">
                    <div class="editAction">Sửa</div>
                    <div
                      class="delEmployee"
                      @click="btnOnClickShowOption(employee.EmployeeCode)"
                    ></div>
                    <ul
                      class="option-down"
                      :ref="`dropdown-${employee.EmployeeCode}`"
                    >
                      <li class="btn-coppy">Nhân bản</li>
                      <li class="btn-delete" @click="btnOnClickDel(employee)">
                        Xóa
                      </li>
                      <li class="btn-ngung">Ngừng sử dụng</li>
                    </ul>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
          <iframe id="dummyFrame" style="display: none"></iframe>
        </div>
        <div class="footerPaging">
          <div class="paging">
            <VPanging
              v-model:pageNumber="pagination.pageNumber"
              v-model:pageSize="pagination.pageSize"
              v-model:totalRecord="employeeListFilter.TotalRecord"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
  <div class="loading" v-if="isLoading">
    <img
      src="../pages/img/loading.svg"
      alt=""
      id="loadingIcon"
      @click="OnClickShowDropdown"
    />
  </div>

  <employee-detail
    @isShowDialogEmployee="showDialongEmployeeDetail"
    :isShowFormAdd="isShowDialog"
    :employeeSelected="employeeSelected"
    :employeeSelectedID="employeeSelectedID"
    :formMode="formDetailMode"
    :combobox="comboboxEmployeeDetail"
    :loadDataTableDetail="loadDataTableDetail"
  />
</template>

<script>
/* eslint-disable */
import axios from "axios";
import employeeDetail from "./employeeDetail.vue";
import VPanging from "../components/base/VPanging.vue";
export default {
  name: "the-employee",
  components: { employeeDetail, VPanging },
  props: ["employeeDetail", "VPanging", "totalRecor"],
  data() {
    return {
      employees: [],
      isLoading: false,
      activeClick: null,
      isShowDialog: false,
      employeeSelectedID: null,
      formDetailMode: 0,
      isShowOption: false,
      employeeSelected: {},
      combobox: {},
      searchEmployeeCode: "",
      pagination: {
        pageNumber: 1, // số trang hiện tại
        pageSize: 10, // số bản ghi trên 1 trang
      },
      employeeListFilter: [], // danh sách lọc dữ liệu dùng để phân trang
    };
  },
  computed: {
    /**
     * @description: filter Search employeeCode ( tìm kiếm nhân viên theo mã )
     * Author: NSDThinh 31/01/2023
     */
    filterUsers() {
      return this.employees.filter((searchEmployee) => {
        return searchEmployee.EmployeeCode.includes(this.searchEmployeeCode);
      });
    },
    /**
     * @description: hàm này dùng để phân số bản ghi trên 1 trang
     * Author: NSDThinh 31/01/2023
     */
    listDataFilter() {
      const star = (this.pageNumber - 1) * this.pageSize;
      const end = this.pageNumber * this.pageSize;
      let em = this.employeeListFilter;
      const result = em.slice(star, end);
      return result;
    },
  },

  methods: {
    /**
     * @description: hàm này dùng để tải dữ liệu từ table ra excel
     * @param: {any}
     * Author: NSDThinh 31/01/2023
     */
    tableToExcel() {},
    /**
     * @description: Hàm này dùng để chuyển đổi giữa các bản ghi từ 1 - 10 và ngược lại
     * Author: NSDThinh - 12//09/2022
     */
    changePage(num) {
      this.currentPage = this.currentPage + num;
    },
    /**
     * @description: hàm này dùng để tải lại dữ liệu của trang
     * Author: NSDThinh 12//09/2022
     */
    loadDataTableDetail() {
      this.getAllEmployee();
      // this.$emit("update:modelValue", employee);
    },
    /**
     * @description: Hàm này dùng để làm mới lại trang khi nhấn vào refresh
     * Author: NSDThinh - 12//09/2022
     */
    refreshData() {
      window.location.reload();
    },

    /**
     * @description: Hàm này dùng để tắt form khi nhấn vào nút xóa nhân viên
     * Author: NSDThinh - 12//09/2022
     */
    btnOnClickDel(employee) {
      const self = this;
      if (employee) {
        var confirmm = confirm(
          "Bạn có thực sự muốn xóa nhân viên" +
            " " +
            "<" +
            employee.EmployeeCode +
            ">" +
            " không? "
        );
        if (confirmm == true) {
          axios
            .delete(`https://cukcuk.manhnv.net/api/v1/Employees/${employee.EmployeeId}`)
            .then(() => {
              alert("Xóa thành công!");
              // hàm này dùng để làm mới lại trang khi xóa nhân viên
              self.getAllEmployee();
            })
            .catch(() => {
              alert("Xóa không thành công" + " " + "<" + employee.EmployeeCode + ">");
            });
        } else {
          alert("Hủy thành công");
        }
      }
    },

    /**
     * @description: Hàm này dùng để hiển thị form sửa nhân viên khi ấn đúp chuột ( editor employee )
     * @param {Object}  employee : lấy giá trị tham số EmployeeId từ api vào để binding giá trị khi sửa
     * Author: NSDThinh - 12/09/2022
     */
    rowOnClick(employee) {
      this.employeeSelected = employee;
      this.employeeSelectedID = employee.EmployeeId;
      this.showDialongEmployeeDetail(true);
      this.$emit("update:modelValue", employee);
      this.formDetailMode = 0;
    },

    /**
     * @description: Hàm này dùng để tắt form phân trang
     * Author: NSDThinh - 14/09/2022
     */
    isShowItemPaging() {
      this.isShowPaging = !this.isShowPaging;
    },
    /**
     * @description: Hàm này dùng để mở và đóng form phân trang
     * Author: NSDThinh - 14/09/2022
     */

    /**
     * @description: Hàm này dùng để hiển thị form chỉnh sửa nhi nhấn vào form dropdown trong danh sách nhân viên
     * Author: NSDThinh - 12/09/2022
     */
    btnOnClickShowOption(eCode) {
      let dropdown = this.$refs[`dropdown-${eCode}`][0];
      dropdown.style.display = "block";
      this.isShowOption = true;
    },

    /**
     * @description: Hàm này dùng để hiển thị form  nhân viên khi ấn chuột ( editor employee )
     * Author: NSDThinh - 12/09/2022
     */
    btnAddDetail() {
      this.showDialongEmployeeDetail(true);
      this.formDetailMode = 1;
    },

    /**
     * @description: Hàm này dùng để tắt hoặc mở form thêm nhân viên ( on/off form  employee )
     * @param {Object} isShowDialog - gán sự kiện để truyền giá trị từ form employeeList sang employeeDetail
     * Author: NSDThinh - 12/09/2022
     */
    showDialongEmployeeDetail(isShowDialog) {
      this.isShowDialog = isShowDialog;
    },

    /**
     * @description: Hàm này dùng để định dạng tiền lương (format)
     * @param {Object} value - gán sự kiện để format theo dạng có dấu " . " để phân cách tiền
     * Author: NSDThinh - 15//09/2022
     */
    inputSalaryFomat() {
      // lấy ra value của input mức lương
      let value = event.currentTarget.value;
      let currentText = event.currentTarget.data;
      if (currentText == ".") {
        return;
      }
      // thực hiện định dạng lại dữ liệu:
      if (value) {
        value = value.replaceAll(",", "");
        // chuyển dữ liệu sang dạng số
        value = Number(value);
        if (isNaN(value)) {
          return;
        }
        value = new Intl.NumberFormat("en-VI").format(value);
        event.currentTarget.value = value;
      }
      // sét lại giá trị hiển thị trên input
      console.log(value);
    },

    /**
     * @description: Hàm này dùng để Format Date (ngày sinh)
     * @param {Object} dob - gán sự kiện để format theo dạng ngày tháng năm
     * Author: NSDThinh - 12//09/2022
     */
    formatDate: function (dob) {
      if (dob) {
        dob = new Date(dob);
        // Lấy ra ngày:
        let date = dob.getDate();
        date = date < 10 ? `0${date}` : date;
        // Lấy ra tháng:
        let month = dob.getMonth() + 1;
        month = month < 10 ? `0${month}` : month;
        // Lấy ra năm:
        let year = dob.getFullYear();
        dob = `${date}/${month}/${year}`;
      } else {
        dob = "";
      }
      return dob;
    },

    /**
     * @description: Hàm này dùng để Format Chức danh
     * @param {Object} dob - gán sự kiện để format theo dạng theo mã ID của Chức danh
     * Author: NSDThinh - 12//09/2022
     */
    formatChucDanh(PositionId) {
      switch (PositionId) {
        case "30d41e52-5e66-72bc-6c1c-b47866e0b131":
          return "Trưởng phòng";
        case "548dce5f-5f29-4617-725d-e2ec561b0f41":
          return "Quản trị hệ thống";
        case "589edf01-198a-4ff5-958e-fb52fd75a1d4":
          return "Nhân viên";
        case "5bd71cda-209f-2ade-54d1-35c781481818":
          return "Trưởng nhóm";
      }
    },

    /**
     * @description: Hàm này dùng để Format đơn vị
     * @param {Object} dob - gán sự kiện để format theo dạng theo mã ID của đơn vị
     * Author: NSDThinh - 12//09/2022
     */
    formatDonVi(DepartmentId) {
      switch (DepartmentId) {
        case "3f8e6896-4c7d-15f5-a018-75d8bd200d7c":
          return "Phòng Công Nghệ Thông Tin";
        case "45ac3d26-18f2-18a9-3031-644313fbb055":
          return "Phòng Hành Chính";
        case "78aafe4a-67a7-2076-3bf3-eb0223d0a4f7":
          return "Phòng Nhân Sự";
        case "7c4f14d8-66fb-14ae-198f-6354f958f4c0":
          return "Phòng Kế Toán";
      }
    },

    /**
     * @description: Hàm này dùng để hiển thị nổi bật nhân viên
     * @param {Object} EmployeeId - Lấy dữ liệu ID của nhân nhân viên thông qua api
     * Author: NSDThinh - 12//09/2022
     */
    activeClickTable(EmployeeId) {
      this.activeClick = EmployeeId;
    },

    /**
     * @description: Format Gender (giới tính)
     * @param {Object} gender - Lấy dữ liệu api trả về thông qua dạng enum
     * Author: NSDThinh - 12//09/2022
     */
    formatGender(gender) {
      switch (gender) {
        case 0:
          return "Nam";
        case 1:
          return "Nữ";
        default:
          return "Khác";
      }
    },
    /**
     * @description: Hàm này dùng để lấy giữ liệu từ API và hiển thị danh sách lên màn hình
     * Author: NSDThinh - 12/09/2022
     */

    async getAllEmployee() {
      let me = this;
      try {
        me.isLoading = true;
        const res = await axios.get("https://cukcuk.manhnv.net/api/v1/Employees");
        me.employees = res.data;
        me.isLoading = false;
      } catch (error) {
        console.log(res);
        me.isLoading = true;
      }
    },
    /**
     * @description: Hàm này dùng để lấy giữ liệu từ API và hiển thị danh filter sách lên màn hình
     */
    async getAllEmployeeFilter() {
      let me = this;
      try {
        me.isLoading = true;
        const res = await axios.get(
          "https://cukcuk.manhnv.net/api/v1/Employees/filter",
          me.pagination
        );
        me.employeeListFilter = res.data;
        me.isLoading = false;
      } catch (error) {
        console.log(error);
        me.isLoading = true;
      }
    },
  },
  watch: {},
  created() {
    let me = this;

    /**
     * @description: // Lấy danh sách nhân viên
     */
    me.getAllEmployeeFilter();
    /**
     * @description: Hàm này dùng để lấy giữ liệu từ API và hiển thị danh sách lên màn hình
     * Author: NSDThinh - 12//09/2022
     */
    me.getAllEmployee();
  },
};
</script>

<style lang="scss" scoped>
@import "../../src/style/employee/employee";
</style>
