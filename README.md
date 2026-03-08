# Smart Restaurant Reservation System

This document provides an overview of the Smart Restaurant Reservation System, a web application designed for restaurant customers to book tables and receive recommendations based on their preferences. It is divided into three repositories: a Spring Boot backend, an Angular frontend, and this documentation repository.

## Table of Contents

1.  Project Overview
2.  Key Features
3.  Technology Stack
4.  Project Structure
5.  Setup and Installation
    *   Prerequisites
    *   Backend Setup
    *   Frontend Setup
6.  How to Use
7.  Admin View
8.  Implementation Details
    *   Table Recommendation Logic
    *   Visual Plan and Layout
    *   Data Initialization
9.  Challenges and Solutions
10. Future Improvements
11. Time Spent
12. AI and External Resources

---

## 1. Project Overview

The Smart Restaurant Reservation System allows users to find and book restaurant tables. Users can specify their desired date, time, number of guests, and various preferences (e.g., window seat, privacy, accessibility). The system then provides a visual layout of the restaurant, highlighting "best match" tables and marking unavailable ones. Users can select a table and complete their reservation.

## 2. Key Features

*   **Reservation Search & Filtering:** Filter available tables by date, time, number of guests, and preferences (window, private, accessible).
*   **Visual Table Plan:** Interactive grid layout displaying all restaurant tables.
*   **Intelligent Table Recommendation:** Suggests the "best match" tables based on guest count and preferences.
*   **Visual Status Indicators:** Clearly distinguishes between available, recommended, selected, and unavailable (reserved or too small) tables.
*   **Simple Reservation Process:** Guided flow from search to selection to booking confirmation.
*   **Basic Admin Panel:** A straightforward view to inspect `Tables`, `Reservations`, and `Users` data.

## 3. Technology Stack

### Backend
*   **Language:** Java 25
*   **Framework:** Spring Boot (Gradle-based project)
*   **Database:** H2 (in-memory for development and demonstration)
*   **Build Tool:** Gradle
*   **Version Control:** Git

### Frontend
*   **Framework:** Angular
*   **Language:** TypeScript, HTML, CSS
*   **Package Manager:** npm
*   **Version Control:** Git

## 4. Project Structure

The project is organized into three separate GitHub repositories:

*   **Backend:** https://github.com/arhrul/restaurant-reservation-system-backend
*   **Frontend:** https://github.com/arhrul/restaurant-reservation-system-frontend
*   **Documentation:** https://github.com/arhrul/restaurant-reservation-system-wiki

## 5. Setup and Installation

### Prerequisites

To run this application, you will need the following installed on your machine:

*   **Java Development Kit (JDK):** Version 25 or compatible (e.g., OpenJDK 25).
*   **Node.js & npm:** A recent LTS version of Node.js, which includes npm.
*   **Git:** For cloning the repositories.
*   (Optional but recommended: An IDE like IntelliJ IDEA or VS Code.)

### Backend Setup

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/arhrul/restaurant-reservation-system-backend
    cd restaurant-reservation-system-backend
    ```
2.  **Run the application:**
    The Spring Boot application uses an in-memory H2 database, which will be initialized with sample data automatically on startup.
    ```bash
    ./gradlew bootRun
    ```
    The backend will start on `http://localhost:8080` by default.

### Frontend Setup

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/arhrul/restaurant-reservation-system-frontend
    cd restaurant-reservation-system-frontend
    ```
2.  **Install dependencies:**
    ```bash
    npm install
    ```
3.  **Run the application:**
    ```bash
    ng serve
    ```
    The frontend application will be accessible at `http://localhost:4200`.

## 6. How to Use

1.  **Access the application:** Open your web browser and navigate to `http://localhost:4200` (assuming the frontend is running on its default port).
2.  **Enter Reservation Details:** Fill in the required fields:
    *   Firstname, Lastname, Email
    *   Date and Time for the reservation
    *   Number of people in your party
    *   **Preferences (Optional):** You can select preferences such as "Window," "Private," or "Accessible" to influence table recommendations.
3.  **Show Results:** Click the "SHOW RESULTS" button.
4.  **View Table Plan:** A visual grid of restaurant tables will appear.
    *   **"Best Match" tables:** Highlighted with an orange "Best match" badge, indicating they optimally fit your criteria.
    *   **Disabled (grayed out) tables:** These tables are either already reserved for the chosen time slot or are too small to accommodate your party. They cannot be selected.
    *   **Available tables:** All other tables are available for selection.
5.  **Select a Table:** Click on any available table card to select it. The selected table will be highlighted with a green border. You are not restricted to choosing only "Best match" tables.
6.  **Reserve Table:** Once a table is selected, click the "RESERVE SELECTED TABLE" button.
7.  **Confirmation:** A success message will appear, displaying the details of your confirmed reservation. All reservations are automatically set for a 2-hour duration.

## 7. Admin View

A basic admin view is available to monitor the system's data. This simple interface provides an overview of:

*   **Tables:** All configured restaurant tables with their capacities, coordinates, and features (window, private, accessible).
*   **Reservations:** A list of all current and past reservations.
*   **Users:** All registered users in the system.

Admin view url: `http://localhost:4200/admin`

## 8. Implementation Details

### Table Recommendation Logic

Отлично! Теперь я могу обновить этот раздел, чтобы он был более точным и отражал вашу конкретную реализацию. Вот как я перепишу раздел "Table Recommendation Logic":

---

## 8. Implementation Details

### Table Recommendation Logic

The system identifies "Best Match" tables and their availability based on a specific algorithm implemented in the `RestaurantTableService` on the backend.

1.  **Initial Data Retrieval:** All restaurant tables are fetched from the database.
2.  **Table Transformation:** Each `RestaurantTable` object is mapped to a `TableResponse` DTO, which includes additional flags: `isAdvised` (for "Best Match") and `isAvailable`.
3.  **Availability Check (`checkAvailability` method):**
    *   A table is immediately marked as **unavailable** if its `capacity` is less than the `numberOfGuests` requested.
    *   It then checks for time conflicts: If any existing reservation for that table overlaps with the desired 2-hour reservation slot, the table is marked as **unavailable**.
4.  **"Best Match" Logic (`checkAdvised` method):**
    *   A table can only be a "Best Match" if it's already deemed **available** by the `checkAvailability` method and its capacity is sufficient for the guest count.
    *   **Preference Matching:** It verifies if the table `matchesPreferences` (e.g., `isWindow`, `isPrivate`, `isAccessible`) according to the user's selected preferences. If preferences are selected and the table doesn't meet all of them, it cannot be a "Best Match."
    *   **Optimal Capacity:** Among all tables that are available and satisfy the user's preferences, the algorithm finds the table(s) with the *minimum capacity* that can still accommodate the `numberOfGuests`. These tables are then designated as "Best Match." This ensures efficient use of space by prioritizing smaller, fitting tables.
5.  **Frontend Display:** On the frontend:
    *   Tables with `isAvailable = false` are visually disabled (grayed out).
    *   Tables with `isAdvised = true` are marked with an orange "Best match" badge.
    *   Users can choose any table where `isAvailable = true`, not just the "Best Match" ones.

This algorithm prioritizes finding the smallest suitable table that matches preferences, ensuring both efficiency and customer satisfaction.

### Visual Plan and Layout

The restaurant's table plan is rendered as a grid of interactive "table cards" on the frontend.

*   **Grid Structure:** CSS Grid (`.tables-grid`) is used to arrange tables in a visually appealing and organized manner.
*   **Table Cards:** Each table is represented by a `.table-card` component, displaying its capacity and relevant features (e.g., "Window," "Private," "Accessible" tags).
*   **Visual Cues:**
    *   Orange "Best match" badge for recommended tables.
    *   Green border for the currently selected table.
    *   Grayed out and disabled state for unavailable tables (reserved or too small).

### Data Initialization

The backend includes a `DataInitializer` class (AI-generated) that automatically populates the H2 database with sample data upon application startup. This includes:

*   **Users:** 8 sample user accounts.
*   **Restaurant Tables:** A fixed set of 28 tables with varying capacities (2, 4, 6, 8, 10 seats) and properties (window, private, accessible) arranged in a 4x7 grid.
*   **Reservations:** Approximately 15 random reservations are created for various users, tables, and future time slots. These reservations are set to a 2-hour duration.

## 9. Challenges and Solutions

The most significant challenge encountered during development was **establishing robust and efficient communication between the frontend (Angular) and backend (Spring Boot)**. This involved:

*   **Problem:** Ensuring that data was correctly transmitted, processed, and returned, avoiding confusion with object structures and potential mismatches.
*   **Solution:** A clear separation of concerns was implemented by strictly using **DTOs (Data Transfer Objects)** for all data exchange between the frontend and backend. Additionally, **Enums** were employed where appropriate to provide consistent and type-safe data representations, significantly reducing potential errors and improving clarity.

## 10. Future Improvements

While the current implementation provides core functionality, several areas could be enhanced:

*   **Enhanced Error Messaging:** Improve feedback when no suitable tables are found, providing specific reasons (e.g., "No tables available for this time slot," "No tables for this many guests").
*   **Dynamic Table Merging:** Implement logic to suggest combining adjacent tables for very large groups that cannot be accommodated by a single table.
*   **More Advanced Filters/Preferences:** Add more granular preference options (e.g., "quiet area," "near a specific landmark").
*   **Time-aware Table Availability:** Consider the average visit duration (e.g., 2-3 hours) when marking tables as available for future bookings.
*   **Authentication & Authorization:** Implement secure login for users and restrict admin panel access.
*   **Automated Tests:** Add unit, integration, and end-to-end tests for both backend and frontend.
*   **Containerization:** Dockerize the application for easier deployment and portability.
*   **External API Integration:** Connect to a public API (e.g., a recipe database) to suggest daily specials alongside reservations.

## 11. Time Spent

Approximately **8 hours** were dedicated to resolving the most challenging aspect: establishing robust communication between the frontend and backend using DTOs and Enums. The overall project time for developing all features was not explicitly tracked but involved significant effort beyond this specific challenge.

## 12. AI and External Resources

The following parts of the project utilized AI tools for assistance:

*   **`DataInitializer.java`:** The initial structure and logic for generating diverse sample data for users, tables, and reservations were drafted with AI assistance.
*   **`table-card.component.css`:** The styling for the table cards, grid layout, and visual indicators (like badges and borders) was refined and improved using AI-generated CSS suggestions to achieve a modern and responsive design.

All other significant code sections and architectural decisions reflect original development efforts.
