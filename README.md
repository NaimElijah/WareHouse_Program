# **Warehouse Management Simulation — C++ Object-Oriented System**

This project implements a full **warehouse simulation system** in modern C++, featuring:

* A fully interactive command-based simulation loop
* Complete object-oriented design using polymorphism, inheritance, dynamic dispatch
* Complex order lifecycle management
* Multiple customer and volunteer types
* Automatic task assignment & progress updates
* Support for backup & restore (deep copy of entire system)
* Full Rule of 5 implementation (copy/move ctor/assignment, destructor)
* Dynamic memory management & cloning across polymorphic types

---

## 📌 **Project Overview**

At its core, this is a **simulation engine** of a warehouse that:

* Receives customers
* Receives orders
* Has volunteers that pick up and deliver orders
* Progresses the warehouse state in discrete **steps**
* Allows actions like:

  * Add customer
  * Add order
  * Print statuses
  * Backup and restore warehouse
  * Close the warehouse gracefully

The program reads an initial configuration file, builds all customers & volunteers, and then processes commands from stdin interactively.

---

## 🧱 **Core Components**

### ### **1. Warehouse (`WareHouse`)**

The heart of the system. Responsible for:

* Customers
* Volunteers
* Orders (pending, in-process, completed)
* Actions log
* Simulation loop
* Backup/restore logic
* Copy/move operations (deep cloning)

Key responsibilities include:

* Assigning orders to volunteers (collectors/drivers)
* Updating volunteer progress via `step()`
* Moving orders between lifecycle states
* Validating IDs and handling not-found cases
* Managing the simulation until `close` is issued

---

## **2. Orders (`Order`)**

Represents a single order placed by a customer.

Order fields include:

* ID
* Customer ID
* Distance
* Collector ID
* Driver ID
* Status: `PENDING`, `COLLECTING`, `DELIVERING`, `COMPLETED`

Orders are moved through different vectors inside the warehouse:

* `pendingOrders`
* `inProcessOrders`
* `completedOrders`

---

## **3. Customers (`Customer`, `SoldierCustomer`, `CivilianCustomer`)**

Each customer has:

* ID
* Name
* Distance
* Max allowed orders
* Current list of order IDs

`SoldierCustomer` and `CivilianCustomer` only differ by **type**, used when created via commands.

---

## **4. Volunteers**

Volunteers can be one of:

### ✔ `CollectorVolunteer`

Pick up orders and prepare them. They operate with a cooldown.

### ✔ `LimitedCollectorVolunteer`

Same as above, but can complete only a limited number of orders.

### ✔ `DriverVolunteer`

Delivers orders. Each step reduces distance.

### ✔ `LimitedDriverVolunteer`

Driver with limited number of deliveries allowed.

Each volunteer supports:

* `hasOrdersLeft()`
* `canTakeOrder(order)`
* `acceptOrder(order)`
* `step()`
* `toString()`
* `clone()`

The warehouse automatically assigns orders to volunteers based on these rules.

---

## **5. Actions (`Action`)**

User commands are implemented as polymorphic classes:

* `SimulateStep`
* `AddOrder`
* `AddCustomer`
* `PrintOrderStatus`
* `PrintCustomerStatus`
* `PrintVolunteerStatus`
* `PrintActionsLog`
* `Close`
* `BackupWareHouse`
* `RestoreWareHouse`

Each action supports:

* `act(WareHouse&)`
* `toString()`
* `clone()`
* Status (`COMPLETED` / `ERROR`)

All executed actions are stored in an **actions log**.

---

## **6. Backup & Restore**

A global pointer:

```cpp
WareHouse* backup = nullptr;
```

Allows:

* `backup` action → deep copies the entire warehouse
* `restore` action → replaces current state with backup

The warehouse implements:

* Copy constructor
* Copy assignment
* Move constructor
* Move assignment
* Destructor

This ensures deep cloning while properly managing resources.

---

## ⚙️ **Program Flow**

### **1. Startup**

The warehouse loads a config file:

```
<name> <id> soldier <distance> <max_orders>
<name> <id> collector <cooldown>
...
```

Customers and volunteers are created and added to the warehouse.

### **2. Interactive Command Loop**

User may enter commands such as:

```
step <num>
order <customer_id>
customer <name> <type> <distance> <max_orders>
orderStatus <id>
customerStatus <id>
volunteerStatus <id>
log
backup
restore
close
```

The warehouse executes each command, updates its state, logs the action, and prints output.

### **3. Termination**

`close` prints all order states and ends simulation.

Backup is cleaned in `main` after program exit.
(Cited from `main.cpp` → )

---

## 📂 **Project Structure**

```
├── include/
│   ├── Action.h
│   ├── Customer.h
│   ├── Order.h
│   ├── Volunteer.h
│   └── WareHouse.h
│
├── src/
│   ├── Action.cpp
│   ├── Customer.cpp
│   ├── Order.cpp
│   ├── Volunteer.cpp
│   └── WareHouse.cpp
│
├── main.cpp
└── README.md
```

---

## 🚀 **How to Run**

### **Compile**

```bash
g++ -std=c++17 -Wall -Wextra -Iinclude src/*.cpp main.cpp -o warehouse
```

### **Run**

```bash
./warehouse <config_file>
```

Example:

```
./warehouse warehouse_config.txt
```

Once running, the system accepts commands directly from stdin.

---

## 🧪 **Example Session**

```
Warehouse is open!
customer Alice soldier 10 3
order 0
step 4
volunteerStatus 1
orderStatus 0
backup
step 10
restore
log
close
```

---

## **In a Nutshell**

What was implemented:

* Full warehouse logic (order flow, volunteer logic, customer management)
* All volunteer types and behaviors
* Order lifecycle handling
* Backup/restore engine using deep copies
* Full Rule of Five (copy/move constructor + assignment, destructor)
* Dynamic memory handling across polymorphic objects
* Action system including all commands
* Input file parser & simulator loop

This project demonstrates:

✔ Object-oriented programming
✔ Polymorphism & dynamic dispatch
✔ Memory management & RAII
✔ C++ deep-copy semantics
✔ Simulation architecture
✔ Clean code, modularity, and extensibility

---

## 📜 **License**

This project is for educational and portfolio use.

Just tell me!
