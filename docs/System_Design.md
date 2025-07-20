# System Design Document for Student-Teacher Assignment Tracker

## 1. System Architecture

The system is designed as a web application built using the Django framework, following the Model-View-Controller (MVC) architectural pattern.

- **Frontend:** Uses Django templates to render HTML pages for users (students and teachers).
- **Backend:** Django views handle business logic, interacting with models and rendering templates or handling API requests.
- **Database:** Relational database (e.g., PostgreSQL or SQLite) stores all persistent data including users, assignments, submissions, marks, messages, and notices.
- **Media Storage:** Uploaded files such as assignments and submissions are stored in the `media/` directory on the server.
- **Authentication:** Django’s built-in authentication system is used with custom user roles.

---

## 2. Core Entities and Relationships

| Entity            | Description                                      | Relationships                                      |
|-------------------|--------------------------------------------------|---------------------------------------------------|
| **User**          | Base user model extended from Django AbstractUser with role flags (`is_student`, `is_teacher`) | One-to-one with Student or Teacher |
| **Student**       | Represents a student profile                     | One-to-one with User; Many-to-many with Teacher via StudentsInClass |
| **Teacher**       | Represents a teacher profile                     | One-to-one with User; Many-to-many with Student via StudentsInClass |
| **StudentsInClass** | Links Teacher and Student                      | Foreign keys to Teacher and Student               |
| **ClassAssignment** | Assignment created by a teacher                | Foreign key to Teacher; Many-to-many with Student |
| **SubmitAssignment** | Student’s assignment submission               | Foreign keys to Student, Teacher, and ClassAssignment |
| **StudentMarks**  | Marks given by a teacher to a student            | Foreign keys to Teacher and Student               |
| **MessageToTeacher** | Messages sent by students to teachers        | Foreign keys to Student and Teacher               |
| **ClassNotice**   | Notices sent by teachers to students             | Foreign key to Teacher; Many-to-many with Student |

---

## 3. API Endpoints

| Functionality             | HTTP Method | URL Pattern                       | Description                                                  |
|---------------------------|-------------|------------------------------------|--------------------------------------------------------------|
| Create Assignment         | POST        | `/upload_assignment/`             | Teacher uploads a new assignment and assigns it to students  |
| Submit Assignment         | POST        | `/submit_assignment/<id>/`        | Student submits their work for a specific assignment         |
| View Submissions          | GET         | `/submit_list/`                   | Teacher views all student submissions for their assignments  |

---

## 4. Authentication Strategy

- Uses Django's built-in `User` model with added boolean fields: `is_student` and `is_teacher`.
- Separate signup forms/views for student and teacher accounts.
- Role-based access control is enforced using decorators (`@login_required`) and user checks (`user.is_teacher` or `user.is_student`).
- Teachers can access assignment creation and submissions listing.
- Students can access assignment submission functionality.
- Each view restricts access based on user role.

---

## 5. Future Scaling Suggestions

To scale this system for production or enterprise-level usage:

- **Caching:** Use Redis or Memcached to cache frequently accessed data like assignment lists.
- **Cloud Storage:** Shift file uploads (assignments, submissions) to AWS S3 or similar for durability and scalability.
- **Load Balancing:** Deploy app on multiple servers with a load balancer to distribute traffic.
- **Database Optimization:** Use indexing, query optimization, and replication for faster performance.
- **Asynchronous Tasks:** Use Celery with RabbitMQ/Redis for background tasks (email notifications, report generation).
- **API Versioning:** Add versioning (e.g., `/api/v1/...`) to maintain backward compatibility in future.
- **Microservices (Optional):** Break features like notifications or grading into separate services as traffic grows.

---

✅ This system design forms the foundation for a scalable, maintainable assignment tracking system tailored for EdTech use cases.
