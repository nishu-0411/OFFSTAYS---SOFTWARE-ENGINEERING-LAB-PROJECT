# OFFSTAYS---SOFTWARE-ENGINEERING-LAB-PROJECT

OffStays – Home & Experience Booking Platform

OffStays is a web application for booking short stays such as villas, cottages and apartments, similar in idea to Airbnb. A guest can create an account, search stays, see the full price for their dates, book with a simulated payment, cancel a booking, write a review and message the host. Every action is recorded in a MySQL database, and an admin can view and export that record.

This is our first full software project, built for the Software Engineering Laboratory (24CSB302P) at Pandit Deendayal Energy University, School of Technology, Department of Computer Science and Business Systems (Academic Year 2026-27, Division 2, Batch J4).

Note: This is an academic demo. Payments are simulated (no real money is taken), host replies are automatic, and the app runs locally on one computer.

Team
Roll No.	Name	Main work area
24BCB099	Aadi Desai	Streamlit pages and screens
24BCB100	Nisarg Trivedi	MySQL tables, sample data and SQL scripts
24BCB101	Tirth Vyas	Testing and documentation

Faculty: Komal Singh

What the app does
Accounts: register, log in, log out, edit profile, change password. The account is blocked for 15 minutes after 5 wrong passwords.
Explore Stays: 100 sample stays in 14 destinations. Filter by destination, area, dates, guests, category, price, pool, Wi-Fi and Superhost. Sort the results and see them on a map. Stays already booked for your dates are hidden.
Property details: photos, amenities, host info, reviews and the full price (nights × rate + $45 cleaning + $40 service + 10% tax).
Checkout: simulated Card or UPI payment with input checks (16-digit card, expiry not passed, 3-digit CVV, valid UPI ID). Only the last 4 card digits are stored.
My Trips: upcoming and past stays, check-in guide with keyless PIN, text invoice, cancellation with a refund rule (full refund 48+ hours before check-in, otherwise 50% of the stay cost + cleaning fee), reviews for completed stays, wishlist and notifications.
Messages: message the host and get an automatic reply (PIN, airport pickup, check-in time).
Become a Host: submit a host application.
Admin Dashboard (admin only): platform figures, a searchable activity log with CSV export, and a list of users.

Not included in this version: real payments, hosts adding their own listings, host approval of bookings, email/SMS notifications, "forgot password", and Android/iOS apps. These are listed as future work.

Built with
Part	Technology
Frontend (web pages)	Python 3.12 + Streamlit
Backend (logic)	Python modules: auth.py, pricing.py, activity.py, database.py
Database	MySQL 8 (16 tables, 5 views), accessed with PyMySQL
Tests	Python unittest
Project structure
text
OffStays/
├── app.py                  # Start here: database check, login screen, sidebar, page switching
├── auth.py                 # Register / login / logout, password hashing, lockout
├── pricing.py              # Night count and price breakdown
├── activity.py             # Records user actions in the activity log
├── database.py             # All SQL queries (parameterised)
├── db_config.py            # MySQL host, port, user, database name (password comes from an environment variable)
├── page_login.py           # Log in / Create account
├── page_explore.py         # Search, filters, map, stay cards
├── page_details.py         # Property details and Reserve Stay
├── page_checkout.py        # Simulated card / UPI payment
├── page_trips.py           # My Trips: bookings, cancel, invoice, reviews, notifications
├── page_messages.py        # Messages with automatic host replies
├── page_profile.py         # Profile, password change, my activity
├── page_host.py            # Become a Host application
├── page_admin.py           # Admin dashboard
├── setup_database.py       # Creates the database from the SQL scripts
├── requirements.txt
├── sql/                    # 01_schema, 02_seed_data, 03_load_csv, 04_views_and_reports
├── data/                   # 16 CSV files with the sample data
├── tools/csv_to_sql.py     # Rebuilds the SQL data scripts from the CSV files
├── tests/                  # Unit and integration tests
└── TESTING.md              # How to run the tests and what they cover
How to run it
1. Install
Python 3.12 (python.org). On Windows, tick "Add python.exe to PATH" during install.
MySQL Server 8 and MySQL Workbench. Remember the root password you choose.
2. Download the project

Click Code → Download ZIP on this page and unzip it, or run git clone. Open a terminal inside the OffStays folder.

3. Create a virtual environment and install the packages

Windows (PowerShell)

powershell
py -3.12 -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt

If you see "running scripts is disabled", run Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned once and try again.

macOS / Linux

bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
4. Create the database

In MySQL Workbench, open and run (⚡) these scripts in order:

sql/01_schema.sql creates the offstays database and 16 tables.
sql/02_seed_data.sql adds the sample data.
sql/04_views_and_reports.sql adds views and reports (optional).

Or, after step 5, run python setup_database.py instead.

Running 01_schema.sql again deletes the database and starts fresh.

5. Give the app your MySQL password

The password is not stored in the code. Set it in the terminal before starting the app:

powershell
# Windows PowerShell (this terminal only)
$env:OFFSTAYS_DB_PASSWORD="your_mysql_password"
bash
# macOS / Linux
export OFFSTAYS_DB_PASSWORD='your_mysql_password'
6. Start the app
bash
streamlit run app.py

The browser opens at http://localhost:8501. Stop the app with Ctrl + C.

7. Log in with a demo account
Role	Email	Password
Guest	sarah.vance@example.com	Password@123
Admin	admin@offstays.com	Admin@123

Every other sample user also uses Password@123. You can also create your own account.

Running the tests

No database is needed for these:

bash
python -m unittest discover -s tests/unit -t . -v                 # 147 unit tests
python -m unittest discover -s tests/integration_mocked -t . -v   # 20 integration tests

There are 38 more tests in tests/integration_db that use a separate MySQL test database called offstays_test, which they create and delete themselves. They never touch the offstays database. To run them, copy tests/test_database.example.ini to tests/test_database.ini and fill in your MySQL password. That file is in .gitignore, so it is never uploaded. See TESTING.md for details.

Checking the data in MySQL

After using the app, run this in Workbench to see the latest actions:

sql
USE offstays;
SELECT a.created_at, u.email, a.activity_type, a.status, a.description
FROM activity_logs a
LEFT JOIN users u ON u.user_id = a.user_id
ORDER BY a.log_id DESC
LIMIT 10;
Common problems
Problem	Fix
requirements.txt not found	Your terminal is not inside the OffStays folder
"MySQL rejected the username/password"	Set OFFSTAYS_DB_PASSWORD again in this terminal (step 5)
"Cannot reach MySQL server"	Start the MySQL service (Windows: services.msc → MySQL80 → Start)
"Database 'offstays' does not exist"	Run the SQL scripts (step 4)
ModuleNotFoundError	Activate the virtual environment and install the packages again (step 3)
A change in the code does not show	Stop with Ctrl + C and run streamlit run app.py again
Photos or the map are empty	They load from the internet; check your connection
Future work
Let hosts log in, add and edit their own listings, and approve booking requests.
Connect a real payment gateway in test mode instead of the simulated payment.
Add "forgot password" with an email reset link, and email notifications.
Run the database tests regularly.
Put the app online so it can be used without a local setup.
