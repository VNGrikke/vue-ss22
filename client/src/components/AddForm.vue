<template>
  <div class="addForm">
    <h3 v-if="isShow">Thêm mới sản phẩm</h3>
    <h3 v-else>Sửa sản phẩm</h3>
    <input type="text" v-model="newProduct.name" placeholder="Tên sản phẩm" />
    <input
      type="number"
      v-model="newProduct.price"
      placeholder="Giá sản phẩm"
    />
    <input
      type="text"
      v-model="newProduct.image"
      placeholder="Hình ảnh sản phẩm"
    />
    <input
      type="date"
      v-model="newProduct.createdDate"
      placeholder="Ngày thêm sản phẩm"
    />

    <input type="number" v-model="newProduct.quantity" placeholder="Số lượng" />
    <p class="btn">
      <button v-if="isShow" @click="addProduct">Xác nhận</button>
      <button v-else @click="updateProduct">Sửa sản phẩm</button>
      <button @click="cancelForm" class="cancel-btn">Hủy</button>
    </p>
  </div>
</template>

<script setup>
import { watch } from "vue";

const props = defineProps({
  newProduct: Object,
  isShow: Boolean,
});

const emit = defineEmits(["add", "update", "reset", "close"]);

const addProduct = () => {
  emit("add", { ...props.newProduct });
};

const updateProduct = () => {
  emit("update", { ...props.newProduct });
};

const cancelForm = () => {
  emit("reset");
  emit("close");
};

watch(
  () => props.newProduct.id,
  (newId) => {
    if (!newId) {
      emit("reset");
    }
  }
);
</script>

<style scoped>
.addForm {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 90%;
  max-width: 400px;
  padding: 20px;
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  z-index: 1000;
}

h3 {
  text-align: center;
  color: #4caf50;
  margin-bottom: 20px;
}

input {
  width: 100%;
  padding: 12px;
  margin: 10px 0;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 16px;
}

input:focus {
  border-color: #4caf50;
  outline: none;
  box-shadow: 0 0 5px rgba(76, 175, 80, 0.3);
}

p.btn {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

button {
  flex: 1;
  padding: 10px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
  margin-right: 10px;
}

button:last-child {
  margin-right: 0;
}

button:hover {
  background-color: #45a049;
}

button:focus {
  outline: none;
  box-shadow: 0 0 5px rgba(76, 175, 80, 0.5);
}

.cancel-btn {
  background-color: #f44336;
}

.cancel-btn:hover {
  background-color: #e53935;
}
</style>
