# DZ Fashion Hub - Database Schema

## Entity Relationship Diagram (ERD)

```
┌─────────────┐         ┌──────────────┐         ┌─────────────┐
│   Users     │◄────────│    Orders    │────────►│   Products  │
└─────────────┘         └──────────────┘         └─────────────┘
      │                        │                        │
      │                        │                        │
      ├─ Addresses             ├─ OrderItems           ├─ ProductImages
      ├─ Preferences           ├─ Payments             ├─ ProductReviews
      ├─ Wishlist              └─ Deliveries           ├─ ProductCategories
      └─ UserReviews                                   └─ ProductVariants
                                                              │
                                                              ├─ Sizes
                                                              └─ Colors
      
┌─────────────┐         ┌──────────────┐         ┌─────────────┐
│    Stores   │◄────────│    Reviews   │────────►│  Commission │
└─────────────┘         └──────────────┘         └─────────────┘
      │                        │                        │
      ├─ StoreMetrics          ├─ StoreReviews         ├─ CommissionRate
      ├─ StoreBanks            └─ ProductReviews       ├─ CommissionHistory
      └─ StoreDocuments                                └─ PaymentRecords
```

## Database Tables

### 1. Users Table

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255),
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    phone VARCHAR(20),
    profile_image_url TEXT,
    date_of_birth DATE,
    gender VARCHAR(10),
    role ENUM('customer', 'store_owner', 'admin', 'delivery_agent') NOT NULL,
    account_status ENUM('active', 'inactive', 'suspended', 'deleted') DEFAULT 'active',
    language_preference VARCHAR(10) DEFAULT 'ar',
    currency_preference VARCHAR(3) DEFAULT 'DZD',
    notification_preferences JSONB,
    google_id VARCHAR(255),
    facebook_id VARCHAR(255),
    two_factor_enabled BOOLEAN DEFAULT false,
    email_verified BOOLEAN DEFAULT false,
    phone_verified BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role ON users(role);
CREATE INDEX idx_users_status ON users(account_status);
```

### 2. Stores Table

```sql
CREATE TABLE stores (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    owner_id UUID NOT NULL REFERENCES users(id),
    store_name VARCHAR(255) NOT NULL,
    store_slug VARCHAR(255) UNIQUE NOT NULL,
    description TEXT,
    logo_url TEXT,
    banner_url TEXT,
    phone VARCHAR(20),
    email VARCHAR(255),
    website VARCHAR(255),
    address VARCHAR(255),
    city VARCHAR(100),
    state VARCHAR(100),
    postal_code VARCHAR(20),
    country VARCHAR(100) DEFAULT 'Algeria',
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    business_registration_number VARCHAR(255),
    tax_id VARCHAR(255),
    store_status ENUM('pending', 'approved', 'rejected', 'suspended') DEFAULT 'pending',
    verification_status ENUM('unverified', 'verified', 'rejected') DEFAULT 'unverified',
    rating DECIMAL(2, 1) DEFAULT 0,
    total_reviews INTEGER DEFAULT 0,
    response_rate DECIMAL(3, 2),
    average_response_time INTEGER,
    total_products INTEGER DEFAULT 0,
    total_orders INTEGER DEFAULT 0,
    total_revenue DECIMAL(15, 2) DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    verified_at TIMESTAMP
);

CREATE INDEX idx_stores_owner_id ON stores(owner_id);
CREATE INDEX idx_stores_city ON stores(city);
CREATE INDEX idx_stores_status ON stores(store_status);
```

### 3. Products Table

```sql
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    store_id UUID NOT NULL REFERENCES stores(id),
    product_name VARCHAR(255) NOT NULL,
    product_slug VARCHAR(255) NOT NULL,
    description TEXT,
    short_description VARCHAR(500),
    category_id UUID NOT NULL REFERENCES categories(id),
    subcategory_id UUID REFERENCES categories(id),
    brand VARCHAR(100),
    gender VARCHAR(20),
    style VARCHAR(100),
    material VARCHAR(100),
    color VARCHAR(100),
    size VARCHAR(50),
    sku VARCHAR(100),
    base_price DECIMAL(10, 2) NOT NULL,
    discount_percentage DECIMAL(5, 2) DEFAULT 0,
    discount_price DECIMAL(10, 2),
    currency VARCHAR(3) DEFAULT 'DZD',
    stock_quantity INTEGER DEFAULT 0,
    minimum_order_quantity INTEGER DEFAULT 1,
    maximum_order_quantity INTEGER,
    is_active BOOLEAN DEFAULT true,
    rating DECIMAL(2, 1) DEFAULT 0,
    total_reviews INTEGER DEFAULT 0,
    total_sales INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP
);

CREATE INDEX idx_products_store_id ON products(store_id);
CREATE INDEX idx_products_category_id ON products(category_id);
CREATE INDEX idx_products_brand ON products(brand);
CREATE INDEX idx_products_active ON products(is_active);
```

### 4. Orders Table

```sql
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_number VARCHAR(50) UNIQUE NOT NULL,
    customer_id UUID NOT NULL REFERENCES users(id),
    order_status ENUM('pending', 'confirmed', 'processing', 'shipped', 'delivered', 'cancelled', 'refunded') DEFAULT 'pending',
    payment_status ENUM('pending', 'completed', 'failed', 'refunded') DEFAULT 'pending',
    payment_method ENUM('cash_on_delivery', 'bank_transfer', 'edahabia', 'cib') NOT NULL,
    total_amount DECIMAL(15, 2) NOT NULL,
    subtotal DECIMAL(15, 2) NOT NULL,
    shipping_cost DECIMAL(10, 2) DEFAULT 0,
    tax_amount DECIMAL(10, 2) DEFAULT 0,
    discount_amount DECIMAL(10, 2) DEFAULT 0,
    commission_amount DECIMAL(10, 2),
    currency VARCHAR(3) DEFAULT 'DZD',
    shipping_address JSONB,
    billing_address JSONB,
    delivery_agent_id UUID REFERENCES users(id),
    estimated_delivery_date DATE,
    actual_delivery_date DATE,
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    confirmed_at TIMESTAMP,
    shipped_at TIMESTAMP,
    delivered_at TIMESTAMP
);

CREATE INDEX idx_orders_customer_id ON orders(customer_id);
CREATE INDEX idx_orders_status ON orders(order_status);
CREATE INDEX idx_orders_payment_status ON orders(payment_status);
```

### 5. OrderItems Table

```sql
CREATE TABLE order_items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_id UUID NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
    product_id UUID NOT NULL REFERENCES products(id),
    store_id UUID NOT NULL REFERENCES stores(id),
    quantity INTEGER NOT NULL,
    unit_price DECIMAL(10, 2) NOT NULL,
    total_price DECIMAL(15, 2) NOT NULL,
    size VARCHAR(50),
    color VARCHAR(100),
    discount_percentage DECIMAL(5, 2) DEFAULT 0,
    discount_amount DECIMAL(10, 2) DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

### 6. Categories Table

```sql
CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    category_name VARCHAR(255) NOT NULL,
    category_slug VARCHAR(255) UNIQUE NOT NULL,
    description TEXT,
    image_url TEXT,
    parent_id UUID REFERENCES categories(id),
    is_active BOOLEAN DEFAULT true,
    display_order INTEGER,
    seo_title VARCHAR(255),
    seo_description TEXT,
    seo_keywords VARCHAR(500),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_categories_parent_id ON categories(parent_id);
CREATE INDEX idx_categories_active ON categories(is_active);
```

### 7. ProductReviews Table

```sql
CREATE TABLE product_reviews (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    product_id UUID NOT NULL REFERENCES products(id),
    customer_id UUID NOT NULL REFERENCES users(id),
    order_id UUID REFERENCES orders(id),
    rating INTEGER NOT NULL CHECK (rating >= 1 AND rating <= 5),
    title VARCHAR(255),
    review_text TEXT,
    helpful_count INTEGER DEFAULT 0,
    unhelpful_count INTEGER DEFAULT 0,
    images JSONB,
    is_verified_purchase BOOLEAN DEFAULT false,
    is_approved BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_reviews_product_id ON product_reviews(product_id);
CREATE INDEX idx_reviews_customer_id ON product_reviews(customer_id);
```

### 8. Wishlist Table

```sql
CREATE TABLE wishlist (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_id UUID NOT NULL REFERENCES users(id),
    product_id UUID NOT NULL REFERENCES products(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(customer_id, product_id)
);

CREATE INDEX idx_wishlist_customer_id ON wishlist(customer_id);
```

### 9. Cart Table

```sql
CREATE TABLE cart_items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_id UUID NOT NULL REFERENCES users(id),
    product_id UUID NOT NULL REFERENCES products(id),
    quantity INTEGER NOT NULL,
    size VARCHAR(50),
    color VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(customer_id, product_id, size, color)
);

CREATE INDEX idx_cart_customer_id ON cart_items(customer_id);
```

### 10. Payments Table

```sql
CREATE TABLE payments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_id UUID NOT NULL REFERENCES orders(id),
    payment_method ENUM('cash_on_delivery', 'bank_transfer', 'edahabia', 'cib') NOT NULL,
    amount DECIMAL(15, 2) NOT NULL,
    currency VARCHAR(3) DEFAULT 'DZD',
    transaction_id VARCHAR(255),
    payment_status ENUM('pending', 'completed', 'failed', 'refunded') DEFAULT 'pending',
    payment_reference VARCHAR(255),
    error_message TEXT,
    metadata JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP
);

CREATE INDEX idx_payments_order_id ON payments(order_id);
CREATE INDEX idx_payments_status ON payments(payment_status);
```

### 11. Deliveries Table

```sql
CREATE TABLE deliveries (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_id UUID NOT NULL REFERENCES orders(id),
    delivery_agent_id UUID REFERENCES users(id),
    pickup_address JSONB,
    delivery_address JSONB,
    delivery_status ENUM('pending', 'picked_up', 'in_transit', 'delivered', 'failed') DEFAULT 'pending',
    estimated_delivery_date DATE,
    actual_delivery_date DATE,
    tracking_number VARCHAR(100),
    latitude DECIMAL(10, 8),
    longitude DECIMAL(11, 8),
    delivery_notes TEXT,
    signature_image_url TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_deliveries_order_id ON deliveries(order_id);
CREATE INDEX idx_deliveries_agent_id ON deliveries(delivery_agent_id);
CREATE INDEX idx_deliveries_status ON deliveries(delivery_status);
```

### 12. Commissions Table

```sql
CREATE TABLE commissions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    order_id UUID NOT NULL REFERENCES orders(id),
    store_id UUID NOT NULL REFERENCES stores(id),
    commission_rate DECIMAL(5, 2) NOT NULL,
    order_amount DECIMAL(15, 2) NOT NULL,
    commission_amount DECIMAL(10, 2) NOT NULL,
    status ENUM('pending', 'calculated', 'paid', 'rejected') DEFAULT 'pending',
    payment_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_commissions_store_id ON commissions(store_id);
CREATE INDEX idx_commissions_status ON commissions(status);
```

### 13. Addresses Table

```sql
CREATE TABLE addresses (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),
    address_type ENUM('home', 'work', 'other') DEFAULT 'home',
    street_address VARCHAR(255) NOT NULL,
    apartment_number VARCHAR(100),
    city VARCHAR(100) NOT NULL,
    state VARCHAR(100),
    postal_code VARCHAR(20),
    country VARCHAR(100) DEFAULT 'Algeria',
    phone VARCHAR(20),
    is_default BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_addresses_user_id ON addresses(user_id);
```

### 14. ProductImages Table

```sql
CREATE TABLE product_images (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
    image_url TEXT NOT NULL,
    alt_text VARCHAR(255),
    display_order INTEGER,
    is_primary BOOLEAN DEFAULT false,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_product_images_product_id ON product_images(product_id);
```

### 15. StoreMetrics Table

```sql
CREATE TABLE store_metrics (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    store_id UUID NOT NULL REFERENCES stores(id),
    date DATE NOT NULL,
    total_views INTEGER DEFAULT 0,
    total_clicks INTEGER DEFAULT 0,
    total_orders INTEGER DEFAULT 0,
    total_revenue DECIMAL(15, 2) DEFAULT 0,
    total_commission DECIMAL(10, 2) DEFAULT 0,
    average_rating DECIMAL(2, 1),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(store_id, date)
);

CREATE INDEX idx_store_metrics_store_id ON store_metrics(store_id);
```

## Key Relationships

1. **Users → Orders** (One-to-Many)
   - One user can have multiple orders

2. **Orders → OrderItems** (One-to-Many)
   - One order can have multiple items

3. **Products → OrderItems** (One-to-Many)
   - One product can appear in many orders

4. **Stores → Products** (One-to-Many)
   - One store can have multiple products

5. **Users → Stores** (One-to-One)
   - User as store owner

6. **Users → Cart** (One-to-Many)
   - One user has one cart with multiple items

7. **Users → Addresses** (One-to-Many)
   - One user can have multiple addresses

## Performance Considerations

1. **Indexes**
   - Primary keys on all tables
   - Foreign keys indexed
   - Status fields indexed for filtering
   - Date fields indexed for range queries

2. **Partitioning Strategy**
   - Orders table: Partition by date
   - OrderItems table: Partition by order date
   - ProductReviews table: Partition by creation date

3. **Archive Strategy**
   - Move old orders to archive table
   - Keep metrics aggregated
   - Soft deletes for user data retention

## Data Integrity

1. **Constraints**
   - NOT NULL for critical fields
   - UNIQUE for unique identifiers
   - CHECK constraints for enums
   - Foreign key constraints

2. **Triggers**
   - Update `updated_at` timestamp
   - Update store metrics on order
   - Calculate commissions on payment

## SEO & Analytics

1. **Analytics Fields**
   - View count
   - Click count
   - Conversion rate
   - Revenue tracking

2. **SEO Fields**
   - URL slugs
   - Meta titles
   - Meta descriptions
   - Keywords

