# Collection of Lost Arts — Showroom & Catalog CMS

An elegant, premium showroom storefront and administrative Content Management System (CMS) built to catalog and showcase an exclusive collection of lost arts.

The website operates as a fully client-side application using vanilla HTML, CSS, and JavaScript. It supports two modes of data persistence: standard browser offline cache (**LocalStorage**) and zero-configuration cloud synchronization (**Supabase**).

---

## 🔗 Repository & Live Deployment

* **GitHub Repository**: [https://github.com/collectionoflostarts/websites](https://github.com/collectionoflostarts/websites)
* **GitHub Profile**: [https://github.com/collectionoflostarts](https://github.com/collectionoflostarts)

---

## 🌟 Key Features

### 1. Premium Customer Storefront (`/index.html` & `/gallery.html`)
* **Dynamic Gallery Grid**: Beautifully styled product list filterable by material categories (e.g., Brass, Stone, Iron, Terracotta, Porcelain, Enamel).
* **Search & Category Filters**: Interactive real-time keyword search and category tag switching across the full floor catalog.
* **Interactive Product Details**: Custom immersive popups displaying pricing, dimensions, material descriptions, and high-resolution visuals slider.
* **Direct Lead Generation**: Users can instantly submit queries. Submitting triggers a WhatsApp chat pre-filled with custom product enquiry context and records the lead in the logs database.

### 2. Administrative Portal (`/lostart-admin/index.html`)
* **Secure Access Overlay**: Passcode-secured portal entrance shielding dashboard views.
* **Leads & Inquiry Logs**: Dashboard metrics displaying total leads, WhatsApp clicks, and pending client requests. Includes exact product filters and status toggle (`Pending`, `In Progress`, `Completed`).
* **Category Manager**: Create, edit, or delete product categories with thumbnail previews.
* **Product Inventory Editor**: Full Add, Update, and Delete operations for catalog listings, featuring dynamic file picker thumbnail conversion (Base64).
* **Settings & Passcode Management**: Dynamically change administrative access keys, contact details, and Web3Forms key directly from the Settings interface.

---

## 📁 Project Architecture

```
collection of lost art/
├── components/
│   └── modal/
│       ├── db-config.js     # Supabase endpoint configuration variables
│       ├── modal.css        # Interactive product details and enquiry popup styling
│       ├── modal.js         # Modal view switching, image carousel, and enquiry form handling
│       └── products-db.js   # Unified data adapter (Cloud Supabase vs. LocalStorage fallback)
├── image/                   # Local static imagery assets
├── lostart-admin/
│   └── index.html           # Administrator Portal markup, styling, and management scripts
├── gallery.html             # Full floor catalog gallery page with live search
├── index.html               # Main Customer Storefront index markup
├── main.js                  # Storefront scripting (rendering gallery grids, search and filter bindings)
├── style.css                # Premium vanilla CSS styling tokens and utilities
├── vercel.json              # Vercel deployment configuration
└── README.md                # Technical Documentation
```

---

## 🚀 Getting Started

### Local Development Setup
1. Clone the project repository onto your system:
   ```bash
   git clone https://github.com/collectionoflostarts/websites.git
   ```
2. Serve the directory using any HTTP server to prevent CORS restriction errors on file-loaders. For example:
   ```bash
   npx -y http-server -p 3000
   ```
3. Open **`http://localhost:3000`** in your browser.
4. Access the admin dashboard at **`http://localhost:3000/lostart-admin`**. Default credentials:
   * **Passcode**: `admin123`
   * **Backup coordinates**: `sonidiv1993@gmail.com` / `+91 89468 66094`

---

## ☁️ Supabase Cloud Integration

To migrate the application from local storage cache to a shared, live cloud database:

1. Create a free database project at **[https://supabase.com](https://supabase.com)**.
2. In your Supabase dashboard, navigate to **Project Settings** -> **API**, and copy the **Project URL** and **anon public API Key**.
3. Open [components/modal/db-config.js](file:///c:/Users/DELL/OneDrive/Desktop/collection%20of%20lost%20art/components/modal/db-config.js) and paste them:
   ```javascript
   const SUPABASE_URL = "https://your-project-id.supabase.co";
   const SUPABASE_KEY = "your-anon-public-key";
   ```
4. Run the following SQL schemas in the **SQL Editor** of your Supabase dashboard to create the necessary tables:

### 1. Categories Table Schema
```sql
create table categories (
  id text primary key,          -- Unique slug (e.g., 'brass')
  name text not null,           -- Display name (e.g., 'Brass')
  img text not null,            -- Base64 thumbnail data url
  on_display boolean default true,
  created_at timestamp with time zone default timezone('utc'::text, now())
);
```

### 2. Products Table Schema
```sql
create table products (
  id text primary key,          -- Unique ID (e.g., 'p_1688753235...')
  name text not null,
  material text not null references categories(id) on delete cascade, -- Category slug referencing categories.id
  price text not null,
  size text not null,
  quantity text not null,
  desc_text text not null,
  img text not null,
  images text[] not null,       -- Array of Base64 product image data URLs
  show_on_homepage boolean default true,
  created_at timestamp with time zone default timezone('utc'::text, now())
);
```

### 3. Enquiries Table Schema
```sql
create table enquiries (
  id bigint primary key,        -- Unique numeric timestamp ID
  timestamp text not null,
  product text not null,        -- Product Name
  contact text not null,        -- Customer contact details
  channel text not null,        -- 'WhatsApp' or 'Email'
  status text default 'Pending',-- 'Pending', 'In Progress', or 'Completed'
  created_at timestamp with time zone default timezone('utc'::text, now())
);
```

### 4. Administrative Security Settings Table Schema
```sql
create table admin_settings (
  id text primary key default 'current_settings',
  password text not null default 'admin123',
  email text not null default 'sonidiv1993@gmail.com',
  phone text not null default '+91 89468 66094',
  web3forms_key text default '',
  updated_at timestamp with time zone default timezone('utc'::text, now())
);
```

