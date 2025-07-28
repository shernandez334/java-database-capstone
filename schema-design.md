# Schema Design

## MySQL Database Design

The MySQL portion of this system handles structured and interrelated clinical data: users, appointments, and system operations. Below are the essential tables and their structure.

---

### Table: patients
- `id`: INT, Primary Key, AUTO_INCREMENT
- `first_name`: VARCHAR(50), NOT NULL
- `last_name`: VARCHAR(50), NOT NULL
- `email`: VARCHAR(100), UNIQUE, NOT NULL
- `phone_number`: VARCHAR(15), NOT NULL
- `date_of_birth`: DATE
- `created_at`: DATETIME, DEFAULT CURRENT_TIMESTAMP

> Email is unique for login. Additional validation (e.g., regex) is handled in code. Appointments are linked to patients, so consider restricting deletion or using cascading delete based on business rules.

---

### Table: doctors
- `id`: INT, Primary Key, AUTO_INCREMENT
- `first_name`: VARCHAR(50), NOT NULL
- `last_name`: VARCHAR(50), NOT NULL
- `specialization`: VARCHAR(100), NOT NULL
- `email`: VARCHAR(100), UNIQUE, NOT NULL
- `phone_number`: VARCHAR(15)
- `available_from`: TIME
- `available_to`: TIME

> Doctors may not be allowed overlapping appointments. Time availability may be used for appointment validation logic.

---

### Table: appointments
- `id`: INT, Primary Key, AUTO_INCREMENT
- `doctor_id`: INT, Foreign Key → `doctors(id)`
- `patient_id`: INT, Foreign Key → `patients(id)`
- `appointment_time`: DATETIME, NOT NULL
- `status`: INT DEFAULT 0  
  (0 = Scheduled, 1 = Completed, 2 = Cancelled)
- `created_at`: DATETIME, DEFAULT CURRENT_TIMESTAMP

> Appointments are tied to both doctor and patient. Consider adding unique constraints to prevent overlapping appointments for the same doctor at the same time.

---

### Table: admin
- `id`: INT, Primary Key, AUTO_INCREMENT
- `username`: VARCHAR(50), UNIQUE, NOT NULL
- `password_hash`: VARCHAR(255), NOT NULL
- `created_at`: DATETIME, DEFAULT CURRENT_TIMESTAMP

> Passwords are stored hashed (e.g., BCrypt). Admins manage doctors and view system reports.

---

### Optional Tables

#### Table: clinic_locations
- `id`: INT, Primary Key, AUTO_INCREMENT
- `name`: VARCHAR(100), NOT NULL
- `address`: TEXT, NOT NULL

> Allows multi-location expansion. Doctors or appointments could reference a clinic via `clinic_id`.

#### Table: payments
- `id`: INT, Primary Key, AUTO_INCREMENT
- `appointment_id`: INT, Foreign Key → `appointments(id)`
- `amount`: DECIMAL(10,2)
- `payment_date`: DATETIME
- `payment_method`: VARCHAR(50)

> Optional financial tracking for monetized services.

---

## MongoDB Collection Design

MongoDB is used to store flexible, non-relational data such as prescriptions. These records may include optional fields, nested structures, or change frequently — making them ideal for a NoSQL format.

---

### Collection: prescriptions

```json
{
  "_id": "ObjectId('64abc123456')",
  "patientId": 17,
  "appointmentId": 51,
  "doctorId": 4,
  "medications": [
    {
      "name": "Paracetamol",
      "dosage": "500mg",
      "instructions": "Take 1 tablet every 6 hours"
    },
    {
      "name": "Ibuprofen",
      "dosage": "200mg",
      "instructions": "After meals only"
    }
  ],
  "doctorNotes": "Monitor for any allergic reactions. Return in 7 days.",
  "issuedAt": "2025-07-28T10:00:00Z",
  "pharmacy": {
    "name": "City Pharmacy",
    "location": "123 Main Street"
  },
  "tags": ["flu", "pain-relief", "short-term"],
  "refillCount": 2
}
