# Retailer Portal API Documentation

## Overview

The Retailer Portal API provides comprehensive functionality for optical retailers to manage their inventory, track shop distributions, generate reports, and monitor business analytics. This portal allows retailers to manage multiple shops under their network and track product distributions with detailed analytics.

## Base URL

```
http://localhost:8080/retailer
```

## Authentication

All protected endpoints require JWT authentication. Include the token in the Authorization header:

```
Authorization: Bearer YOUR_JWT_TOKEN
```

---

## Authentication Endpoints

### 1. Register Retailer

**POST** `/auth/register`

Register a new retailer account.

**Request Body:**

```json
{
  "email": "retailer@opticalworld.com",
  "password": "retailer123456",
  "name": "Optical World Retailer",
  "companyName": "Optical World Distributors",
  "phone": "+1234567890",
  "address": "123 Business District, City",
  "gstNo": "GST123456789",
  "licenseNo": "LIC789456123"
}
```

**Response:**

```json
{
  "message": "Retailer registered successfully",
  "retailer": {
    "id": 1,
    "email": "retailer@opticalworld.com",
    "name": "Optical World Retailer",
    "companyName": "Optical World Distributors",
    "phone": "+1234567890",
    "address": "123 Business District, City",
    "gstNo": "GST123456789",
    "licenseNo": "LIC789456123",
    "isActive": true,
    "createdAt": "2025-09-20T10:30:00.000Z"
  },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### 2. Login Retailer

**POST** `/auth/login`

**Request Body:**

```json
{
  "email": "retailer@opticalworld.com",
  "password": "retailer123456"
}
```

**Response:**

```json
{
  "message": "Login successful",
  "retailer": {
    "id": 1,
    "email": "retailer@opticalworld.com",
    "name": "Optical World Retailer",
    "companyName": "Optical World Distributors",
    "phone": "+1234567890",
    "address": "123 Business District, City",
    "gstNo": "GST123456789",
    "licenseNo": "LIC789456123",
    "isActive": true
  },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### 3. Get Profile

**GET** `/auth/profile` 🔒

**Headers:**

```
Authorization: Bearer YOUR_TOKEN
```

**Response:**

```json
{
  "id": 1,
  "email": "retailer@opticalworld.com",
  "name": "Optical World Retailer",
  "companyName": "Optical World Distributors",
  "phone": "+1234567890",
  "address": "123 Business District, City",
  "gstNo": "GST123456789",
  "licenseNo": "LIC789456123",
  "isActive": true,
  "createdAt": "2025-09-20T10:30:00.000Z",
  "updatedAt": "2025-09-20T10:30:00.000Z"
}
```

### 4. Update Profile

**PUT** `/auth/profile` 🔒

**Request Body:**

```json
{
  "name": "Updated Retailer Name",
  "companyName": "Updated Company Name",
  "phone": "+9876543210",
  "address": "456 New Business District",
  "gstNo": "GST987654321",
  "licenseNo": "LIC123789456"
}
```

### 5. Change Password

**PUT** `/auth/change-password` 🔒

**Request Body:**

```json
{
  "currentPassword": "retailer123456",
  "newPassword": "newpassword123"
}
```

**Response:**

```json
{
  "message": "Password changed successfully"
}
```

### 6. Refresh Token

**POST** `/auth/refresh-token` 🔒

**Headers:**
```
Authorization: Bearer YOUR_TOKEN
```

**Response:**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

---

## Dashboard Endpoints

### 1. Dashboard Overview

**GET** `/dashboard/overview` 🔒

Get comprehensive dashboard data including sales summary, inventory status, and monthly overview.

**Response:**

```json
{
  "salesSummary": {
    "today": {
      "totalSales": 15000.0,
      "orderCount": 5
    },
    "thisMonth": {
      "totalSales": 450000.0,
      "orderCount": 150
    }
  },
  "inventoryStatus": {
    "totalProducts": 250,
    "totalStock": 5000,
    "availableStock": 3500,
    "allocatedStock": 1500,
    "lowStockProducts": 12,
    "outOfStockProducts": 3
  },
  "monthlyOverview": {
    "productsSold": 800,
    "revenueGenerated": 450000.0,
    "distributionCount": 45,
    "activeShops": 8
  },
  "topProducts": [
    {
      "id": 1,
      "product": {
        "name": "Ray-Ban Aviator Classic",
        "company": {
          "name": "Ray-Ban"
        }
      },
      "soldQuantity": 120,
      "revenue": 48000.0
    }
  ]
}
```

### 2. Sales Analytics

**GET** `/dashboard/sales-analytics` 🔒

**Query Parameters:**

- `period`: today, week, month, year
- `startDate`: YYYY-MM-DD (optional)
- `endDate`: YYYY-MM-DD (optional)

**Example:** `/dashboard/sales-analytics?period=month`

**Response:**

```json
{
  "period": "month",
  "salesByDate": [
    {
      "date": "2025-09-01T00:00:00.000Z",
      "revenue": 15000.0,
      "orders": 5
    }
  ],
  "salesByShop": [
    {
      "shop": {
        "id": 1,
        "name": "Downtown Optical Store",
        "address": "123 Main Street"
      },
      "revenue": 120000.0,
      "quantity": 300,
      "orderCount": 25
    }
  ]
}
```

### 3. Inventory Analytics

**GET** `/dashboard/inventory-analytics` 🔒

**Response:**

```json
{
  "inventoryByCompany": [
    {
      "company": {
        "id": 1,
        "name": "Ray-Ban"
      },
      "totalStock": 1500,
      "availableStock": 1200,
      "allocatedStock": 300,
      "productCount": 25
    }
  ],
  "stockAging": [
    {
      "product": {
        "name": "Ray-Ban Wayfarer",
        "company": {
          "name": "Ray-Ban"
        }
      },
      "currentStock": 50,
      "lastPurchaseDate": "2025-08-15T00:00:00.000Z",
      "supplier": "Ray-Ban Official",
      "batchNumber": "RB2025001"
    }
  ],
  "lowStockAlerts": [
    {
      "product": {
        "name": "Oakley Holbrook",
        "company": {
          "name": "Oakley"
        }
      },
      "availableStock": 5,
      "reorderLevel": 20,
      "wholesalePrice": 250.0
    }
  ]
}
```

---

## Inventory Management Endpoints

### 1. Get All Companies

**GET** `/inventory/companies` 🔒

**Response:**

```json
[
  {
    "id": 1,
    "name": "Ray-Ban",
    "description": "Premium eyewear brand",
    "createdAt": "2025-09-20T10:30:00.000Z",
    "updatedAt": "2025-09-20T10:30:00.000Z",
    "_count": {
      "products": 45
    }
  }
]
```

### 2. Add Company

**POST** `/inventory/companies` 🔒

**Request Body:**

```json
{
  "name": "Oakley",
  "description": "Sports performance eyewear"
}
```

**Response:**

```json
{
  "message": "Company added successfully",
  "company": {
    "id": 2,
    "name": "Oakley",
    "description": "Sports performance eyewear",
    "createdAt": "2025-09-20T10:35:00.000Z",
    "updatedAt": "2025-09-20T10:35:00.000Z"
  }
}
```

### 2.1. Update Company

**PUT** `/inventory/companies/:companyId` 🔒

**Request Body:**

```json
{
  "name": "Oakley Updated",
  "description": "Sports performance eyewear - Updated description"
}
```

**Response:**

```json
{
  "message": "Company updated successfully",
  "company": {
    "id": 2,
    "name": "Oakley Updated",
    "description": "Sports performance eyewear - Updated description",
    "updatedAt": "2025-09-20T11:00:00.000Z"
  }
}
```

### 3. Get Products by Company

**GET** `/inventory/companies/:companyId/products` 🔒

**Query Parameters:**

- `eyewearType`: GLASSES, SUNGLASSES, LENSES
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20)

**Example:** `/inventory/companies/1/products?eyewearType=SUNGLASSES&page=1&limit=10`

**Response:**

```json
{
  "products": [
    {
      "id": 1,
      "name": "Ray-Ban Aviator Classic",
      "description": "Classic aviator sunglasses",
      "basePrice": 300.0,
      "barcode": "8053672001648",
      "sku": "RB3025-001-58",
      "eyewearType": "SUNGLASSES",
      "frameType": "AVIATOR",
      "material": "Metal",
      "color": "Gold",
      "size": "Medium",
      "model": "RB3025",
      "company": {
        "id": 1,
        "name": "Ray-Ban"
      }
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 25,
    "pages": 3
  }
}
```

### 4. Add Product

**POST** `/inventory/products` 🔒

**Request Body:**

```json
{
  "name": "Ray-Ban Wayfarer Classic",
  "description": "Iconic wayfarer design",
  "basePrice": 250.0,
  "barcode": "8053672126571",
  "sku": "RB2140-901-50",
  "eyewearType": "SUNGLASSES",
  "frameType": "WAYFARER",
  "companyId": 1,
  "material": "Acetate",
  "color": "Black",
  "size": "Medium",
  "model": "RB2140"
}
```

**Response:**

```json
{
  "message": "Product added successfully",
  "product": {
    "id": 2,
    "name": "Ray-Ban Wayfarer Classic",
    "description": "Iconic wayfarer design",
    "basePrice": 250.0,
    "barcode": "8053672126571",
    "sku": "RB2140-901-50",
    "eyewearType": "SUNGLASSES",
    "frameType": "WAYFARER",
    "companyId": 1,
    "material": "Acetate",
    "color": "Black",
    "size": "Medium",
    "model": "RB2140",
    "createdAt": "2025-09-20T10:45:00.000Z"
  }
}
```

### 4.1. Update Product

**PUT** `/inventory/products/:productId` 🔒

**Request Body:**

```json
{
  "name": "Ray-Ban Wayfarer Classic Updated",
  "description": "Iconic wayfarer design - Updated",
  "basePrice": 280.0,
  "material": "Premium Acetate",
  "color": "Black Gradient"
}
```

**Response:**

```json
{
  "message": "Product updated successfully",
  "product": {
    "id": 2,
    "name": "Ray-Ban Wayfarer Classic Updated",
    "description": "Iconic wayfarer design - Updated",
    "basePrice": 280.0,
    "material": "Premium Acetate",
    "color": "Black Gradient",
    "updatedAt": "2025-09-20T11:15:00.000Z"
  }
}
```

### 5. Get Retailer Products

**GET** `/inventory/my-products` 🔒

**Query Parameters:**

- `companyId`: Filter by company
- `eyewearType`: GLASSES, SUNGLASSES, LENSES
- `lowStock`: true/false
- `outOfStock`: true/false
- `search`: Search in name, SKU, or barcode
- `page`, `limit`: Pagination

**Response:**

```json
{
  "products": [
    {
      "id": 1,
      "retailerId": 1,
      "productId": 1,
      "wholesalePrice": 200.0,
      "mrp": 300.0,
      "minSellingPrice": 180.0,
      "totalStock": 100,
      "availableStock": 80,
      "allocatedStock": 20,
      "reorderLevel": 10,
      "isActive": true,
      "product": {
        "id": 1,
        "name": "Ray-Ban Aviator Classic",
        "company": {
          "name": "Ray-Ban"
        }
      },
      "stockStatus": "IN_STOCK",
      "stockValue": 16000.0,
      "allocationRate": 20.0
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 45,
    "pages": 3
  }
}
```

### 6. Add Product to Inventory

**POST** `/inventory/my-products` 🔒

**Request Body:**

```json
{
  "productId": 1,
  "wholesalePrice": 200.0,
  "mrp": 300.0,
  "minSellingPrice": 180.0,
  "initialStock": 100,
  "reorderLevel": 15,
  "warehouseLocation": "Warehouse A - Section 1",
  "supplier": "Ray-Ban Official Distributor",
  "costPrice": 180.0
}
```

### 7. Update Stock

**PUT** `/inventory/my-products/:retailerProductId/stock` 🔒

**Request Body:**

```json
{
  "quantity": 50,
  "type": "ADD",
  "reason": "New stock arrival",
  "costPrice": 180.0,
  "supplier": "Ray-Ban Official",
  "warehouseLocation": "Warehouse A",
  "batchNumber": "RB2025001",
  "expiryDate": "2027-12-31"
}
```

**Response:**

```json
{
  "message": "Stock updated successfully",
  "retailerProduct": {
    "id": 1,
    "totalStock": 150,
    "availableStock": 130,
    "stockValue": 24000.0
  },
  "transaction": {
    "id": 15,
    "type": "ADD",
    "quantity": 50,
    "reason": "New stock arrival"
  }
}
```

### 7.1. Update Retailer Product

**PUT** `/inventory/my-products/:retailerProductId` 🔒

**Request Body:**

```json
{
  "wholesalePrice": 210.0,
  "mrp": 320.0,
  "minSellingPrice": 190.0,
  "reorderLevel": 20,
  "warehouseLocation": "Warehouse B - Section 2",
  "isActive": true
}
```

**Response:**

```json
{
  "message": "Retailer product updated successfully",
  "retailerProduct": {
    "id": 1,
    "wholesalePrice": 210.0,
    "mrp": 320.0,
    "minSellingPrice": 190.0,
    "reorderLevel": 20,
    "warehouseLocation": "Warehouse B - Section 2",
    "updatedAt": "2025-09-20T11:30:00.000Z"
  }
}
```

### 8. Get Inventory Summary

**GET** `/inventory/summary` 🔒

**Response:**

```json
{
  "totalProducts": 250,
  "totalStockValue": 875000.0,
  "totalQuantity": 5000,
  "availableStock": 3500,
  "allocatedStock": 1500,
  "lowStockProducts": 12,
  "outOfStockProducts": 3,
  "companiesCount": 8,
  "topCompanies": [
    {
      "company": {
        "id": 1,
        "name": "Ray-Ban"
      },
      "productCount": 45,
      "stockValue": 350000.0,
      "totalQuantity": 1200
    }
  ],
  "stockByType": {
    "SUNGLASSES": {
      "quantity": 2500,
      "value": 525000.0
    },
    "GLASSES": {
      "quantity": 2000,
      "value": 300000.0
    },
    "LENSES": {
      "quantity": 500,
      "value": 50000.0
    }
  },
  "recentTransactions": [
    {
      "id": 15,
      "type": "ADD",
      "quantity": 50,
      "product": {
        "name": "Ray-Ban Aviator Classic"
      },
      "createdAt": "2025-09-20T10:30:00.000Z"
    }
  ]
}
```

---

## Shop Distribution Endpoints

### 1. Get Retailer Shops

**GET** `/shops` 🔒

**Query Parameters:**

- `isActive`: true/false
- `partnershipType`: FRANCHISE, DEALER, DISTRIBUTOR

**Response:**

```json
[
  {
    "id": 1,
    "retailerId": 1,
    "shopId": 1,
    "partnershipType": "FRANCHISE",
    "commissionRate": 10.5,
    "creditLimit": 50000.0,
    "paymentTerms": "NET_30",
    "isActive": true,
    "joinedAt": "2025-09-01T00:00:00.000Z",
    "shop": {
      "id": 1,
      "name": "Downtown Optical Store",
      "address": "123 Main Street",
      "phone": "+1234567890",
      "email": "store@downtown.com"
    },
    "stats": {
      "totalDistributions": 25,
      "totalQuantityDistributed": 500,
      "totalAmountDistributed": 125000.0,
      "pendingPayments": 15000.0,
      "lastDistribution": {
        "id": 45,
        "distributionDate": "2025-09-15T00:00:00.000Z",
        "quantity": 20,
        "totalAmount": 5000.0
      }
    }
  }
]
```

### 2. Add Shop to Network

**POST** `/shops` 🔒

**Request Body:**

```json
{
  "shopId": 2,
  "partnershipType": "DEALER",
  "commissionRate": 8.5,
  "creditLimit": 30000.0,
  "paymentTerms": "NET_15"
}
```

**Response:**

```json
{
  "message": "Shop added to network successfully",
  "retailerShop": {
    "id": 2,
    "retailerId": 1,
    "shopId": 2,
    "partnershipType": "DEALER",
    "commissionRate": 8.5,
    "creditLimit": 30000.0,
    "paymentTerms": "NET_15",
    "isActive": true,
    "joinedAt": "2025-09-20T10:30:00.000Z"
  }
}
```

### 2.1. Update Shop Partnership

**PUT** `/shops/:retailerShopId` 🔒

**Request Body:**

```json
{
  "commissionRate": 9.0,
  "creditLimit": 40000.0,
  "paymentTerms": "NET_30",
  "isActive": true
}
```

**Response:**

```json
{
  "message": "Shop partnership updated successfully",
  "retailerShop": {
    "id": 2,
    "commissionRate": 9.0,
    "creditLimit": 40000.0,
    "paymentTerms": "NET_30",
    "isActive": true,
    "updatedAt": "2025-09-20T11:45:00.000Z"
  }
}
```

### 3. Distribute to Shop

**POST** `/distributions` 🔒

**Request Body:**

```json
{
  "retailerShopId": 1,
  "distributions": [
    {
      "retailerProductId": 1,
      "quantity": 20,
      "unitPrice": 250.0
    },
    {
      "retailerProductId": 2,
      "quantity": 15,
      "unitPrice": 180.0
    }
  ],
  "notes": "Monthly stock replenishment",
  "paymentDueDate": "2025-10-20"
}
```

**Response:**

```json
{
  "message": "Products distributed successfully",
  "distributions": [
    {
      "id": 1,
      "retailerId": 1,
      "retailerShopId": 1,
      "retailerProductId": 1,
      "quantity": 20,
      "unitPrice": 250.0,
      "totalAmount": 5000.0,
      "deliveryStatus": "PENDING",
      "paymentStatus": "PENDING",
      "distributionDate": "2025-09-20T10:30:00.000Z",
      "retailerProduct": {
        "product": {
          "name": "Ray-Ban Aviator Classic",
          "company": {
            "name": "Ray-Ban"
          }
        }
      }
    }
  ],
  "totalAmount": 7700.0
}
```

### 3.1. Get All Distributions

**GET** `/distributions` 🔒

**Query Parameters:**

- `shopId`: Filter by specific shop
- `deliveryStatus`: PENDING, SHIPPED, IN_TRANSIT, DELIVERED, RETURNED, CANCELLED
- `paymentStatus`: PENDING, PAID, PARTIAL, OVERDUE, CANCELLED
- `startDate`, `endDate`: Date range (YYYY-MM-DD)
- `page`, `limit`: Pagination

**Example:** `/distributions?shopId=1&deliveryStatus=DELIVERED&page=1&limit=20`

**Response:**

```json
{
  "distributions": [
    {
      "id": 1,
      "retailerShopId": 1,
      "quantity": 20,
      "unitPrice": 250.0,
      "totalAmount": 5000.0,
      "deliveryStatus": "DELIVERED",
      "paymentStatus": "PAID",
      "distributionDate": "2025-09-15T00:00:00.000Z",
      "deliveryDate": "2025-09-17T00:00:00.000Z",
      "paidDate": "2025-09-20T00:00:00.000Z",
      "retailerProduct": {
        "product": {
          "name": "Ray-Ban Aviator Classic",
          "company": {
            "name": "Ray-Ban"
          }
        }
      },
      "shop": {
        "id": 1,
        "name": "Downtown Optical Store",
        "address": "123 Main Street"
      }
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 125,
    "pages": 7
  },
  "summary": {
    "totalDistributions": 125,
    "totalQuantity": 2500,
    "totalAmount": 625000.0,
    "paidAmount": 560000.0,
    "pendingAmount": 65000.0,
    "deliveredCount": 100,
    "pendingDeliveryCount": 25
  }
}
```

### 4. Get Shop Distributions

**GET** `/shops/:retailerShopId/distributions` 🔒

**Query Parameters:**

- `deliveryStatus`: PENDING, SHIPPED, IN_TRANSIT, DELIVERED, RETURNED, CANCELLED
- `paymentStatus`: PENDING, PAID, PARTIAL, OVERDUE, CANCELLED
- `startDate`, `endDate`: Date range
- `page`, `limit`: Pagination

**Response:**

```json
{
  "distributions": [
    {
      "id": 1,
      "quantity": 20,
      "unitPrice": 250.0,
      "totalAmount": 5000.0,
      "deliveryStatus": "DELIVERED",
      "paymentStatus": "PAID",
      "distributionDate": "2025-09-15T00:00:00.000Z",
      "deliveryDate": "2025-09-17T00:00:00.000Z",
      "paidDate": "2025-09-20T00:00:00.000Z",
      "retailerProduct": {
        "product": {
          "name": "Ray-Ban Aviator Classic",
          "company": {
            "name": "Ray-Ban"
          }
        }
      }
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 25,
    "pages": 2
  },
  "summary": {
    "totalDistributions": 25,
    "totalQuantity": 500,
    "totalAmount": 125000.0,
    "paidAmount": 110000.0,
    "pendingAmount": 15000.0
  }
}
```

### 5. Update Delivery Status

**PUT** `/distributions/:distributionId/delivery-status` 🔒

**Request Body:**

```json
{
  "deliveryStatus": "DELIVERED",
  "deliveryDate": "2025-09-20T14:30:00.000Z",
  "trackingNumber": "TRK123456789"
}
```

### 6. Update Payment Status

**PUT** `/distributions/:distributionId/payment-status` 🔒

**Request Body:**

```json
{
  "paymentStatus": "PAID",
  "paidDate": "2025-09-20T16:00:00.000Z"
}
```

### 4. Shop Performance Analytics

**GET** `/dashboard/shop-performance` 🔒

**Query Parameters:**

- `period`: week, month, quarter, year (default: month)
- `shopId`: specific shop ID (optional)

**Example:** `/dashboard/shop-performance?period=month`

**Response:**

```json
{
  "period": "month",
  "shopPerformance": [
    {
      "shop": {
        "id": 1,
        "name": "Downtown Optical Store",
        "address": "123 Main Street",
        "managerName": "John Smith"
      },
      "metrics": {
        "totalRevenue": 125000.0,
        "totalQuantity": 320,
        "orderCount": 28,
        "averageOrderValue": 4464.29,
        "topSellingProduct": {
          "name": "Ray-Ban Aviator Classic",
          "quantity": 45,
          "revenue": 18000.0
        }
      },
      "growth": {
        "revenueGrowth": 12.5,
        "quantityGrowth": 8.3,
        "orderGrowth": 15.2
      }
    }
  ],
  "totalShops": 8,
  "topPerformingShop": {
    "id": 1,
    "name": "Downtown Optical Store",
    "revenue": 125000.0
  },
  "averageRevenuePerShop": 56250.0
}
```

---

## Reports Endpoints

### 1. Generate Profit & Loss Report

**GET** `/reports/profit-loss` 🔒

**Query Parameters:**

- `startDate`: YYYY-MM-DD
- `endDate`: YYYY-MM-DD
- `format`: json, pdf (default: json)

**Example:** `/reports/profit-loss?startDate=2025-09-01&endDate=2025-09-30&format=pdf`

**Response (JSON):**

```json
{
  "reportType": "PROFIT_LOSS",
  "period": {
    "startDate": "2025-09-01",
    "endDate": "2025-09-30"
  },
  "summary": {
    "totalRevenue": 450000.0,
    "totalCosts": 320000.0,
    "grossProfit": 130000.0,
    "grossProfitMargin": 28.89,
    "netProfit": 105000.0,
    "netProfitMargin": 23.33
  },
  "breakdown": {
    "revenueByCompany": [
      {
        "company": "Ray-Ban",
        "revenue": 200000.0,
        "cost": 140000.0,
        "profit": 60000.0
      }
    ],
    "expenses": [
      {
        "type": "COMMISSION",
        "amount": 15000.0
      }
    ]
  }
}
```

### 2. Generate Tax Report

**GET** `/reports/tax-report` 🔒

**Query Parameters:**

- `startDate`, `endDate`: Date range
- `format`: json, pdf

### 3. Generate Stock Valuation Report

**GET** `/reports/stock-valuation` 🔒

**Response:**

```json
{
  "reportType": "STOCK_VALUATION",
  "generatedAt": "2025-09-20T10:30:00.000Z",
  "summary": {
    "totalStockValue": 875000.0,
    "totalProducts": 250,
    "totalQuantity": 5000
  },
  "breakdown": [
    {
      "company": "Ray-Ban",
      "products": [
        {
          "name": "Ray-Ban Aviator Classic",
          "currentStock": 80,
          "unitCost": 180.0,
          "totalValue": 14400.0
        }
      ],
      "companyTotal": 350000.0
    }
  ]
}
```

### 4. Get All Reports

**GET** `/reports` 🔒

**Query Parameters:**

- `type`: PROFIT_LOSS, TAX_REPORT, STOCK_VALUATION (optional)
- `page`: page number (default: 1)
- `limit`: records per page (default: 10)

**Response:**

```json
{
  "reports": [
    {
      "id": 1,
      "type": "PROFIT_LOSS",
      "generatedAt": "2025-09-20T10:30:00.000Z",
      "parameters": {
        "startDate": "2025-09-01",
        "endDate": "2025-09-30"
      },
      "summary": {
        "totalRevenue": 450000.0,
        "netProfit": 105000.0
      }
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 25,
    "pages": 3
  }
}
```

### 5. Delete Report

**DELETE** `/reports/:reportId` 🔒

**Response:**

```json
{
  "message": "Report deleted successfully"
}
```

---

## Error Responses

### Authentication Errors

```json
{
  "error": "Access denied. No token provided."
}
```

### Validation Errors

```json
{
  "error": "Email, password, and name are required."
}
```

### Not Found Errors

```json
{
  "error": "Product not found in your inventory"
}
```

### Business Logic Errors

```json
{
  "error": "Insufficient stock for Ray-Ban Aviator Classic. Available: 10, Requested: 20"
}
```

---

## Status Codes

- **200**: Success
- **201**: Created successfully
- **400**: Bad request / Validation error
- **401**: Unauthorized / Authentication required
- **403**: Forbidden / Access denied
- **404**: Not found
- **500**: Internal server error

---

## Postman Collection Setup

### Environment Variables

Create a Postman environment with:

- `base_url`: http://localhost:8080
- `retailer_token`: (will be set after login)

### Pre-request Script for Authentication

Add this to requests requiring authentication:

```javascript
pm.request.headers.add({
  key: "Authorization",
  value: "Bearer " + pm.environment.get("retailer_token"),
});
```

### Login Test Script

Add this to the login request to save the token:

```javascript
if (pm.response.code === 200) {
  const response = pm.response.json();
  pm.environment.set("retailer_token", response.token);
}
```

---

## Key Features

✅ **Complete Authentication System** - Registration, login, profile management  
✅ **Comprehensive Dashboard** - Sales, inventory, and performance analytics  
✅ **Inventory Management** - Company-wise product categorization  
✅ **Shop Network Management** - Track multiple shops and partnerships  
✅ **Distribution Tracking** - Monitor product distributions to shops  
✅ **Payment Tracking** - Track payments and due amounts  
✅ **Advanced Reporting** - Profit/Loss, Tax, and Stock valuation reports  
✅ **Real-time Analytics** - Dashboard with live business metrics  
✅ **Stock Management** - Add/remove stock with full audit trail

The Retailer Portal provides a complete solution for optical retailers to manage their business operations, from inventory management to shop distribution tracking, with comprehensive reporting and analytics capabilities.
