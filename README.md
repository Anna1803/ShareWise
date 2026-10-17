# ShareWise ♻️

ShareWise is a web platform that connects schools in need of IT equipment with organisations that have surplus devices to donate. Instead of ending up in landfill, working laptops, monitors and keyboards go to classrooms that actually need them.

I built this project with my team at the end of the Full-Stack Software Development programme at **La Fosse Academy** (London, 2025). The brief was to deliver a complete end-to-end web application using only the technologies we had learned on the course.

> Original team repository: [ShaunMadziva/project-sharewise](https://github.com/ShaunMadziva/project-sharewise)

---

## The problem

From a list of possible project ideas, we chose donations, and as a team we designed the whole concept around it. We noticed two problems:

1. **E-waste.** Companies replace devices that still work, and many of them are destroyed or thrown away.
2. **Donations that don't match the need.** When there's no central place to communicate, schools often receive too much of what they don't need and too little of what they do.

There wasn't a simple, transparent place where schools and donors could talk to each other, so we built one.
<img width="1317" height="892" alt="home" src="https://github.com/user-attachments/assets/ecf1aaaf-6e53-4d09-bee5-527344780786" />

## Our solution

ShareWise turns donating into a **request → donation → notification** workflow:

- **Schools post requests** for exactly the items and quantities they need.
- **Donors browse open requests** and donate towards them, fully or in part. A donor can't give more than was requested, which prevents over-donation.
- **Each request tracks its progress.** The fulfilled quantity is updated with every donation, and the status moves from *pending* to *partially fulfilled* to *fulfilled*.
- **The school is notified** whenever one of its requests is fulfilled or partly fulfilled.
- **Each side has its own dashboard** showing its activity, with a chart summarising the items requested or donated.

<img width="1471" height="810" alt="school_req" src="https://github.com/user-attachments/assets/e218058d-399d-45e1-a6ed-a41c651e2c96" />
<img width="1607" height="592" alt="donations" src="https://github.com/user-attachments/assets/a0d6d6df-4e33-488e-983c-6a919736c69e" />
<img width="1705" height="837" alt="donor dash" src="https://github.com/user-attachments/assets/8d7a7793-d93e-430c-b90d-687a8e2fa936" />
<img width="1645" height="647" alt="notifications school" src="https://github.com/user-attachments/assets/76b22fad-d661-48f7-9453-dc63f7400a14" />

## Features

- Separate sign-up and login for **schools** and **donors**
- **School dashboard:** profile, my requests (requested vs fulfilled quantity, status, date), a notification centre with an unread badge, mark-as-read and delete, and a chart of requested items
- **Donor dashboard:** profile, donation history and a chart of donated items
- **Donation form:** lists all open and partly fulfilled requests, and validates the quantity and description before submitting
- **Request form** for schools to post new needs
- **E-waste impact section** on the landing page, with data visualisations of what discarded devices are made of (see below)

## Tech stack

| Layer | Technologies |
| --- | --- |
| Front end | HTML, CSS, vanilla JavaScript, Bootstrap 5 |
| Data visualisation | Plotly.js, PapaParse, Chart.js |
| Back end | Node.js, Express 5, REST API |
| Database | PostgreSQL |
| Authentication | bcrypt, JSON Web Tokens (JWT) |
| Testing | Jest, jsdom, Supertest |
| DevOps | Docker, Jenkins, Terraform, Ansible, AWS (EC2, ELB, VPC) |

---

## My role

I was a **software developer** on the team, and I built:

### 1. The front end

I designed and built every page of the user interface, working from the business requirements we defined together as a team:

- landing page, login and registration (with a school/donor switch), school and donor dashboards, donation form, request form and contact page
- the styling and layout with Bootstrap 5 and custom CSS
- all the client-side logic in vanilla JavaScript: rendering tables dynamically, form validation, the notification badge and logout

### 2. Connecting the front end to the back end

I connected every page to the Express REST API using `fetch`:

- the login flow: sending credentials, storing the JWT and user details after a successful login, and sending the token in the `Authorization` header to protected routes
- reading the user's ID from the token so each dashboard loads only that school's or donor's data
- the full donation flow, from loading open requests to submitting a donation, and the notification actions (mark as read, delete)

### 3. Database design and data workflow

I designed the PostgreSQL schema and the logic of how information moves through the app:

| Table | What it stores |
| --- | --- |
| `school` / `donor` | The two types of user account |
| `request` | What a school needs: item, quantity, fulfilled quantity, status, date |
| `donation` | How much a donor gives towards a specific request |
| `notification` | Updates sent to a school about its requests |

The tables are linked with foreign keys (`ON DELETE CASCADE`), so a donation always belongs to a donor and a request, and a notification always belongs to a school and a request. I also wrote the sample data and the setup script that creates the database.

### 4. Data visualisation: e-waste impact

After the first five weeks of the bootcamp, I specialised in **data analysis**: gathering, cleaning, manipulating and visualising data. I brought that into ShareWise with the **"About Us & Our Impact"** section on the landing page, which shows why reusing devices matters:

- **The dataset:** I researched and selected a public dataset on the material composition of common IT devices (laptops, mice, keyboards, GPUs, and LCD/LED and CRT monitors) across six categories: plastics, metals (aluminium, copper, iron), circuit boards, glass, batteries and hazardous materials, and precious metals. I prepared it as a CSV (`Client/Datasets.csv`) for the website. 
- **Loading and cleaning:** the CSV is parsed in the browser with **PapaParse**. Missing values (`N/A`) are converted to 0 so the charts don't break.
- **Chart 1, "Material Composition by Device":** a **stacked bar chart** (Plotly) showing what each device is made of. For example, about 55% of an LCD monitor is glass, while a GPU is mostly metal and circuit boards.
- **Chart 2, "Average Material Composition Across All Devices":** a **pie chart** with the average share of each material, calculated in JavaScript across all devices.
<img width="1292" height="668" alt="about us" src="https://github.com/user-attachments/assets/273dd374-c3d6-4d28-8517-6bda3f9df7b3" />

Both charts are responsive. The point is to show visitors that every device sent to landfill is full of plastics, metals and even precious metals that could still be used.

I also built the **dashboard charts** (Chart.js bar charts) that show each school how many of each item it has requested, and each donor how many of each item they have donated. They use aggregated data from the API, where quantities are grouped and summed by item.

### 5. Testing

I wrote front-end unit tests with **Jest** and **jsdom** for the login, registration, donation form, request form and donor dashboard scripts. They check that forms switch correctly, that the right API calls are made, that the token is saved after login and that errors are handled. `fetch` and `localStorage` are mocked. I also tested the main user flows manually.

### 6. Supporting the team

I helped my teammates debug and build parts of the back end (API routes, middleware and controllers), although I didn't lead that work. The back-end authentication (bcrypt password hashing and JWT generation and verification) and the deployment pipeline were built by my teammates.

---

## How it works

```
Browser (HTML + JS)  ──fetch / JSON──▶  Express REST API  ──SQL──▶  PostgreSQL
        │                                       │
        └── JWT saved after login ──────────────┘ (sent in the Authorization header)
```

When a donor donates towards a request, the API:

1. records the **donation**,
2. updates the **request**'s fulfilled quantity and status,
3. creates a **notification** for the school.

### Main API endpoints

| Method | Endpoint | Description |
| --- | --- | --- |
| POST | `/users/schoolsignup` · `/users/donorsignup` | Register |
| POST | `/users/schoollogin` · `/users/donorlogin` | Log in and receive a JWT |
| GET | `/requests` · `/requests/school/:schoolId` | All requests, or one school's requests |
| POST | `/requests` 🔒 | Create a request |
| POST | `/fulfill-donation` | Donate towards a request |
| GET | `/fulfill-donation/:schoolId` 🔒 | A school's notifications |
| GET | `/donations/donor/:donorId` | A donor's donations |
| GET | `/requests/school/:id/processed` · `/donations/donor/:id/processed` | Aggregated data for the dashboard charts |

🔒 = requires a valid JWT

## Running it locally

**You'll need:** [Node.js](https://nodejs.org/) 18+ and a free [Supabase](https://supabase.com/) account, which provides the PostgreSQL database. Prefer a local PostgreSQL install? See the [alternative](#alternative-local-postgresql) below.

### 1. Clone the repository and install dependencies

```bash
git clone https://github.com/<your-username>/sharewise.git
cd sharewise/sharewise-api
npm install
```

### 2. Create the database in Supabase

1. Sign in to Supabase and click **New project**. Give it a name, choose a **database password** (letters and numbers only are easiest) and save it, then pick a region close to you.
2. When the project is ready, open **SQL Editor** → **New query**.
3. Paste the full contents of [`sharewise-db/setup.sql`](sharewise-db/setup.sql) and click **Run**.
4. Open **Table Editor**. You should see five tables: `school`, `donor`, `request`, `donation` and `notification`.

### 3. Get your database connection string

1. Click **Connect** at the top of your Supabase project page.
2. Set the connection method to **Session pooler** and copy the connection string. It looks like this:
   ```
   postgresql://postgres.abcdefgh:[YOUR-PASSWORD]@aws-0-eu-central-1.pooler.supabase.com:5432/postgres
   ```
3. Replace `[YOUR-PASSWORD]`, including the brackets, with your database password.

> Use **Session pooler** rather than "Direct connection": the direct connection needs IPv6, which many home networks don't support.

### 4. Configure the API

Create a file called `.env` inside the `sharewise-api` folder:

```bash
# from inside sharewise-api
touch .env
```

Add these four values to it:

```env
DB_URL=your_supabase_session_pooler_connection_string
SECRET_TOKEN=your_long_random_secret
BCRYPT_SALT_ROUNDS=10
PORT=3000
```

| Variable | What it is |
| --- | --- |
| `DB_URL` | The connection string from step 3, on one line, with no quotes or spaces |
| `SECRET_TOKEN` | Any long random string, used to sign the login tokens. Generate one with:<br>`node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"` |
| `BCRYPT_SALT_ROUNDS` | How much work bcrypt does to hash each password. `10` is the standard value |
| `PORT` | The port the API runs on |

`.env` is listed in `.gitignore`, so your secrets are never committed.

### 5. Start the API

```bash
npm run dev
```

You should see `App up and running on port: 3000`. Keep this terminal open. To check it's connected to the database, open http://localhost:3000/requests in your browser. You should see the sample requests.

### 6. Start the front end

Open a **second terminal** in the project root (`sharewise`) and run:

```bash
npx serve Client -l 5500
```

The `-l 5500` matters: without it, `serve` also tries to use port 3000, which the API is already using. In VS Code, you can instead right-click `Client/index2.html` → **Open with Live Server**.

Then open **http://localhost:5500/index2.html**.

### 7. Try it out

1. **Register a school**, log in and create a request, for example 5 laptops.
2. **Log out, register a donor**, log in, open the donation form and donate towards that request.
3. **Log back in as the school**: the request shows as partially fulfilled and a new notification appears.

> Register new accounts to log in. The sample accounts in `setup.sql` use placeholder passwords, so they can't be used to log in.

### Alternative: local PostgreSQL

If you have PostgreSQL installed locally, skip steps 2 and 3:

1. Create a database: `createdb sharewise`
2. Create the tables: `psql -d sharewise -f sharewise-db/setup.sql`
3. In `sharewise-api/.env`, set:
   ```env
   DB_URL=postgresql://postgres:your_password@localhost:5432/sharewise
   ```

Then continue from step 4.

### Troubleshooting

| Problem | Fix |
| --- | --- |
| `password authentication failed` | Check the password in `DB_URL`. Special characters such as `@`, `#` or `/` can break the string, so reset the Supabase password to letters and numbers only |
| `ENOTFOUND` or connection timeout | Make sure you copied the **Session pooler** string, not the direct connection |
| Port 3000 already in use | Another app is using it. Stop it, or change `PORT` in `.env` (and the `localhost:3000` URLs in the `Client` JavaScript files) |
| The front end loads but shows no data | Check that the API terminal is still running, and look for errors in the browser console (F12 → Console) |

## Tests

From the project root:

```bash
# Front-end tests
cd Client
npm install
npx jest
cd ..

# API tests
cd sharewise-api
npx jest
npm run coverage   # coverage report
```

## Deployment

My teammates also built a full CI/CD pipeline and cloud deployment. **Jenkins** builds the Docker images and pushes them to **Docker Hub**. **Terraform** provisions the AWS infrastructure: a VPC with a public subnet for the API servers and a private subnet for the database. **Ansible** deploys the containers to EC2, and an **Elastic Load Balancer** spreads traffic across the API instances.

![ShareWise architecture](Client/sharewise_architecture.png)

## What I'd improve next

- Move the API URL into a config file instead of hard-coding `localhost:3000`
- Validate donations against the *remaining* quantity of a request, not the total
- Exclude rows with no data (such as "Others") when calculating the average material composition
- Add a shared navigation bar and make the dashboards fully responsive on mobile

Built together at La Fosse Academy. Thanks to my teammates for a great collaboration!

— *Anamaria Sandu*
