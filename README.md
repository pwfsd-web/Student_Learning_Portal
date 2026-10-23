
# Student Learning Portal

A small learning portal built with the **Next.js App Router**. It demonstrates layouts, nested routes, dynamic routes, `Link` navigation, and `notFound()` handling. Course data lives in a local JavaScript array (no database, API, or auth).

## Features

- Home, course list, course details, lesson details, dashboard, and profile pages
- Root, Courses, and Dashboard layouts (nested layouts stay visible on child pages)
- Dynamic routes: `/courses/[courseId]` and `/courses/[courseId]/lessons/[lessonId]`
- Custom 404 page for unknown courses or lessons
- Client-side navigation with `next/link`

## Tech Stack

- Next.js 15 (App Router)
- React 19
- Plain JavaScript, minimal inline styling

## Setup

```bash
# 1. Create the project (or clone this repo and skip to step 3)
npx create-next-app@latest student-learning-portal
# Choose: JavaScript, ESLint (optional), No Tailwind, No src/ directory, Yes App Router

# 2. Move into the folder
cd student-learning-portal

# 3. Install dependencies
npm install

# 4. Start the dev server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Routes

| URL | File | Description |
| --- | --- | --- |
| `/` | `app/page.js` | Home page |
| `/courses` | `app/courses/page.js` | All courses |
| `/courses/[courseId]` | `app/courses/[courseId]/page.js` | Course details + lesson list |
| `/courses/[courseId]/lessons/[lessonId]` | `app/courses/[courseId]/lessons/[lessonId]/page.js` | Lesson details |
| `/dashboard` | `app/dashboard/page.js` | Dashboard |
| `/dashboard/profile` | `app/dashboard/profile/page.js` | Profile |
| any unknown URL / ID | `app/not-found.js` | Custom not-found page |

Sample working URLs: `/courses/react-basics`, `/courses/react-basics/lessons/jsx-intro`

## Project Structure

```
student-learning-portal/
├── data/
│   └── courses.js
├── app/
│   ├── layout.js
│   ├── page.js
│   ├── not-found.js
│   ├── courses/
│   │   ├── layout.js
│   │   ├── page.js
│   │   └── [courseId]/
│   │       ├── page.js
│   │       └── lessons/
│   │           └── [lessonId]/
│   │               └── page.js
│   └── dashboard/
│       ├── layout.js
│       ├── page.js
│       └── profile/
│           └── page.js
└── package.json
```

> Delete `app/globals.css` imports and the default boilerplate from `create-next-app` if you want a clean start. Styling is intentionally minimal.

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

### `app/page.js` (Home)

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

### `app/courses/layout.js` (Courses layout)

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

### `app/courses/page.js` (Course list)

```js
import Link from "next/link";
import { courses } from "@/data/courses";

export default function CoursesPage() {
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

### `app/courses/[courseId]/page.js` (Course details)

```js
import Link from "next/link";
import { notFound } from "next/navigation";
import { getCourse } from "@/data/courses";

export default async function CourseDetailsPage({ params }) {
  const { courseId } = await params;
  const course = getCourse(courseId);

  if (!course) notFound();

  return (
    <div>
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
    </div>
  );
}
```

### `app/courses/[courseId]/lessons/[lessonId]/page.js` (Lesson details)

```js
import Link from "next/link";
import { notFound } from "next/navigation";
import { getCourse, getLesson } from "@/data/courses";

export default async function LessonPage({ params }) {
  const { courseId, lessonId } = await params;

  const course = getCourse(courseId);
  if (!course) notFound();

  const lesson = getLesson(course, lessonId);
  if (!lesson) notFound();

  return (
    <div>
      <p>
        <Link href={`/courses/${course.id}`}>← Back to {course.title}</Link>
      </p>
      <h1>{lesson.title}</h1>
      <p>{lesson.content}</p>
    </div>
  );
}
```

### `app/dashboard/layout.js` (Dashboard layout)

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

### `app/dashboard/page.js`

```js
import Link from "next/link";
import { courses } from "@/data/courses";

export default function DashboardPage() {
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

### `app/dashboard/profile/page.js`

```js
export default function ProfilePage() {
  return (
    <div>
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
- [ ] `/courses`
- [ ] `/courses/react-basics`
- [ ] `/courses/react-basics/lessons/jsx-intro`
- [ ] `/dashboard`
- [ ] `/dashboard/profile`
- [ ] `/courses/does-not-exist` → custom 404
- [ ] `/courses/react-basics/lessons/does-not-exist` → custom 404

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

## Notes

- In Next.js 15, `params` is a Promise, so dynamic pages are `async` and use `await params`. On Next.js 14 or earlier, you can read `params` directly without `await`.
- The `@/` import alias is configured by default when you accept the alias prompt in `create-next-app`. If you skipped it, use relative imports (e.g., `../../data/courses`).
- When submitting, exclude `node_modules` and `.next` (they are already in the default `.gitignore`).

## Optional Extensions

Breadcrumbs, `loading.js`, route groups, and a course feedback form.
