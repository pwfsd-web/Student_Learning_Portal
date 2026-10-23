# Student Learning Portal

A small learning portal built with the **Next.js App Router**. It demonstrates layouts, nested routes, dynamic routes, `Link` navigation, and `notFound()` handling. It also includes the optional extensions: **breadcrumbs**, **`loading.js`**, **route groups**, and a **course feedback form**.

Course data lives in a local JavaScript array (no database, API, or auth).

## Features

- Home, course list, course details, lesson details, dashboard, and profile pages
- Root, Courses, and Dashboard layouts (nested layouts stay visible on child pages)
- Dynamic routes: `/courses/[courseId]` and `/courses/[courseId]/lessons/[lessonId]`
- Custom 404 page for unknown courses or lessons
- Client-side navigation with `next/link`
- **Breadcrumbs** on course, lesson, and profile pages
- **`loading.js`** loading states for courses and dashboard
- **Route groups** `(marketing)` and `(portal)` to organize routes without changing URLs
- **Course feedback form** on every course details page

## Tech Stack

- Next.js 15 (App Router)
- React 19
- Plain JavaScript, minimal inline styling

## Setup

```bash
# 1. Create the project (or clone this repo and skip to step 3)
npx create-next-app@latest student-learning-portal
# Choose: JavaScript, No Tailwind, No src/ directory, Yes App Router, Yes to "@/*" import alias

# 2. Move into the folder
cd student-learning-portal

# 3. Install dependencies
npm install

# 4. Start the dev server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Routes

Folders in parentheses like `(portal)` are **route groups**. They organize files but do **not** appear in the URL.

| URL | File | Description |
| --- | --- | --- |
| `/` | `app/(marketing)/page.js` | Home page |
| `/courses` | `app/(portal)/courses/page.js` | All courses |
| `/courses/[courseId]` | `app/(portal)/courses/[courseId]/page.js` | Course details, lessons, feedback form |
| `/courses/[courseId]/lessons/[lessonId]` | `app/(portal)/courses/[courseId]/lessons/[lessonId]/page.js` | Lesson details |
| `/dashboard` | `app/(portal)/dashboard/page.js` | Dashboard |
| `/dashboard/profile` | `app/(portal)/dashboard/profile/page.js` | Profile |
| any unknown URL / ID | `app/not-found.js` | Custom not-found page |

Sample working URLs: `/courses/react-basics`, `/courses/react-basics/lessons/jsx-intro`

## Project Structure

```
student-learning-portal/
├── components/
│   ├── Breadcrumbs.js
│   └── CourseFeedbackForm.js
├── data/
│   └── courses.js
├── app/
│   ├── layout.js
│   ├── not-found.js
│   ├── (marketing)/
│   │   └── page.js
│   └── (portal)/
│       ├── courses/
│       │   ├── layout.js
│       │   ├── loading.js
│       │   ├── page.js
│       │   └── [courseId]/
│       │       ├── page.js
│       │       └── lessons/
│       │           └── [lessonId]/
│       │               └── page.js
│       └── dashboard/
│           ├── layout.js
│           ├── loading.js
│           ├── page.js
│           └── profile/
│               └── page.js
└── package.json
```

> Delete the default `app/page.js` and `app/globals.css` created by `create-next-app` (and remove the `globals.css` import from the root layout). The home page now lives in `app/(marketing)/page.js`.

---

## Source Code

### `data/courses.js`

```js
export const courses = [
  {
    id: "react-basics",
    title: "React Basics",
    description: "Learn components, props, and state from scratch.",
    lessons: [
      {
        id: "jsx-intro",
        title: "Introduction to JSX",
        content: "JSX lets you write HTML-like syntax inside JavaScript.",
      },
      {
        id: "props-and-state",
        title: "Props and State",
        content: "Props pass data down; state stores data that changes over time.",
      },
    ],
  },
  {
    id: "nextjs-fundamentals",
    title: "Next.js Fundamentals",
    description: "Build full-stack apps with the Next.js App Router.",
    lessons: [
      {
        id: "file-routing",
        title: "File-based Routing",
        content: "Folders inside the app directory become URL segments.",
      },
      {
        id: "layouts",
        title: "Nested Layouts",
        content: "Layouts wrap child pages and persist across navigation.",
      },
    ],
  },
  {
    id: "nodejs-express",
    title: "Node.js & Express",
    description: "Create REST APIs using Node.js and Express.",
    lessons: [
      {
        id: "express-setup",
        title: "Setting Up Express",
        content: "Install Express, create a server, and define your first route.",
      },
      {
        id: "middleware",
        title: "Middleware",
        content: "Middleware functions run between the request and the response.",
      },
    ],
  },
];

export function getCourse(courseId) {
  return courses.find((course) => course.id === courseId);
}

export function getLesson(course, lessonId) {
  return course?.lessons.find((lesson) => lesson.id === lessonId);
}

// Simulates a slow data fetch so loading.js is visible during development.
// Remove the calls to wait() when you no longer need to demo loading states.
export function wait(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}
```

### `components/Breadcrumbs.js`

```js
import Link from "next/link";

export default function Breadcrumbs({ items }) {
  return (
    <nav aria-label="Breadcrumb" style={{ marginBottom: "1rem" }}>
      {items.map((item, index) => {
        const isLast = index === items.length - 1;

        return (
          <span key={`${item.label}-${index}`}>
            {isLast ? (
              <span aria-current="page">{item.label}</span>
            ) : (
              <>
                <Link href={item.href}>{item.label}</Link>
                {" / "}
              </>
            )}
          </span>
        );
      })}
    </nav>
  );
}
```

### `components/CourseFeedbackForm.js`

```js
"use client";

import { useState } from "react";

const emptyForm = { name: "", rating: "5", comment: "" };

export default function CourseFeedbackForm({ courseTitle }) {
  const [form, setForm] = useState(emptyForm);
  const [submitted, setSubmitted] = useState(null);

  function handleChange(event) {
    setForm({ ...form, [event.target.name]: event.target.value });
  }

  function handleSubmit(event) {
    event.preventDefault();
    // No backend required: the feedback is only kept in component state.
    setSubmitted(form);
    setForm(emptyForm);
  }

  return (
    <section style={{ marginTop: "2rem", borderTop: "1px solid #eee", paddingTop: "1rem" }}>
      <h3>Feedback for {courseTitle}</h3>

      {submitted && (
        <p style={{ color: "green" }}>
          Thank you, {submitted.name}! You rated this course {submitted.rating}/5.
        </p>
      )}

      <form onSubmit={handleSubmit} style={{ display: "grid", gap: "0.75rem", maxWidth: "400px" }}>
        <label>
          Name
          <input
            type="text"
            name="name"
            value={form.name}
            onChange={handleChange}
            required
            style={{ display: "block", width: "100%" }}
          />
        </label>

        <label>
          Rating
          <select
            name="rating"
            value={form.rating}
            onChange={handleChange}
            style={{ display: "block", width: "100%" }}
          >
            <option value="5">5 - Excellent</option>
            <option value="4">4 - Good</option>
            <option value="3">3 - Average</option>
            <option value="2">2 - Poor</option>
            <option value="1">1 - Very poor</option>
          </select>
        </label>

        <label>
          Comment
          <textarea
            name="comment"
            value={form.comment}
            onChange={handleChange}
            rows={4}
            required
            style={{ display: "block", width: "100%" }}
          />
        </label>

        <button type="submit">Submit Feedback</button>
      </form>
    </section>
  );
}
```

### `app/layout.js` (Root layout)

```js
import Link from "next/link";

export const metadata = {
  title: "Student Learning Portal",
  description: "A small learning portal built with the Next.js App Router",
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body style={{ fontFamily: "sans-serif", margin: 0 }}>
        <header
          style={{
            display: "flex",
            gap: "1.5rem",
            alignItems: "center",
            padding: "1rem 2rem",
            borderBottom: "1px solid #ddd",
          }}
        >
          <strong>Student Learning Portal</strong>
          <nav style={{ display: "flex", gap: "1rem" }}>
            <Link href="/">Home</Link>
            <Link href="/courses">Courses</Link>
            <Link href="/dashboard">Dashboard</Link>
          </nav>
        </header>
        <main style={{ padding: "2rem" }}>{children}</main>
      </body>
    </html>
  );
}
```

### `app/not-found.js`

```js
import Link from "next/link";

export default function NotFound() {
  return (
    <div>
      <h1>404 - Not Found</h1>
      <p>Sorry, the course or lesson you are looking for does not exist.</p>
      <Link href="/courses">Back to Courses</Link>
    </div>
  );
}
```

### `app/(marketing)/page.js` (Home)

```js
import Link from "next/link";

export default function HomePage() {
  return (
    <div>
      <h1>Welcome to the Student Learning Portal</h1>
      <p>Browse courses, open lessons, and track your progress.</p>
      <p>
        <Link href="/courses">View Courses</Link> |{" "}
        <Link href="/dashboard">Go to Dashboard</Link>
      </p>
    </div>
  );
}
```

### `app/(portal)/courses/layout.js` (Courses layout)

```js
import Link from "next/link";

export default function CoursesLayout({ children }) {
  return (
    <section>
      <div style={{ borderBottom: "1px solid #eee", marginBottom: "1rem" }}>
        <h2>Courses</h2>
        <Link href="/courses">← Back to all courses</Link>
      </div>
      {children}
    </section>
  );
}
```

### `app/(portal)/courses/loading.js`

This loading UI is shown while any page under `/courses` (list, course details, lesson) is loading.

```js
export default function CoursesLoading() {
  return <p>Loading courses...</p>;
}
```

### `app/(portal)/courses/page.js` (Course list)

```js
import Link from "next/link";
import { courses, wait } from "@/data/courses";

export default async function CoursesPage() {
  await wait(800); // demo only: makes loading.js visible

  return (
    <div>
      <h1>All Courses</h1>
      <ul>
        {courses.map((course) => (
          <li key={course.id} style={{ marginBottom: "1rem" }}>
            <Link href={`/courses/${course.id}`}>
              <strong>{course.title}</strong>
            </Link>
            <p style={{ margin: 0 }}>{course.description}</p>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### `app/(portal)/courses/[courseId]/page.js` (Course details)

```js
import Link from "next/link";
import { notFound } from "next/navigation";
import Breadcrumbs from "@/components/Breadcrumbs";
import CourseFeedbackForm from "@/components/CourseFeedbackForm";
import { getCourse, wait } from "@/data/courses";

export default async function CourseDetailsPage({ params }) {
  const { courseId } = await params;
  const course = getCourse(courseId);

  if (!course) notFound();

  await wait(800); // demo only: makes loading.js visible

  return (
    <div>
      <Breadcrumbs
        items={[
          { label: "Home", href: "/" },
          { label: "Courses", href: "/courses" },
          { label: course.title },
        ]}
      />

      <h1>{course.title}</h1>
      <p>{course.description}</p>

      <h3>Lessons</h3>
      <ol>
        {course.lessons.map((lesson) => (
          <li key={lesson.id}>
            <Link href={`/courses/${course.id}/lessons/${lesson.id}`}>
              {lesson.title}
            </Link>
          </li>
        ))}
      </ol>

      <CourseFeedbackForm courseTitle={course.title} />
    </div>
  );
}
```

### `app/(portal)/courses/[courseId]/lessons/[lessonId]/page.js` (Lesson details)

```js
import { notFound } from "next/navigation";
import Breadcrumbs from "@/components/Breadcrumbs";
import { getCourse, getLesson, wait } from "@/data/courses";

export default async function LessonPage({ params }) {
  const { courseId, lessonId } = await params;

  const course = getCourse(courseId);
  if (!course) notFound();

  const lesson = getLesson(course, lessonId);
  if (!lesson) notFound();

  await wait(800); // demo only: makes loading.js visible

  return (
    <div>
      <Breadcrumbs
        items={[
          { label: "Home", href: "/" },
          { label: "Courses", href: "/courses" },
          { label: course.title, href: `/courses/${course.id}` },
          { label: lesson.title },
        ]}
      />

      <h1>{lesson.title}</h1>
      <p>{lesson.content}</p>
    </div>
  );
}
```

### `app/(portal)/dashboard/layout.js` (Dashboard layout)

```js
import Link from "next/link";

export default function DashboardLayout({ children }) {
  return (
    <section>
      <nav
        style={{
          display: "flex",
          gap: "1rem",
          borderBottom: "1px solid #eee",
          paddingBottom: "0.5rem",
          marginBottom: "1rem",
        }}
      >
        <Link href="/dashboard">Dashboard</Link>
        <Link href="/dashboard/profile">Profile</Link>
      </nav>
      {children}
    </section>
  );
}
```

### `app/(portal)/dashboard/loading.js`

```js
export default function DashboardLoading() {
  return <p>Loading dashboard...</p>;
}
```

### `app/(portal)/dashboard/page.js`

```js
import Link from "next/link";
import { courses, wait } from "@/data/courses";

export default async function DashboardPage() {
  await wait(800); // demo only: makes loading.js visible

  const totalLessons = courses.reduce((sum, c) => sum + c.lessons.length, 0);

  return (
    <div>
      <h1>Dashboard</h1>
      <p>Available courses: {courses.length}</p>
      <p>Total lessons: {totalLessons}</p>
      <Link href="/courses">Continue learning →</Link>
    </div>
  );
}
```

### `app/(portal)/dashboard/profile/page.js`

```js
import Breadcrumbs from "@/components/Breadcrumbs";

export default function ProfilePage() {
  return (
    <div>
      <Breadcrumbs
        items={[
          { label: "Home", href: "/" },
          { label: "Dashboard", href: "/dashboard" },
          { label: "Profile" },
        ]}
      />

      <h1>Profile</h1>
      <p><strong>Name:</strong> Student Name</p>
      <p><strong>Email:</strong> student@example.com</p>
      <p><strong>Enrolled courses:</strong> 3</p>
    </div>
  );
}
```

---

## Testing Checklist

Open each URL directly in the browser and click every link:

- [ ] `/`
- [ ] `/courses` (shows "Loading courses..." briefly, then the list)
- [ ] `/courses/react-basics` (breadcrumbs and feedback form visible)
- [ ] `/courses/react-basics/lessons/jsx-intro` (breadcrumbs visible, Courses layout visible)
- [ ] `/dashboard` (shows "Loading dashboard..." briefly)
- [ ] `/dashboard/profile` (breadcrumbs visible, Dashboard layout visible)
- [ ] Submit the feedback form: a thank-you message appears and the form resets
- [ ] `/courses/does-not-exist` shows the custom 404
- [ ] `/courses/react-basics/lessons/does-not-exist` shows the custom 404
- [ ] Confirm URLs do **not** contain `(marketing)` or `(portal)`

## Submission Checklist

- [x] All required routes are available
- [x] Root layout contains the main navigation
- [x] Courses layout appears on course and lesson pages
- [x] Dashboard layout appears on dashboard and profile pages
- [x] Course and lesson pages use dynamic route folders
- [x] Dynamic pages read `params` correctly
- [x] Internal navigation uses `Link`
- [x] Unknown course and lesson IDs show the custom not-found page
- [x] 3 courses and 6 lessons included
- [x] Runs with `npm run dev` without errors
- [x] Optional: breadcrumbs, `loading.js`, route groups, feedback form

## Notes

- In Next.js 15, `params` is a Promise, so dynamic pages are `async` and use `await params`. On Next.js 14 or earlier, read `params` directly without `await`.
- The `@/` import alias is configured by default when you accept the alias prompt in `create-next-app`. If you skipped it, use relative imports.
- The `wait(800)` calls only exist to make `loading.js` visible. Remove them for a production-style build.
- `notFound()` is called before `wait()` so invalid URLs show the 404 page immediately.
- The feedback form is a Client Component (`"use client"`) because it uses `useState`. The rest of the pages are Server Components.
- When submitting, exclude `node_modules` and `.next` (they are already in the default `.gitignore`).
