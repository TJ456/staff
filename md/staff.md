

### 🔐 **Authentication Endpoints**


#### 1. **POST /api/auth/register** - Staff Registration


**Route File**: `routes/auth.js`  
**Controller**: `authController.register`


**OLD Request Body:**


```json
{
  "email": "staff@example.com",
  "name": "Staff Name",
  "password": "password123"
}
```


**NEW Request Body:**


```json
{
  "email": "staff@example.com",
  "name": "Staff Name",
  "password": "password123",
  "shopId": 1,
  "role": "SALES_STAFF"
}
```


**NEW Required Fields:**


- `shopId` (integer) - ID of the shop the staff belongs to
- `role` (string) - Must be one of: `SALES_STAFF`, `CASHIER`, `INVENTORY_MANAGER`, `OPTOMETRIST`


---


### 👥 **Patient Endpoints**


#### 2. **POST /api/patient/** - Create Patient


**Route File**: `routes/patient.js`  
**Controller**: `patientController.createPatient`


**OLD Request Body:**


```json
{
  "name": "John Doe",
  "age": 30,
  "gender": "Male",
  "phone": "+1234567890",
  "address": "123 Main St",
  "medicalHistory": "None"
}
```


**NEW Request Body:**


```json
{
  "name": "John Doe",
  "age": 30,
  "gender": "Male",
  "phone": "+1234567890",
  "address": "123 Main St",
  "medicalHistory": "None",
  "shopId": 1
}
```


**NEW Required Fields:**


- `shopId` (integer) - ID of the shop where patient is registered


---
=========================

### 🛒 **Customer Endpoints**


#### 3. **POST /api/customer/** - Create Customer


**Route File**: `routes/customer.js`  
**Controller**: `customerController.createCustomer`


**OLD Request Body:**


```json
{
  "name": "Jane Smith",
  "phone": "+0987654321",
  "address": "456 Oak Avenue"
}
```


**NEW Request Body:**


```json
{
  "name": "Jane Smith",
  "phone": "+0987654321",
  "address": "456 Oak Avenue",
  "shopId": 1
}
```


**NEW Required Fields:**


- `shopId` (integer) - ID of the shop where customer is registered


#### 4. **POST /api/customer/invoice** - Create Customer Invoice


**Route File**: `routes/customer.js`  
**Controller**: `customerController.createCustomerAndInvoice`


**OLD Request Body:**


```json
{
  "customer": {
    "name": "Walk-in Customer",
    "phone": "+1111111111",
    "address": "789 Pine Street"
  },
  "items": [
    {
      "productId": 1,
      "quantity": 2,
      "unitPrice": 150.0
    }
  ],
  "subtotal": 300.0,
  "totalAmount": 354.0,
  "cgst": 27.0,
  "sgst": 27.0
}
```


**NEW Request Body:**


```json
{
  "customer": {
    "name": "Walk-in Customer",
    "phone": "+1111111111",
    "address": "789 Pine Street",
    "shopId": 1
  },
  "items": [
    {
      "productId": 1,
      "quantity": 2,
      "unitPrice": 150.0
    }
  ],
  "subtotal": 300.0,
  "totalAmount": 354.0,
  "cgst": 27.0,
  "sgst": 27.0
}
```


**NEW Required Fields:**


- `customer.shopId` (integer) - ID of the shop for the customer


---


## Endpoints NOT Requiring Updates


The following endpoints do **NOT** need request body changes because they either:


- Don't create/update Staff, Patient, or Customer models
- Only read data (GET endpoints)
- Work with other models that weren't changed


### ✅ **No Changes Needed:**


**Invoice Endpoints:**


- `POST /api/invoice/` - _(Creates invoice, doesn't modify Staff/Patient/Customer)_
- `GET /api/invoice/`
- `GET /api/invoice/:id`
- `PATCH /api/invoice/:id/status`
- `POST /api/invoice/:id/payment`
- `DELETE /api/invoice/:id`
- `GET /api/invoice/:id/pdf`


**Prescription Endpoints:**


- `POST /api/prescription/` - _(Creates prescription for existing patient)_
- `GET /api/prescription/`
- `GET /api/prescription/:id`
- `GET /api/prescription/:id/pdf`
- `GET /api/prescription/:id/thermal`


*


===========================================================
# 📋 **INVENTORY MANAGEMENT API DOCUMENTATION**


**Base URL**: `http://localhost:8080/api/inventory`  
**Authentication**: All endpoints require JWT authentication via `Authorization: Bearer <token>` header


---


## 🔸 **1. Update Stock by Barcode**


### **Endpoint**: `POST /api/inventory/stock-by-barcode`


### **Auth**: Required


### **Description**: Updates inventory stock and optionally price using product barcode


#### **Request Body**:


```json
{
  "barcode": "RB3025001",
  "quantity": 10,
  "price": 150.0,
  "shopId": 1
}
```


#### **Request Fields**:


- `barcode` (string, required): Product barcode
- `quantity` (number, required): Quantity to add to inventory
- `price` (number, optional): New selling price for the product
- `shopId` (number, required): Shop ID for inventory update


#### **Success Response** (200):


```json
{
  "success": true,
  "message": "Stock updated successfully via barcode scan",
  "inventory": {
    "id": 1,
    "productId": 5,
    "quantity": 25,
    "lastRestockedAt": "2025-09-10T10:30:00.000Z",
    "lastUpdated": "2025-09-10T10:30:00.000Z"
  },
  "productDetails": {
    "id": 5,
    "sku": "RAY-AV-001",
    "barcode": "RB3025001",
    "name": "Ray-Ban Aviator Classic",
    "description": "Classic aviator sunglasses with metal frame",
    "model": "RB3025",
    "size": "L",
    "color": "Gold",
    "material": "Metal",
    "price": 150.0,
    "eyewearType": "SUNGLASSES",
    "frameType": "AVIATOR",
    "company": {
      "id": 1,
      "name": "Ray-Ban"
    }
  }
}
```


#### **Error Responses**:


```json
// 400 - Missing fields
{
  "error": "Barcode and quantity are required."
}


// 400 - Invalid shopId
{
  "error": "shopId must be a valid number"
}


// 404 - Product not found
{
  "error": "Product with barcode RB3025001 not found."
}
```


---


## 🔸 **2. Add Product**


### **Endpoint**: `POST /api/inventory/product`


### **Auth**: Required


### **Description**: Creates a new product in the system


#### **Request Body**:


```json
{
  "name": "Ray-Ban Aviator Classic",
  "description": "Classic aviator sunglasses with metal frame",
  "barcode": "RB3025001",
  "sku": "RAY-AV-001",
  "basePrice": 150.0,
  "eyewearType": "SUNGLASSES",
  "frameType": "AVIATOR",
  "companyId": 1,
  "material": "Metal",
  "color": "Gold",
  "size": "L",
  "model": "RB3025"
}
```


#### **Request Fields**:


- `name` (string, required): Product name
- `description` (string, optional): Product description
- `barcode` (string, optional): Product barcode (must be unique)
- `sku` (string, optional): Stock Keeping Unit (must be unique)
- `basePrice` (number, required): Base price from retailer
- `eyewearType` (string, required): Type of eyewear - "GLASSES", "SUNGLASSES", "LENSES"
- `frameType` (string, required for GLASSES/SUNGLASSES): Frame type - "AVIATOR", "ROUND", "SQUARE", "WAYFARER", etc.
- `companyId` (number, required): Company/Brand ID
- `material` (string, optional): Frame material
- `color` (string, optional): Product color
- `size` (string, optional): Product size
- `model` (string, optional): Product model


#### **Success Response** (201):


```json
{
  "id": 5,
  "name": "Ray-Ban Aviator Classic",
  "description": "Classic aviator sunglasses with metal frame",
  "basePrice": 150.0,
  "barcode": "RB3025001",
  "sku": "RAY-AV-001",
  "eyewearType": "SUNGLASSES",
  "frameType": "AVIATOR",
  "companyId": 1,
  "material": "Metal",
  "color": "Gold",
  "size": "L",
  "model": "RB3025",
  "createdAt": "2025-09-10T10:30:00.000Z",
  "updatedAt": "2025-09-10T10:30:00.000Z",
  "company": {
    "id": 1,
    "name": "Ray-Ban",
    "description": "Premium eyewear manufacturer"
  }
}
```


#### **Error Responses**:


```json
// 400 - Missing required fields
{
  "error": "Name, basePrice, eyewearType, and companyId are required fields."
}


// 400 - Invalid eyewear type
{
  "error": "Invalid eyewearType. Must be GLASSES, SUNGLASSES, or LENSES."
}


// 400 - Duplicate barcode
{
  "error": "Barcode already exists."
}
```


---


## 🔸 **3. Stock In (Traditional or Barcode)**


### **Endpoint**: `POST /api/inventory/stock-in`


### **Auth**: Required


### **Description**: Adds stock to inventory using either product ID or barcode


#### **Request Body (Option A - By Product ID)**:


```json
{
  "productId": 15,
  "quantity": 10
}
```


#### **Request Body (Option B - By Barcode)**:


```json
{
  "barcode": "RAY0015678901",
  "quantity": 10
}
```


#### **Request Fields**:


- `productId` (number, required if no barcode): Product ID
- `barcode` (string, required if no productId): Product barcode
- `quantity` (number, required): Quantity to add


#### **Success Response** (200):


```json
{
  "success": true,
  "message": "Stock added successfully",
  "operation": "STOCK_IN",
  "productDetails": {
    "id": 15,
    "name": "Product Name",
    "barcode": "RAY0015678901",
    "company": "Ray-Ban"
  },
  "inventoryUpdate": {
    "previousQuantity": 20,
    "addedQuantity": 10,
    "newQuantity": 30,
    "lastUpdated": "2025-09-10T10:30:00.000Z"
  }
}
```


#### **Error Responses**:


```json
// 400 - Missing fields
{
  "error": "Either productId or barcode is required.",
  "examples": {
    "traditional": { "productId": 15, "quantity": 10 },
    "barcodeScan": { "barcode": "RAY0015678901", "quantity": 10 }
  }
}
```


---


## 🔸 **4. Stock Out (Traditional or Barcode)**


### **Endpoint**: `POST /api/inventory/stock-out`


### **Auth**: Required


### **Description**: Removes stock from inventory using either product ID or barcode


#### **Request Body (Option A - By Product ID)**:


```json
{
  "productId": 15,
  "quantity": 5
}
```


#### **Request Body (Option B - By Barcode)**:


```json
{
  "barcode": "RAY0015678901",
  "quantity": 5
}
```


#### **Request Fields**:


- `productId` (number, required if no barcode): Product ID
- `barcode` (string, required if no productId): Product barcode
- `quantity` (number, required): Quantity to remove


#### **Success Response** (200):


```json
{
  "success": true,
  "message": "Stock removed successfully",
  "operation": "STOCK_OUT",
  "productDetails": {
    "id": 15,
    "name": "Product Name",
    "barcode": "RAY0015678901",
    "company": "Ray-Ban"
  },
  "inventoryUpdate": {
    "previousQuantity": 30,
    "removedQuantity": 5,
    "newQuantity": 25,
    "lastUpdated": "2025-09-10T10:30:00.000Z"
  }
}
```


#### **Error Responses**:


```json
// 400 - Insufficient stock
{
  "error": "Insufficient stock. Available: 3, Requested: 5",
  "availableStock": 3
}
```


---


## 🔸 **5. Stock Out by Barcode**


### **Endpoint**: `POST /api/inventory/stock-out-by-barcode`


### **Auth**: Required


### **Description**: Removes stock using barcode (dedicated barcode endpoint)


#### **Request Body**:


```json
{
  "barcode": "RB3025001",
  "quantity": 2
}
```


#### **Request Fields**:


- `barcode` (string, required): Product barcode
- `quantity` (number, required): Quantity to remove


#### **Success Response** (200):


```json
{
  "id": 1,
  "productId": 5,
  "quantity": 18,
  "product": {
    "id": 5,
    "name": "Ray-Ban Aviator Classic",
    "barcode": "RB3025001",
    "company": {
      "name": "Ray-Ban"
    }
  },
  "stockOutDetails": {
    "productName": "Ray-Ban Aviator Classic",
    "removedQuantity": 2,
    "remainingStock": 18,
    "timestamp": "2025-09-10T10:30:00.000Z"
  }
}
```


---


## 🔸 **6. Get Product by Barcode**


### **Endpoint**: `GET /api/inventory/product/barcode/:barcode`


### **Auth**: Required


### **Description**: Retrieves product details by barcode


### **Example**: `GET /api/inventory/product/barcode/RB3025001`


#### **Request Body**: None (GET request)


#### **Success Response** (200):


```json
{
  "id": 5,
  "name": "Ray-Ban Aviator Classic",
  "description": "Classic aviator sunglasses with metal frame",
  "basePrice": 150.0,
  "barcode": "RB3025001",
  "sku": "RAY-AV-001",
  "eyewearType": "SUNGLASSES",
  "frameType": "AVIATOR",
  "company": {
    "id": 1,
    "name": "Ray-Ban",
    "description": "Premium eyewear manufacturer"
  },
  "material": "Metal",
  "color": "Gold",
  "size": "L",
  "model": "RB3025",
  "inventory": {
    "quantity": 20,
    "lastUpdated": "2025-09-10T10:30:00.000Z",
    "stockStatus": "In Stock"
  },
  "createdAt": "2025-09-10T10:30:00.000Z",
  "updatedAt": "2025-09-10T10:30:00.000Z",
  "quickInfo": "Ray-Ban SUNGLASSES - Ray-Ban Aviator Classic ($150)"
}
```


#### **Error Responses**:


```json
// 400 - Invalid barcode
{
  "error": "Barcode is required and must be a valid string."
}


// 404 - Product not found
{
  "error": "Product with barcode RB3025001 not found.",
  "suggestion": "Check if the barcode is correct or if the product needs to be added to the system."
}
```


---


## 🔸 **7. Update Product**


### **Endpoint**: `PUT /api/inventory/product/:productId`


### **Auth**: Required


### **Description**: Updates existing product details


#### **Request Body** (all fields optional):


```json
{
  "name": "Updated Product Name",
  "description": "Updated description",
  "barcode": "NEW123456",
  "price": 200.0,
  "eyewearType": "GLASSES",
  "frameType": "ROUND",
  "companyId": 2,
  "material": "Plastic",
  "color": "Black",
  "size": "M",
  "model": "Updated Model"
}
```


#### **Request Fields** (all optional):


- `name` (string): Product name
- `description` (string): Product description
- `barcode` (string): Product barcode
- `price` (number): Product price
- `eyewearType` (string): Eyewear type
- `frameType` (string): Frame type
- `companyId` (number): Company ID
- `material` (string): Material
- `color` (string): Color
- `size` (string): Size
- `model` (string): Model


#### **Success Response** (200):


```json
{
  "id": 5,
  "name": "Updated Product Name",
  "description": "Updated description",
  "basePrice": 200.0,
  "barcode": "NEW123456",
  "eyewearType": "GLASSES",
  "frameType": "ROUND",
  "companyId": 2,
  "material": "Plastic",
  "color": "Black",
  "size": "M",
  "model": "Updated Model",
  "updatedAt": "2025-09-10T10:35:00.000Z",
  "company": {
    "id": 2,
    "name": "Oakley"
  }
}
```


---


## 🔸 **8. Get Inventory**


### **Endpoint**: `GET /api/inventory/`


### **Auth**: Required


### **Description**: Retrieves all inventory for the authenticated user's shop


### **Query Parameters** (optional):


- `eyewearType`: Filter by eyewear type
- `companyId`: Filter by company
- `frameType`: Filter by frame type


#### **Request Body**: None (GET request)


#### **Success Response** (200):


```json
{
  "inventory": [
    {
      "id": 1,
      "quantity": 20,
      "lastRestockedAt": "2025-09-10T10:30:00.000Z",
      "product": {
        "id": 5,
        "name": "Ray-Ban Aviator Classic",
        "barcode": "RB3025001",
        "basePrice": 150.0,
        "eyewearType": "SUNGLASSES",
        "frameType": "AVIATOR",
        "company": {
          "id": 1,
          "name": "Ray-Ban"
        }
      }
    }
  ],
  "summary": {
    "totalProducts": 25,
    "totalValue": 3750.0,
    "lowStockItems": 3,
    "outOfStockItems": 1,
    "byCategory": {
      "SUNGLASSES": 15,
      "GLASSES": 8,
      "LENSES": 2
    }
  }
}
```


---


## 🔸 **9. Add Company**


### **Endpoint**: `POST /api/inventory/company`


### **Auth**: Required


### **Description**: Creates a new company/brand


#### **Request Body**:


```json
{
  "name": "Ray-Ban",
  "description": "Premium eyewear manufacturer"
}
```


#### **Request Fields**:


- `name` (string, required): Company name
- `description` (string, optional): Company description


#### **Success Response** (201):


```json
{
  "id": 1,
  "name": "Ray-Ban",
  "description": "Premium eyewear manufacturer",
  "createdAt": "2025-09-10T10:30:00.000Z",
  "updatedAt": "2025-09-10T10:30:00.000Z"
}
```


#### **Error Responses**:


```json
// 400 - Missing name
{
  "error": "Company name is required."
}


// 400 - Duplicate name
{
  "error": "Company name already exists."
}
```


---


## 🔸 **10. Get Companies**


### **Endpoint**: `GET /api/inventory/companies`


### **Auth**: Required


### **Description**: Retrieves all companies/brands


#### **Request Body**: None (GET request)


#### **Success Response** (200):


```json
[
  {
    "id": 1,
    "name": "Ray-Ban",
    "description": "Premium eyewear manufacturer",
    "createdAt": "2025-09-10T10:30:00.000Z",
    "updatedAt": "2025-09-10T10:30:00.000Z"
  },
  {
    "id": 2,
    "name": "Oakley",
    "description": "Sports eyewear specialist",
    "createdAt": "2025-09-10T10:30:00.000Z",
    "updatedAt": "2025-09-10T10:30:00.000Z"
  }
]
```


---


## 🔸 **11. Get Company Products**


### **Endpoint**: `GET /api/inventory/company/:companyId/products`


### **Auth**: Required


### **Description**: Retrieves all products for a specific company


### **Example**: `GET /api/inventory/company/1/products`


### **Query Parameters** (optional):


- `eyewearType`: Filter by eyewear type
- `frameType`: Filter by frame type


#### **Request Body**: None (GET request)


#### **Success Response** (200):


```json
{
  "products": [
    {
      "id": 5,
      "name": "Ray-Ban Aviator Classic",
      "description": "Classic aviator sunglasses",
      "basePrice": 150.0,
      "barcode": "RB3025001",
      "eyewearType": "SUNGLASSES",
      "frameType": "AVIATOR",
      "company": {
        "id": 1,
        "name": "Ray-Ban"
      },
      "shopInventory": [
        {
          "id": 1,
          "quantity": 20,
          "shopId": 1
        }
      ]
    }
  ],
  "grouped": {
    "SUNGLASSES": {
      "AVIATOR": [
        // Products array
      ],
      "WAYFARER": [
        // Products array
      ]
    }
  },
  "summary": {
    "totalProducts": 15,
    "byEyewearType": {
      "SUNGLASSES": 10,
      "GLASSES": 5
    },
    "byFrameType": {
      "AVIATOR": 6,
      "WAYFARER": 4,
      "ROUND": 5
    }
  }
}
```


---


## 📝 **Field Validations**


### **EyewearType Values**:


- `"GLASSES"`
- `"SUNGLASSES"`
- `"LENSES"`


### **FrameType Values**:


- `"AVIATOR"`
- `"ROUND"`
- `"SQUARE"`
- `"WAYFARER"`
- `"RECTANGULAR"`
- `"OVERSIZED"`
- `"CAT_EYE"`


### **Required Fields by Endpoint**:


- **Add Product**: `name`, `basePrice`, `eyewearType`, `companyId`
- **Stock Operations**: `barcode` or `productId`, `quantity`
- **Add Company**: `name`
- **Update Stock by Barcode**: `barcode`, `quantity`, `shopId`


---

===============================================================================
Patient routes 👍
🔹 Patients API Documentation
1. Create Patient
URL: /patients/


Method: POST


Auth Required: ✅ Yes (Bearer Token)


Request Body (JSON):


{
  "name": "Ravi Kumar",
  "age": 32,
  "gender": "Male",
  "phone": "9876543210",
  "address": "123 Street, City",
  "medicalHistory": "Diabetes, High BP",
  "shopId": 1
}


Response (201 - Created):


{
  "id": 1,
  "name": "Ravi Kumar",
  "age": 32,
  "gender": "Male",
  "phone": "9876543210",
  "address": "123 Street, City",
  "medicalHistory": "Diabetes, High BP",
  "shopId": 1,
  "createdAt": "2025-09-12T10:30:00.000Z",
  "updatedAt": "2025-09-12T10:30:00.000Z"
}


Error Response (500):


{
  "error": "Patient creation failed"
}



2. Get All Patients
URL: /patients/


Method: GET


Auth Required: ✅ Yes (Bearer Token)


Query Params:


page (optional, default = 1)


limit (optional, default = 10)


search (optional → searches in name or phone)


Example URL:


/patients?page=1&limit=5&search=Ravi


Response (200 - OK):


{
  "patients": [
    {
      "id": 1,
      "name": "Ravi Kumar",
      "age": 32,
      "gender": "Male",
      "phone": "9876543210",
      "address": "123 Street, City",
      "medicalHistory": "Diabetes, High BP",
      "shopId": 1,
      "createdAt": "2025-09-12T10:30:00.000Z",
      "updatedAt": "2025-09-12T10:30:00.000Z"
    }
  ],
  "total": 1,
  "page": 1,
  "totalPages": 1
}


Error Response (500):


{
  "error": "Failed to fetch patients"
}



3. Get Patient by ID
URL: /patients/:id


Method: GET


Auth Required: ✅ Yes (Bearer Token)


Example Request:


/patients/1


Response (200 - OK):


{
  "id": 1,
  "name": "Ravi Kumar",
  "age": 32,
  "gender": "Male",
  "phone": "9876543210",
  "address": "123 Street, City",
  "medicalHistory": "Diabetes, High BP",
  "shopId": 1,
  "createdAt": "2025-09-12T10:30:00.000Z",
  "updatedAt": "2025-09-12T10:30:00.000Z",
  "prescriptions": [
    {
      "id": 5,
      "patientId": 1,
      "doctorName": "Dr. Sharma",
      "medicines": "Metformin, Amlodipine",
      "createdAt": "2025-09-12T10:45:00.000Z"
    }
  ],
  "invoices": [
    {
      "id": 10,
      "patientId": 1,
      "totalAmount": 500,
      "createdAt": "2025-09-12T10:50:00.000Z",
      "items": [
        {
          "id": 21,
          "invoiceId": 10,
          "productId": 7,
          "quantity": 2,
          "price": 250,
          "product": {
            "id": 7,
            "name": "Glucose Test Kit"
          }
        }
      ]
    }
  ]
}


Error Response (404):


{
  "error": "Patient not found"
}


Error Response (500):


{
  "error": "Failed to fetch patient"
}




====================================================================================================================
CUSTOMER API 
1️⃣ Create a Standalone Customer
Route: POST /api/customer/


Auth Required: ✅


Request Body (JSON):


{
  "name": "Ravi Kumar",
  "phone": "9876543210",
  "address": "123 Main Street, City"
}


(⚠️ name and address are required, phone is optional)
Response (201 - Created):


{
  "id": 1,
  "name": "Ravi Kumar",
  "phone": "9876543210",
  "address": "123 Main Street, City",
  "shopId": 1,
  "createdAt": "2025-09-12T10:40:00.000Z",
  "updatedAt": "2025-09-12T10:40:00.000Z"
}


Error (400):


{
  "error": "Missing required fields: name and address are required."
}



2️⃣ Get All Customers
Route: GET /api/customer/


Auth Required: ✅


Query Parameters (optional):


Param
Type
Default
Description
page
number
1
Page number for pagination
limit
number
10
Number of customers per page
search
string
—
Filter by name, phone, or address




Example URL:


GET /api/customer?page=1&limit=5&search=Ravi


Response (200 - OK):


{
  "customers": [
    {
      "id": 1,
      "name": "Ravi Kumar",
      "phone": "9876543210",
      "address": "123 Main Street, City",
      "shopId": 1,
      "createdAt": "2025-09-12T10:40:00.000Z",
      "updatedAt": "2025-09-12T10:40:00.000Z"
    }
  ],
  "total": 1,
  "page": 1,
  "totalPages": 1
}


Error (500):


{
  "error": "Failed to fetch customers."
}



3️⃣ Get a Single Customer by ID
Route: GET /api/customer/:id


Auth Required: ✅


Example URL:


GET /api/customer/1


Response (200 - OK):


{
  "id": 1,
  "name": "Ravi Kumar",
  "phone": "9876543210",
  "address": "123 Main Street, City",
  "shopId": 1,
  "createdAt": "2025-09-12T10:40:00.000Z",
  "updatedAt": "2025-09-12T10:40:00.000Z",
  "invoices": [
    {
      "id": 10,
      "customerId": 1,
      "staffId": 3,
      "subtotal": 500,
      "totalAmount": 500,
      "paidAmount": 300,
      "status": "UNPAID",
      "createdAt": "2025-09-12T10:50:00.000Z",
      "items": [
        {
          "id": 21,
          "invoiceId": 10,
          "productId": 7,
          "quantity": 2,
          "unitPrice": 250,
          "totalPrice": 500,
          "product": {
            "id": 7,
            "name": "Blood Pressure Monitor"
          }
        }
      ]
    }
  ]
}


Error (404):


{
  "error": "Customer not found."
}



4️⃣ Create Customer and Invoice (Walk-in Customer)
Route: POST /api/customer/invoice


Auth Required: ✅


Request Body (JSON):


{
  "customer": {
    "name": "Amit Verma",
    "phone": "9876541230",
    "address": "45 Elm Street, City"
  },
  "items": [
    {
      "productId": 7,
      "quantity": 2,
      "unitPrice": 250
    },
    {
      "productId": 8,
      "quantity": 1,
      "unitPrice": 300
    }
  ],
  "staffId": 3,
  "paidAmount": 500,
  "paymentMethod": "Cash"
}


(⚠️ customer.name, customer.address, and items are required)
Response (201 - Created):


{
  "id": 12,
  "customerId": 5,
  "staffId": 3,
  "subtotal": 800,
  "totalAmount": 800,
  "paidAmount": 500,
  "status": "UNPAID",
  "createdAt": "2025-09-12T11:00:00.000Z",
  "updatedAt": "2025-09-12T11:00:00.000Z"
}


Error (400 - Insufficient Stock):


{
  "error": "Insufficient stock for product: \"Blood Pressure Monitor\". Requested: 2, Available: 1."
}


Error (500 - Generic):


{
  "error": "An error occurred while creating the invoice."
}



5️⃣ Get Top Address Hotspots
Route: GET /api/customer/hotspots


Auth Required: ✅


Example URL:


GET /api/customer/hotspots


Response (200 - OK):


[
  { "address": "123 Main Street, City", "customerCount": 5 },
  { "address": "45 Elm Street, City", "customerCount": 3 }
]


Error (500):


{
  "error": "An error occurred while fetching hotspots."
}




