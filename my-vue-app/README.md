Flosum Git Web UI

A modern, scalable frontend interface designed for Flosum's Git integration platform. Built using Vue 3, TypeScript, and Vite, the application enables users to visually manage agents, repositories, bidirectional sync, and deployment workflows with precision and ease.

⚙️ Tech Stack

Vue 3 – Composition API with <script setup>

TypeScript – Strong typing for robust development

Vite – Lightning-fast build and dev server

Tailwind CSS – Utility-first responsive design

Lucide Icons – Lightweight and modern icon set

✅ Key Features

🧠 Smart Git Integration UI: Track repositories, agents, sync status, and services visually.

🔁 Bidirectional Sync Control: Toggle sync directions and SFDX conversion with intuitive controls.

🧹 Modular Component Architecture: Easily extendable Vue 3 components for flexibility.

🔐 Secure & Scalable: Built for performance and enterprise-grade usage.

🌗 Dark Mode Ready (optional future enhancement)

🚀 Quick Start

1. Clone the repository

git clone git@github.com:nshuklaflosum/flosum-git-web-ui.git
cd flosum-git-web-ui

2. Install dependencies

npm install

3. Start development server

npm run dev

The app will be available at http://localhost:5173/

📆 Production Build

npm run build

To preview the production build locally:

npm run preview

📁 Project Structure

src/
├── components/               # Reusable Vue components
├── assets/                   # Static assets (icons, logos)
├── App.vue                   # Main application component
├── main.ts                   # App entry point
├── index.css                 # Global styles (Tailwind)
└── vite.config.ts            # Vite configuration

🔍 Status

✅ MVP Completed🚧 Ongoing UI/UX enhancements📆 Ready for integration with backend services

🧠 Vision

This UI serves as the control panel for Git-driven DevOps in the Salesforce ecosystem, blending ease-of-use with powerful configuration options. It’s designed to align with Flosum’s mission of streamlined DevOps, compliance, and release management.

👨‍💻 Created & Maintained By

Built with passion by Nitendra Shukla to enhance the Git integration experience within Flosum’s ecosystem.

📄 License

This project is proprietary and confidential.© Flosum, Inc. All rights reserved.