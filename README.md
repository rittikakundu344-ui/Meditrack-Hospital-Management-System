# MediTrack Hospital Management Database

MediTrack is a MySQL-based hospital management database project for organizing patient information, doctor profiles, appointments, prescriptions, and billing records.

## Project Overview

The database is named `MediTrack` and was developed using MySQL Shell 26.7.1 with MySQL Server 8.0.46.

### Database Objects

The supplied project schema includes five tables:

| Table | Purpose |
|---|---|
| `Patients` | Stores patient demographic and contact information. |
| `Doctors` | Stores doctor names, specializations, consultation fees, and experience. |
| `Appointments` | Connects patients with doctors and records appointment dates and statuses. |
| `Prescriptions` | Stores medicines, dosage, and duration associated with appointments. |
| `Billing` | Stores appointment charges, payment methods, and payment statuses. |

The architecture report also documents two views, `HighValueBills` and `RevenueSummary`, and indexes including `idx_patient_id` and a unique email index on `Patients`.

## Entity Relationships

- One patient can have multiple appointments.
- One doctor can attend multiple appointments.
- One appointment can have multiple prescriptions.
- The current schema permits multiple billing records for one appointment because `Billing.Appointment_ID` is not unique.

Foreign keys connect:
- `Appointments.Patient_ID` → `Patients.Patient_ID`
- `Appointments.Doctor_ID` → `Doctors.Doctor_ID`
- `Prescriptions.Appointment_ID` → `Appointments.Appointment_ID`
- `Billing.Appointment_ID` → `Appointments.Appointment_ID`

## Requirements

- MySQL Server 8.0 or compatible
- MySQL Shell (`mysqlsh`) or another MySQL client

## Setup

1. Start the MySQL Server service.
2. Open MySQL Shell.
3. Connect to the local server:

   ```text
   \connect root@localhost:3306
   ```

4. Create and select the database:

   ```sql
   CREATE DATABASE MediTrack;
   USE MediTrack;
   ```

5. Run the table creation and data insertion SQL statements from the supplied SQL transcript (`hosptal management_ 27-10-2026.txt`).

> **Note:** If the database or tables already exist, do not rerun the creation statements unchanged. Use a clean database or adapt the script to avoid “already exists” errors.

## Example Queries

Show all patients:

```sql
SELECT * FROM Patients;
```

List appointments after a specified date:

```sql
SELECT *
FROM Appointments
WHERE Appointment_Date > '2024-01-08';
```

Find the medicines prescribed to a patient:

```sql
SELECT pr.Medicine
FROM Prescriptions AS pr
JOIN Appointments AS a
  ON pr.Appointment_ID = a.Appointment_ID
JOIN Patients AS p
  ON a.Patient_ID = p.Patient_ID
WHERE p.Name = 'Rohit Sharma';
```

Calculate total revenue by payment method:

```sql
SELECT Payment_Method, SUM(Amount) AS Total_Revenue
FROM Billing
GROUP BY Payment_Method;
```

Find the five doctors with the highest consultation fees:

```sql
SELECT *
FROM Doctors
ORDER BY Fee DESC
LIMIT 5;
```

## Current Scope and Limitations

According to the supplied architecture report, the database covers core outpatient workflows but does not currently define dedicated tables for:

- Inpatient admissions and discharges
- Wards, rooms, and bed allocation
- Laboratory tests and diagnostic results
- Pharmacy inventory and supplier/batch tracking
- User authentication, roles, and access control

The `Prescriptions` table stores medicine names as free text rather than referencing a medicine master table. Several non-primary-key fields allow `NULL`, and billing does not enforce one bill per appointment at the database level.

## Possible Improvements

- Add appropriate `NOT NULL` constraints to required fields.
- Add a medicine master table and reference it from `Prescriptions`.
- Standardize appointment and payment status values using lookup tables or suitable constraints.
- Decide whether each appointment should have one bill; if so, add a unique constraint to `Billing.Appointment_ID` after checking existing data.
- Define appropriate foreign-key update/delete behavior based on business rules.
- Add authentication and role-based access control before using the database in an application.
- Avoid using real patient data in public repositories; use synthetic or anonymized sample records.

## Project Files

The materials provided for this project include:

- `hosptal management_ 27-10-2026.txt` — MySQL Shell transcript with SQL statements and query output.
- `meditrack_database_architecture_report.docx` — Database architecture and schema audit report.
- `NotebookLM Mind Map.png` — Mind-map image.
- `MediTrack_Merged_Project_Document.docx` — Combined document containing the mind map, architecture report, and shell transcript.

## Disclaimer

This is a database project/demo, not a production-ready hospital information system. It should not be used to store real patient information without appropriate security, privacy, validation, backup, and access-control measures.
