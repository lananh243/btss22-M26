<template>
  <div class="header">
    <div>
      <button @click="showModal = true">Thêm sản phẩm</button>
      <table>
        <thead>
          <tr>
            <th>#</th>
            <th>Tên sản phẩm</th>
            <th>Hình ảnh</th>
            <th>Giá</th>
            <th>Số lượng (kg)</th>
            <th>Ngày thêm</th>
            <th>Chức năng</th>
          </tr>
        </thead>
        <tbody v-for="product in products" :key="product.id">
          <tr>
            <td>{{ product.id }}</td>
            <td>{{ product.name }}</td>
            <td><img :src="product.image" alt="" /></td>
            <td>{{ product.price }}</td>
            <td>{{ product.quantity }}</td>
            <td>{{ product.extraDay }}</td>
            <td>
              <div class="include">
                <button @click="updateProductById(product.id)">Sửa</button>
                <button @click="removeProductById(product.id)">Xóa</button>
                <button @click="getProductById(product.id)">Get detail</button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- Modal for adding new product -->
      <div v-if="showModal" class="modal-overlay">
        <div class="modal-content">
          <div class="modal-header">
            <h3>Thêm sản phẩm mới</h3>
            <span class="close-icon" @click="showModal = false">X</span>
          </div>
          <br />
          <div class="modal-body">
            <div class="form-group">
              <label class="form-label">Tên sản phẩm</label>
              <br /><br />
              <input
                v-model="productInfo.name"
                class="form-control"
                type="text"
                placeholder="Nhập tên sản phẩm"
              />
              <p v-if="errors.name" class="error">{{ errors.name }}</p>
            </div>
            <br />
            <div class="form-group">
              <label class="form-label">Giá</label>
              <br /><br />
              <input
                v-model="productInfo.price"
                class="form-control"
                type="number"
                placeholder="Nhập giá sản phẩm"
              />
              <p v-if="errors.price" class="error">{{ errors.price }}</p>
            </div>
            <br />
            <div class="form-group">
              <label class="form-label">Số lượng (kg)</label>
              <br /><br />
              <input
                v-model="productInfo.quantity"
                class="form-control"
                type="number"
                placeholder="Nhập số lượng"
              />
              <p v-if="errors.quantity" class="error">{{ errors.quantity }}</p>
            </div>
            <br />
            <div class="form-group">
              <label class="form-label">Hình ảnh (URL)</label>
              <br /><br />
              <input
                v-model="productInfo.image"
                class="form-control"
                type="text"
                placeholder="Nhập URL hình ảnh"
              />
              <p v-if="errors.image" class="error">{{ errors.image }}</p>
            </div>
            <br />
            <div class="form-group">
              <label class="form-label">Ngày tạo</label>
              <br /><br />
              <input
                v-model="productInfo.extraDay"
                class="form-control"
                type="text"
                placeholder="Nhập ngày tạo"
              />
              <p v-if="errors.extraDay" class="error">{{ errors.extraDay }}</p>
            </div>
          </div>
          <br /><br />
          <div class="modal-footer">
            <button @click="showModal = false">Hủy</button>
            <button @click="saveProduct">Lưu</button>
          </div>
        </div>
      </div>
    </div>
    <div>
      <form @submit.prevent="handleSubmit">
        <div class="modal-body">
          <h1>Sửa sản phẩm</h1>
          <br />
          <div class="form-group">
            <label class="form-label">Tên sản phẩm</label>
            <br /><br />
            <input
              v-model="productInfo.name"
              class="form-control"
              type="text"
              placeholder="Nhập tên sản phẩm"
            />
            <p v-if="errors.name" class="error">{{ errors.name }}</p>
          </div>
          <br />
          <div class="form-group">
            <label class="form-label">Giá</label>
            <br /><br />
            <input
              v-model="productInfo.price"
              class="form-control"
              type="number"
              placeholder="Nhập giá sản phẩm"
            />
            <p v-if="errors.price" class="error">{{ errors.price }}</p>
          </div>
          <br />
          <div class="form-group">
            <label class="form-label">Số lượng (kg)</label>
            <br /><br />
            <input
              v-model="productInfo.quantity"
              class="form-control"
              type="number"
              placeholder="Nhập số lượng"
            />
            <p v-if="errors.quantity" class="error">{{ errors.quantity }}</p>
          </div>
          <br />
          <div class="form-group">
            <label class="form-label">Hình ảnh (URL)</label>
            <br /><br />
            <input
              v-model="productInfo.image"
              class="form-control"
              type="text"
              placeholder="Nhập URL hình ảnh"
            />
            <p v-if="errors.image" class="error">{{ errors.image }}</p>
          </div>
          <br />
          <div class="form-group">
            <label class="form-label">Ngày tạo</label>
            <br /><br />
            <input
              v-model="productInfo.extraDay"
              class="form-control"
              type="text"
              placeholder="Nhập ngày tạo"
            />
            <p v-if="errors.extraDay" class="error">{{ errors.extraDay }}</p>
          </div>
        </div>
        <br />
        <button type="submit" class="bit">Lưu</button>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from "vue";

// State variables
const products = ref([]);
const showModal = ref(false);

// Error messages
const errors = reactive({
  name: "",
  price: "",
  quantity: "",
  image: "",
  extraDay: "",
});

// Product information
const productInfo = reactive({
  name: "",
  price: 0,
  quantity: 0,
  image: "",
  extraDay: "",
});

// Fetch all products on page load
const getAllProduct = async () => {
  try {
    const response = await fetch("http://localhost:8080/products");
    products.value = await response.json();
  } catch (error) {
    console.error(error);
  }
};

onMounted(() => {
  getAllProduct();
});

const saveProduct = async () => {
  // Reset error messages
  errors.name = "";
  errors.price = "";
  errors.quantity = "";
  errors.image = "";
  errors.extraDay = "";

  // Validate inputs
  let valid = true;
  if (!productInfo.name) {
    errors.name = "Tên sản phẩm là bắt buộc!";
    valid = false;
  }
  if (!productInfo.price) {
    errors.price = "Giá sản phẩm là bắt buộc!";
    valid = false;
  }
  if (!productInfo.quantity) {
    errors.quantity = "Số lượng là bắt buộc!";
    valid = false;
  }
  if (!productInfo.image) {
    errors.image = "URL hình ảnh là bắt buộc!";
    valid = false;
  }
  if (!productInfo.extraDay) {
    errors.extraDay = "Ngày tạo không đc để trống";
    valid = false;
  }
  // Check for duplicate product name
  const isDuplicate = products.value.some(
    (product) => product.name.toLowerCase() === productInfo.name.toLowerCase()
  );
  if (isDuplicate) {
    errors.name = "Tên sản phẩm đã tồn tại!";
    valid = false;
  }

  // If validation fails, return
  if (!valid) return;

  // API call to save the product
  try {
    const response = await fetch("http://localhost:8080/products", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(productInfo),
    });

    if (!response.ok) {
      throw new Error("Lưu sản phẩm thất bại!");
    }

    const newProduct = await response.json();
    products.value.push(newProduct);

    // Close modal and reset form
    showModal.value = false;
    productInfo.name = "";
    productInfo.price = 0;
    productInfo.quantity = 0;
    productInfo.image = "";
    productInfo.extraDay = "";
  } catch (error) {
    console.error(error);
    errors.name = "Lưu sản phẩm thất bại!";
  }
};

const removeProductById = async (id) => {
  // Hiển thị hộp thoại xác nhận trước khi xóa
  const confirmed = window.confirm("Bạn có chắc chắn muốn xóa sản phẩm này?");

  // Nếu người dùng xác nhận, thực hiện xóa
  if (confirmed) {
    try {
      const response = await fetch(`http://localhost:8080/products/${id}`, {
        method: "DELETE",
      });
      if (response.ok) {
        // Cập nhật danh sách sản phẩm sau khi xóa
        products.value = products.value.filter((product) => product.id !== id);
      } else {
        // Xử lý khi có lỗi từ server (nếu cần)
        console.error("Không thể xóa sản phẩm:", response.statusText);
      }
    } catch (error) {
      console.error("Lỗi khi xóa sản phẩm:", error);
    }
  }
};

// Fetch product details
const getProductById = async (id) => {
  try {
    const response = await fetch(`http://localhost:8080/products/${id}`);
    if (!response.ok) {
      throw new Error("Không tìm thấy sản phẩm");
    }
    const data = await response.json();
    console.log("Thông tin chi tiết sản phẩm:", data);
  } catch (error) {
    console.error(error.message);
  }
};
const baseId = ref(null);
const handleSubmit = async (id) => {
  if (baseId) {
    await fetch(`http://localhost:8080/products/${baseId.value}`, {
      method: "PUT",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(productInfo),
    });

    getAllProduct();
  }
  productInfo.name = "";
  productInfo.price = "";
  productInfo.quantity = "";
  productInfo.image = "";
  productInfo.extraDay = "";
};

const updateProductById = async (id) => {
  baseId.value = id;

  // Tìm kiếm sản phẩm theo id
  const response = await fetch(`http://localhost:8080/products/${id}`, {
    method: "GET",
  });

  const data = await response.json();

  if (data) {
    productInfo.name = data.name;
    productInfo.price = data.price;
    productInfo.quantity = data.quantity;
    productInfo.image = data.image;
    productInfo.extraDay = data.extraDay;
  }
};
</script>

<style scoped>
.bit {
  width: 60px;
  height: 25px;
  background-color: blue;
  color: #fff;
  border: none;
}
img {
  width: 200px;
}
.header {
  width: 1300px;
  margin: auto;
  margin-top: 100px;
  display: flex;
  justify-content: space-between;
}
table {
  border-collapse: collapse;
  width: 850px;
  margin-top: 50px;
}
thead {
  border-bottom: 2px solid #ddd;
}
tbody tr {
  border-bottom: 1px solid #ddd;
  text-align: center;
}
.include {
  display: flex;
  justify-content: center;
  gap: 10px;
}
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}
.modal-header {
  width: 320px;
  display: flex;
  justify-content: space-between;
}
.modal-content {
  background: white;
  padding: 20px;
  border-radius: 8px;
  width: 380px;
}
.modal-footer {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}
.error {
  color: red;
  font-size: 12px;
}
input {
  width: 320px;
  height: 30px;
}
</style>
