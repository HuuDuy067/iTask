# API Reference (iTask)

Base URL (relative): `/api`

Local base (example): `http://localhost:5000/api`

---

> Dưới đây mỗi endpoint có ví dụ `local URL`, request `body` (hoặc dạng multipart) và mẫu `response` thành công.

## Auth (`/api/auth`)

- POST `http://localhost:5000/api/auth/register` — Register new user
	- Body (application/json):
		```json
		{
			"name": "Nguyen Van A",
			"email": "a@example.com",
			"password": "password123"
		}
		```
	- Success response (201):
		```json
		{
			"success": true,
			"message": "User registered successfully",
			"data": { "id": "...", "email": "a@example.com", "name": "Nguyen Van A" }
		}
		```

- POST `http://localhost:5000/api/auth/login` — Login (returns JWT)
	- Body (application/json):
		```json
		{
			"email": "a@example.com",
			"password": "password123"
		}
		```
	- Success response (200):
		```json
		{
			"success": true,
			"accessToken": "<JWT_TOKEN>",
			"user": { "id": "...", "name": "Nguyen Van A", "email": "a@example.com" }
		}
		```

## Tasks (`/api/tasks`)

- GET `http://localhost:5000/api/tasks` — Get list of tasks (Authorization: Bearer <token>)
	- Query params: `page`, `limit`, `status`, `q` (search)
	- Success response (200):
		```json
		{
			"success": true,
			"data": [{ "_id": "...", "title": "Task 1", "status": "todo" }],
			"meta": { "page": 1, "limit": 20, "total": 42 }
		}
		```

- GET `http://localhost:5000/api/tasks/:id` — Get task detail
	- Success response (200):
		```json
		{
			"success": true,
			"data": { "_id": "...", "title": "Task 1", "description": "...", "attachments": [] }
		}
		```

- POST `http://localhost:5000/api/tasks` — Create task (multipart/form-data)
	- Form fields (multipart):
		- `title` (string)
		- `description` (string)
		- `dueDate` (ISO string)
		- files: `image` (single), `attachments` (multiple)
	- Example successful response (201):
		```json
		{ "success": true, "data": { "_id": "...", "title": "New Task" } }
		```

- PUT `http://localhost:5000/api/tasks/:id` — Update task (multipart/form-data)
	- Same fields as create; response (200) returns updated task.

- DELETE `http://localhost:5000/api/tasks/:id` — Soft delete task
	- Success response (200):
		```json
		{ "success": true, "message": "Task moved to trash" }
		```

- POST `http://localhost:5000/api/tasks/:id/timer/start` — Start timer
	- Body: none
	- Success response (200): `{ "success": true, "message": "Timer started" }`

- POST `http://localhost:5000/api/tasks/:id/timer/stop` — Stop timer
	- Success response (200): `{ "success": true, "message": "Timer stopped", "durationSec": 3600 }`

- PUT `http://localhost:5000/api/tasks/:id/restore` — Restore from trash
- DELETE `http://localhost:5000/api/tasks/:id/force` — Permanently delete
- GET `http://localhost:5000/api/tasks/admin/all` — Get all tasks (Admin only)

## Users (`/api/users`)

- PUT `http://localhost:5000/api/users/profile` — Update profile (multipart single `avatar`)
	- Form-data fields: `name`, `bio`, `avatar` (file)
	- Success response (200): updated user object

- GET `http://localhost:5000/api/users` — Get all users (Admin only)
- DELETE `http://localhost:5000/api/users/:id` — Delete user (Admin only)
- PUT `http://localhost:5000/api/users/change-password` — Change password
	- Body (application/json): `{ "oldPassword": "...", "newPassword": "..." }`

## Admin (`/api/admin`)

- GET `http://localhost:5000/api/admin/logs` — Get system logs (Admin only)
- GET `http://localhost:5000/api/admin/stats` — Get admin statistics (Admin only)

## Groups (`/api/groups`)

- POST `http://localhost:5000/api/groups` — Create group
	- Body (application/json): `{ "name": "Team A", "description": "..." }`
	- Success: created group object with `code` for joining.

- POST `http://localhost:5000/api/groups/join` — Join group by code
	- Body: `{ "code": "ABC123" }`
	- Success: `{ "success": true, "message": "Joined group" }`

- GET `http://localhost:5000/api/groups/my-groups` — Get my groups
- GET `http://localhost:5000/api/groups/:groupId` — Get details
- POST `http://localhost:5000/api/groups/:groupId/invite` — Invite/add member
- GET `http://localhost:5000/api/groups/:groupId/leaderboard` — Group leaderboard
- GET `http://localhost:5000/api/groups/admin/all` — Admin: get groups
- DELETE `http://localhost:5000/api/groups/admin/:id` — Admin: delete group

## System (`/api/system`)

- GET `http://localhost:5000/api/system` — Get system config (public)
- PUT `http://localhost:5000/api/system` — Update system config (Admin only)
	- Body example: `{ "maintenance": false, "banner": "..." }`

## Comments (`/api/comments`)

- POST `http://localhost:5000/api/comments` — Add comment
	- Body: `{ "taskId": "...", "content": "Nice work!" }`
	- Success: created comment object

- GET `http://localhost:5000/api/comments/:taskId` — Get comments for task
- PUT `http://localhost:5000/api/comments/:id` — Update comment
- DELETE `http://localhost:5000/api/comments/:id` — Delete comment

## Categories (`/api/categories`)

- GET `http://localhost:5000/api/categories` — Get categories
- GET `http://localhost:5000/api/categories/:id` — Category details
- POST `http://localhost:5000/api/categories` — Create category
	- Body: `{ "name": "UI", "color": "#FF0000" }`
- PUT `http://localhost:5000/api/categories/:id` — Update category
- DELETE `http://localhost:5000/api/categories/:id` — Delete category
- GET `http://localhost:5000/api/categories/admin/all` — Admin: all categories
- DELETE `http://localhost:5000/api/categories/admin/:id` — Admin delete

## Feedbacks (`/api/feedbacks`)

- POST `http://localhost:5000/api/feedbacks` — Create feedback
	- Body: `{ "title": "Bug report", "content": "..." }`

## Dashboard (`/api/dashboard`)

- GET `http://localhost:5000/api/dashboard/summary` — Get dashboard summary
	- Success response: metrics object `{ "tasksCount": 100, "completed": 60 }`

## Notifications (`/api/notifications`)

- GET `http://localhost:5000/api/notifications` — Get my notifications
	- Success: list of notifications
- PUT `http://localhost:5000/api/notifications/:id/read` — Mark as read
- DELETE `http://localhost:5000/api/notifications/:id` — Delete notification

## Static / Other

- GET `http://localhost:5000/uploads/*` — Static files served from uploads folder
- PeerJS signaling: `http://localhost:5000/peerjs` (PeerServer, path `/myapp`)
- Socket.IO: launched on same HTTP server (connect with `io('http://localhost:5000', { path: '/socket.io' })`)

---

### Notes
- Most protected endpoints require `verifyToken` middleware; admin-only require `verifyAdmin`.
- File upload fields for tasks: `image` (single) and `attachments` (array up to 10). User avatar uses field `avatar`.
- Base server mounts routes exactly as above (see `server/index.ts`).


