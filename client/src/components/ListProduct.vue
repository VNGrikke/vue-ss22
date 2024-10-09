<template>
  <div>
    <h1>Danh sách sản phẩm</h1>
    <div>
      <button @click="showAddForm">Thêm sản phẩm</button>
      <select @change="sortProducts">
        <option value="asc">Gần nhất</option>
        <option value="desc">Xa nhất</option>
      </select>
    </div>

    <AddForm
      v-show="isFormVisible"
      :newProduct="newProduct"
      :isShow="isAddMode"
      @add="handleAddOrUpdate"
      @update="handleAddOrUpdate"
      @reset="ResetNewProduct"
      @close="closeForm"
    />
    <table>
      <thead>
        <tr>
          <th width="10%">ID</th>
          <th width="25%">Tên</th>
          <th width="15%">Giá</th>
          <th width="10%">Hình ảnh</th>
          <th width="10%">Số lượng</th>
          <th width="20%">Ngày thêm</th>
          <th width="10%">Thao tác</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(product, index) in products" :key="product.id">
          <td>{{ index + 1 }}</td>
          <td>{{ product.name }}</td>
          <td>{{ product.price }}</td>
          <td>
            <img :src="product.image" alt="Product Image" class="product-image" />
          </td>
          <td>{{ product.quantity }}</td>
          <td>{{ formatDate(product.createdDate) }}</td>
          <td>
            <button @click="GetProductDetails(product.id)">Chi tiết</button>
            <button @click="DeleteProduct(product.id)">Xóa</button>
            <button @click="EditProduct(product.id)">Sửa</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import AddForm from "@/components/AddForm.vue";

const products = ref([]);
const isFormVisible = ref(false);
const isAddMode = ref(true);
const newProduct = ref({
  id: null,
  name: "",
  price: 0,
  image: "",
  quantity: 0,
  createdDate: "",
});

const GetAllProducts = async () => {
  try {
    const response = await fetch("http://localhost:3000/products");
    products.value = await response.json();
  } catch (error) {
    console.error("Lỗi khi lấy sản phẩm:", error);
  }
};

watch(GetAllProducts);

const GetProductDetails = async (id) => {
  try {
    const response = await fetch(`http://localhost:3000/products/${id}`);
    console.log("Chi tiết sản phẩm:", await response.json());
  } catch (error) {
    console.error("Không tìm thấy sản phẩm:", error);
  }
};

const DeleteProduct = async (id) => {
  try {
    if (confirm("Bạn có muốn xóa sản phẩm này không?")) {
      await fetch(`http://localhost:3000/products/${id}`, { method: "DELETE" });
      products.value = products.value.filter((product) => product.id !== id);
      console.log("Xóa sản phẩm thành công!");
    }
  } catch (error) {
    console.error("Lỗi xóa sản phẩm:", error);
  }
};

const handleAddOrUpdate = async (product) => {
  try {
    const method = isAddMode.value ? "POST" : "PUT";
    const url = isAddMode.value
      ? "http://localhost:3000/products"
      : `http://localhost:3000/products/${product.id}`;

    const response = await fetch(url, {
      method,
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(product),
    });

    const savedProduct = await response.json();
    if (isAddMode.value) {
      products.value.push(savedProduct);
    } else {
      const index = products.value.findIndex((p) => p.id === product.id);
      products.value[index] = savedProduct;
    }

    closeForm();
    console.log(
      `${isAddMode.value ? "Thêm" : "Cập nhật"} sản phẩm thành công!`
    );
  } catch (error) {
    console.error(
      `Lỗi ${isAddMode.value ? "thêm" : "cập nhật"} sản phẩm:`,
      error
    );
  }
};

const EditProduct = (id) => {
  isAddMode.value = false;
  isFormVisible.value = true;
  newProduct.value = { ...products.value.find((product) => product.id === id) };
};

const showAddForm = () => {
  isAddMode.value = true;
  isFormVisible.value = true;
  ResetNewProduct();
};

const ResetNewProduct = () => {
  newProduct.value = {
    id: null,
    name: "",
    price: 0,
    image: "",
    quantity: 0,
    createdDate: "",
  };
};

const closeForm = () => {
  isFormVisible.value = false;
};

const formatDate = (date) => {
  if (!date) return "";
  const parsedDate = new Date(date);
  return `${parsedDate.getDate().toString().padStart(2, "0")}-${(
    parsedDate.getMonth() + 1
  )
    .toString()
    .padStart(2, "0")}-${parsedDate.getFullYear()}`;
};

const sortProducts = (event) => {
  const sortOrder = event.target.value; 
  products.value.sort((a, b) => {
    const dateA = new Date(a.createdDate);
    const dateB = new Date(b.createdDate);
    return sortOrder === "asc" ? dateA - dateB : dateB - dateA;
  });
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  margin: 0;
  padding: 20px;
}

h1 {
  text-align: center;
  color: #333;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

thead {
  background-color: #4caf50;
  color: white;
}

th,
td {
  padding: 15px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

tbody tr:hover {
  background-color: #f1f1f1;
}

.product-image {
  width: 50px;
  height: auto;
  border-radius: 4px;
}
</style>
