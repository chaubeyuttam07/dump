bandobast-management/
│
├── public/
│   ├── index.html
│   └── favicon.ico
│
├── src/
│   ├── api/
│   │   └── axiosInstance.js          // axios base configuration
│   │
│   ├── assets/
│   │   ├── logo.png
│   │   ├── bg-pattern.svg
│   │   ├── sample1.jpg               // (your uploaded image)
│   │   └── sample2.jpg
│   │
│   ├── components/
│   │   ├── sidebar/
│   │   │   └── Sidebar.jsx           // Order Mgmt Sidebar with nested menus
│   │   ├── header/
│   │   │   └── Header.jsx            // AppBar / Navbar
│   │   └── common/
│   │       ├── StatusChip.jsx        // Active/Completed chip
│   │       ├── ProgressBar.jsx       // Resource allocation linear bar
│   │       └── ConfirmationDialog.jsx
│   │
│   ├── features/
│   │   ├── orders/
│   │   │   ├── OrderSlice.js         // Redux Toolkit slice for orders
│   │   │   ├── CreateOrder.jsx       // Create Bandobast order form page
│   │   │   ├── OrdersList.jsx        // View Orders page with filters + pagination
│   │   │   ├── ViewOrder.jsx         // Detailed order view page
│   │   │   └── EditOrder.jsx         // Edit Bandobast order
│   │   │
│   │   └── inbox/
│   │       └── Inbox.jsx             // Inbox for approvals
│   │
│   ├── layouts/
│   │   └── MainLayout.jsx            // Contains Sidebar + Header + Outlet
│   │
│   ├── routes/
│   │   └── AppRoutes.jsx             // All frontend routes
│   │
│   ├── store/
│   │   └── store.js                  // Redux Toolkit store configuration
│   │
│   ├── styles/
│   │   └── global.css                // Optional global css
│   │
│   ├── App.jsx                       // Root App (wraps layout + routes)
│   └── main.jsx                      // ReactDOM render (Vite)
│
├── package.json
├── vite.config.js
└── README.md
https://chatgpt.com/canvas/shared/69211644d4148191a6d8aabb5101258b
