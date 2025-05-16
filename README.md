# 🛍️ Neembly Front-End Exercise (Vue 3 + Vite)

This is a **Vue 3** front-end coding exercise built using **Vite**. It was developed as part of a technical assessment for **Neembly**, a project by **Two Miss Pink Place Inc.**

🟢 **Live Demo**: [https://rivera-neembly-exercise.netlify.app/](https://rivera-neembly-exercise.netlify.app/)

The application features a simple product management interface that allows you to view, search, paginate, add, edit, and delete products using the Fake Store API. It includes fully interactive UI components, dynamic form handling, and smooth stateful interactions.

---

## 🚀 Tech Stack

- [Vue 3](https://vuejs.org/)
- [Vite](https://vitejs.dev/)
- [Vuetify](https://vuetifyjs.com/) (Material Design UI Components)
- [Axios](https://axios-http.com/) (HTTP Client)

---

## 🧠 Features

- 🔍 Product search and filtering
- 📄 Pagination with local data handling
- ➕ Add, 📝 Edit, 🗑️ Delete product operations (via modal forms)
- 🧾 View single product in a modal
- 🎨 Toggle table columns dynamically
- ✅ Form validation and field reset on success
- 📢 Snackbar alerts for success/error feedback
- 🛑 Delete confirmation dialog for safe deletions

---

## 📁 Project Structure

```
├── pages/
│   └── index.vue               # Main logic for product management
├── components/
│   ├── ProductTable.vue        # Displays product table with search & pagination
│   ├── ProductForm.vue         # Form for creating and editing products
│   ├── ProductViewDialog.vue   # Dialog for viewing product details
│   ├── DeleteConfirmDialog.vue # Confirmation modal for deletions
│   └── AlertSnackbar.vue       # Component to show alerts
```

---

## 🛠 Recommended IDE Setup

- [Visual Studio Code](https://code.visualstudio.com/)
- [Volar](https://marketplace.visualstudio.com/items?itemName=johnsoncodehk.volar)

> ⚠️ Make sure to **disable Vetur** if it’s installed.

---

## ⚙️ Project Setup

Install dependencies:

```bash
npm install
```

### 🔄 Compile and Hot-Reload for Development

```bash
npm run dev
```

### 🏗️ Type-Check, Compile and Minify for Production

```bash
npm run build
```

---

## 🧪 API Used

- [Fake Store API](https://fakestoreapi.com/) – A free and open-source RESTful API for eCommerce prototyping.

---

## 📬 About This Project

This front-end app is a **coding exercise** for **Neembly** from **Two Miss Pink Place Inc.** It showcases proficiency with Vue 3, reusable component design, local data management, modal-driven interaction, and clean UX practices.

---

Thank you for the opportunity! 🚀
