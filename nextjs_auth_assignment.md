####Mini Assignment(takes 12-16 hours)
## Assignment: Event Management System

### Overview
In this assignment, you will build an Event Management System that allows users to browse events publicly, and—once authenticated—perform actions such as creating, managing, and attending events based on their role. The system will feature three distinct roles:

- **Admin:** Has full control (can manage events and users, including deletion).
- **Organizer:** Can create and manage their own events.
- **Attendee:** Can browse events and sign up for them.

This project mimics a real-world application where secure access and role-specific capabilities are essential.

---

### Objectives

1. **Authentication with NextAuth:**
   - Set up NextAuth in your Next.js 15 project.
   - Implement secure authentication (via email/password or OAuth) that manages user sessions.

2. **Multi-Role Authorization:**
   - Define user roles (Admin, Organizer, Attendee) in your database (e.g., using Prisma with SQLite or PostgreSQL).
   - Create middleware or higher-order functions to protect pages and API routes based on user roles.
   - Ensure role-based access control on both server-rendered pages and API endpoints.

3. **Real-World Endpoints:**
   - Build both public and protected endpoints.
   - Ensure that public endpoints remain accessible without authentication, while protected endpoints enforce role-specific access.

---

### Endpoints Specification

#### **Public Endpoints (3)**
These endpoints are accessible to all users—even without authentication.

1. **GET `/`**  
   - **Purpose:** Home page showcasing upcoming events.
   - **Details:** Display a brief introduction and a list of featured events.

2. **GET `/events`**  
   - **Purpose:** List all available events.
   - **Details:** Publicly accessible listing with basic event information.

3. **GET `/events/[id]`**  
   - **Purpose:** View detailed information about a specific event.
   - **Details:** Event details such as description, date, location, and organizer info.

#### **Protected Endpoints (5)**
These endpoints require users to be logged in, with additional role checks where necessary.

4. **GET `/profile`**  
   - **Purpose:** User profile page.
   - **Details:** Display the authenticated user's account details, event sign-ups, and preferences.

5. **GET `/dashboard`**  
   - **Purpose:** Dashboard with personalized event data.
   - **Details:** Content varies based on role. For example, an Organizer sees their events while an Attendee sees events they’re signed up for.

6. **POST `/api/events`**  
   - **Purpose:** Create a new event.
   - **Access:** Accessible to Organizers and Admins.
   - **Details:** Validate input and associate the event with the Organizer’s account.

7. **PUT `/api/events/[id]`**  
   - **Purpose:** Update an existing event.
   - **Access:** Organizers can update events they own; Admins can update any event.
   - **Details:** Validate user permissions before allowing changes.

8. **DELETE `/api/events/[id]`**  
   - **Purpose:** Delete an event.
   - **Access:** Restricted to Admin users.
   - **Details:** Secure deletion process with role verification.

#### **Admin-only Endpoint (1)**
This endpoint is exclusively accessible by Admin users.

9. **GET `/admin`**  
   - **Purpose:** Admin panel for managing users and reviewing system analytics.
   - **Details:** Provide tools for user management, event oversight, and system reporting.

---

### Implementation Steps

1. **Project Setup:**
   - Initialize a new Next.js 15 project.
   - Install NextAuth along with any other necessary dependencies (e.g., Prisma for database management).

2. **NextAuth Configuration:**
   - Configure NextAuth in your `[...nextauth].js` file.
   - Set up providers (email/password or OAuth) and manage session callbacks.
   - Ensure your user model includes a role attribute.

3. **Database and User Roles:**
   - Create a user schema that supports roles (Admin, Organizer, Attendee).
   - Seed your database with some test users to simulate real-world scenarios.

4. **Middleware and Role-based Access:**
   - Develop middleware to check for authentication on protected routes.
   - Implement role-based checks in your API routes and pages. For instance:
     - Use a helper function to verify that a user is an Organizer or Admin before allowing event creation.
     - In the admin panel, enforce that only Admin users can access the page.

5. **Building the Endpoints:**
   - **Public Pages:** Create pages for `/`, `/events`, and `/events/[id]` that fetch and display event data.
   - **Protected Pages:** Build pages like `/profile` and `/dashboard` that require the user to be logged in.
   - **API Routes:** Implement CRUD operations for events under `/api/events`, including role checks.
   - **Admin Page:** Develop the `/admin` page to allow Admin users to manage other users and events.

6. **Testing and Validation:**
   - Test each endpoint using different user roles to ensure that access controls are properly enforced.
   - Verify that public endpoints remain accessible without logging in.
   - Confirm that role-specific actions (e.g., deleting an event) are restricted to the correct users.

7. **Bonus Enhancements:**
   - Add a protected `/settings` endpoint for user preferences.
   - Incorporate real-time notifications (using WebSockets or server-sent events) to alert users when new events are posted or updated.

---

### Deliverables

- **Source Code:** A fully functional Next.js 15 application with NextAuth integration.
- **Documentation:** A README file with:
  - Setup and installation instructions.
  - A list of endpoints and the required authentication/authorization for each.
  - Descriptions of the roles and their respective capabilities.
- **Testing:** Evidence of testing different user roles (screenshots or a brief report).

By completing this assignment, you’ll gain practical experience with NextAuth for secure authentication, implement multi-role authorization, and understand how to protect both client-rendered pages and API routes in a scalable Next.js application.

Happy coding, and enjoy building your scalable event management system!
