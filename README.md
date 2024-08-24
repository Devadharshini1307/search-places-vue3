# Vue 3 + Vite

This template should help get you started developing with Vue 3 in Vite. The template uses Vue 3 `<script setup>` SFCs, check out the [script setup docs](https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup) to learn more.

Learn more about IDE Support for Vue in the [Vue Docs Scaling up Guide](https://vuejs.org/guide/scaling-up/tooling.html#ide-support).

# Search Places

This project allows users to search through cities and view results in a table using Vue 3.

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (v16 or later)
- [npm](https://www.npmjs.com/) (comes with Node.js)

### Installation

1. Clone the repository:

    ```bash
    git clone https://github.com/devadharshini1307/Search-places-vue3.git
    cd Search-places-vue3
    ```

2. Install dependencies:

    ```bash
    npm install
    ```

3. Set up environment variables:
   
    Create a `.env` file in the root directory and add your RapidAPI key:

    ```env
    VITE_RAPIDAPI_KEY=your_api_key_here
    ```

### Running the Project

1. Start the development server:

    ```bash
    npm run dev
    ```

2. Open your web browser and navigate to `http://localhost:5173` to view the application.

## Features

- Search for places with a search box.
- View results in a responsive table.
- Pagination with options to change the number of items displayed.
- Display country flags using the [Flags API](https://flagsapi.com/).
- Keyboard shortcut to focus the search box.


