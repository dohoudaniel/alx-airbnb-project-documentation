# 🔁 Data Flow Diagram (DFD)

## 📝 Objective

To map out and visualize how data flows within the Airbnb Clone backend system by designing a **Data Flow Diagram (DFD)**. This diagram helps in understanding the interaction between users, the system’s internal processes, and the data stores.

---

## 📐 Description

The DFD illustrates the movement of data between key actors (users, admins), core processes (registration, booking, payment, reviews), and the system's data stores (users, properties, bookings, payments, reviews).

### ✅ Core Entities Represented

- **External Entities**:
  - Guest
  - Host
  - Admin

- **Processes**:
  - User Registration & Login
  - Property Management
  - Booking Management
  - Payment Processing
  - Review System
  - Notifications

- **Data Stores**:
  - Users
  - Properties
  - Bookings
  - Payments
  - Reviews

---

## 🛠 Tool Used

- [**Draw.io (diagrams.net)**](https://draw.io)  
  Used to create a clean and professional-level DFD for backend operations.

---

## 📁 File Structure

The exported DFD image is stored in the following directory within this repository:

```

data-flow-diagram/
└── data-flow\.png

```

---

## 📷 Viewing the Diagram

To view the diagram:

1. Clone or download the repository.
2. Navigate to the `data-flow-diagram/` folder.
3. Open the `data-flow.png` image.

Alternatively, [**click here to view on GitHub**](./data-flow-diagram/data-flow.png) if browsing directly on the web.

---

## 💡 Notes

- The DFD serves as a foundational tool for understanding backend interactions.
- It complements the use case diagram by highlighting **how** data flows once users **interact** with the system.
- This will be a valuable reference for designing database schemas, APIs, and backend logic.

---

## ✅ Next Step

Use this DFD to guide:
- API design decisions
- Data validation and sanitization logic
- Efficient caching and error handling strategies
- Development of unit and integration tests based on data flow points

