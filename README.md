# 📚 Online Book Reader System (C++)

An Object-Oriented C++ application that simulates a digital library and reading platform. Built from the ground up, this project reflects deep problem-solving, architectural planning, and hands-on application of core C++ and Software Design principles.

---

## 💡 About The Project & My Learning Journey

Building this system wasn't just about writing working C++ code—it was about learning how to **think like a software engineer**. 

Before typing a single line of code, I spent hours mapping out the system architecture on paper, analyzing how real-world entities (Users, Books, Sessions) should interact. I focused on designing a system that is clean, maintainable, and scalable.

### Key Concepts & OOP Principles Applied:
- **Object-Oriented Design (OOD)**: Modeled real-world entities cleanly by separating data state from management behavior.
- **Single Responsibility Principle (SRP)**: Divided system tasks into distinct components (`Book`, `BooksManager`, `session`, `manage_sessions`, `profile`, `manageprofile`).
- **Encapsulation & Data Hiding**: Kept member variables private and exposed controlled access via getters, setters, and manager methods.
- **Object Composition & Ownership**: Modeled real-world ownership by making each `profile` own its `manage_sessions` directly.
- **Standard Template Library (STL)**: Used `std::vector` for dynamic list handling and `std::map` for fast $O(\log N)$ profile lookups.
- **Pass-by-Reference & Memory Management**: Optimized memory usage and state consistency across function calls.

---

## ✨ Features

- 👤 **Multi-Role User System**: Separate workflows for **Customers** and **Admins**.
- 📖 **Interactive Reading Sessions**: Start new sessions or resume existing ones, saving your page progress.
- 📄 **Page Navigation**: Flip between book pages seamlessly (`NEXT` / `PREV`).
- 📚 **Admin Catalog Control**: Admins can register new books into the system library dynamically.
- 👥 **User Profiles**: Track user information and individual reading history.

---

## 🛠️ Built With

- **Language**: C++11
- **Data Structures**: `std::vector`, `std::map`, `std::string`
- **Paradigm**: Object-Oriented Programming (OOP)

---

## 🚀 How to Run

```bash
# Compile using any C++11 compiler
g++ -std=c++11 main.cpp -o BookReaderApp

# Run the application
./BookReaderApp
