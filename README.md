# AcadMS: Academic Management System

AcadMS is a comprehensive, web-based platform for managing student registration, attendance, and academic administration. It provides dedicated portals for Students, Faculty, and Administrators, all powered by a FastAPI backend and an Oracle Database with robust PL/SQL business logic.

The entire user interface is a dynamic, single-page application built with React, served directly from the backend.

## System Architecture

*   **Backend**: A REST API built with **Python** and **FastAPI**, providing endpoints for all user roles. Authentication is handled using JWT.
*   **Database**: **Oracle Database** with a schema designed in Third Normal Form (3NF) to ensure data integrity and prevent update anomalies.
*   **Business Logic**: Complex operations, validations, and data processing are encapsulated within **PL/SQL packages** (`registration_manager`, `attendance_manager`, `admin_manager`), ensuring that the core logic resides securely in the database.
*   **Frontend**: A responsive single-page application built with **React** (via CDN), contained within a single `index.html` file. It communicates with the FastAPI backend to provide a seamless user experience.

## Key Features

### Student Portal

*   **Dashboard**: View a real-time overview of current and upcoming course enrollments, total credits, and low attendance alerts.
*   **Course Registration**: Browse available courses for upcoming terms, with real-time seat availability. The system validates eligibility, prerequisites, credit limits, and timetable conflicts before processing a registration request.
*   **Attendance Tracking**: Monitor detailed attendance percentages (Theory, Lab, and Overall) for each course and view a complete history of every class session.
*   **Timetable**: View a personalized class schedule for the current academic term.

### Faculty Portal

*   **Dashboard**: Get a summary of assigned sections, total student enrollment, and section capacity analytics for both current and upcoming terms.
*   **Section Management**: View detailed student lists for each assigned section, including registration status and attendance warnings.
*   **Float Sections**: Create new sections for course offerings in an upcoming term. The system enforces a maximum number of sections per course, set by the administrator.
*   **Class Scheduling**: Create, manage, and cancel class sessions for active courses.
*   **Attendance Marking**: Mark and update attendance for students in bulk or individually for each class session.
*   **Reporting**: Generate detailed attendance reports for any section, including a list of "defaulters" (students below the 75% attendance threshold).

### Admin Portal

*   **System Dashboard**: Access a high-level overview of the entire system, including total active students, faculty, course offerings, and enrollment statistics.
*   **User Management**: Register new students and faculty members, and activate or deactivate their accounts.
*   **Course & Curriculum Management**:
    *   Create new courses with detailed attributes, including credits, hours, and a cap on the maximum sections that can be floated per term (`max_sections`).
    *   Define complex eligibility rules to control which departments, programs, or semesters can register for a course.
    *   Manage course prerequisites.
*   **Academic Term Management**: Create new academic terms, define registration windows, and set the globally "current" term.
*   **Course Offerings**: Float courses for an upcoming term, making them available for faculty to create sections.

## Database Design

The system's foundation is a robust Oracle Database schema designed in **Third Normal Form (3NF)**. This approach minimizes data redundancy and avoids update/deletion anomalies. Key principles include:

*   **No Redundant Data**: Attributes that can be derived are not stored. For example, attendance percentages and enrollment counts are always calculated on-demand via SQL queries and views, ensuring data is always consistent.
*   **Encapsulated Business Logic**: All core business rules are implemented in PL/SQL packages. This centralizes logic within the database, making it secure, maintainable, and independent of the application layer.
*   **Data Integrity**: Constraints, triggers, and transactions are used to maintain the integrity and consistency of academic records.

## Local Setup and Installation

### Prerequisites

*   Python 3.8+
*   An active Oracle Database instance (e.g., Oracle XE)

### 1. Database Setup

All required tables, sequences, packages, triggers, and views are defined in the `database/` directory.

1.  Connect to your Oracle database with a user that has privileges to create tables and packages (e.g., using SQL*Plus or SQLcl).
2.  Execute the master script, which runs all other scripts in the correct order:
    ```sql
    @database/00_master.sql
    ```
    This will create the entire schema and populate it with sample data for a two-term scenario.

### 2. Backend Setup

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/niddzeez/student-registratio-validation-and-attendance-system.git
    cd student-registratio-validation-and-attendance-system
    ```

2.  **Create and activate a virtual environment:**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows: venv\Scripts\activate
    ```

3.  **Install dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Set Environment Variables:**
    The application requires environment variables to connect to the database. Create a `.env` file in the root directory or set them in your shell:
    ```
    JWT_SECRET="your-super-secret-key-for-jwt"

    # The db.py file uses hardcoded credentials for demo purposes.
    # For production, comment out the hardcoded values in db.py
    # and use the following environment variables:
    # DB_USER="your_oracle_username"
    # DB_PASSWORD="your_oracle_password"
    # DB_DSN="host:port/service_name"
    ```

5.  **Run the application:**
    ```bash
    uvicorn main:app --reload
    ```
    The server will be running at `http://localhost:8000`.

### 3. Accessing the Application

*   **Frontend**: Open `http://localhost:8000/` in your web browser.
*   **API Documentation**: The FastAPI backend provides interactive API documentation at `http://localhost:8000/docs`.

## Demo Credentials

The sample data script (`database/06_sample_gen.sql`) populates the database with users for all three roles.

*   **Admin**
    *   **Username:** `admin`
    *   **Password:** `admin123`

*   **Faculty** (Username / Password are the same as Employee ID)
    *   `F001` (Prof. Kumar)
    *   `F002` (Dr. Sharma)
    *   `F006` (Dr. Deshmukh)

*   **Students** (Username / Password are the same as Roll Number)
    *   `2023BCS001` (Aarav, good attendance)
    *   `2023BCS005` (Kabir, defaulter with <75% attendance)
    *   `2024BCS001` (Tanvi, junior student to demonstrate eligibility rules)
    *   `2023BEE001` (Vivaan, EEE student to demonstrate cross-department open electives)
