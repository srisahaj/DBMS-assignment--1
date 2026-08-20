# DBMS Lab — ER Forensics

# Question 1 — University Course Registration

The main problem is that Course is being used for both the course catalog and the actual course offering. A course can be offered in different semesters and can have multiple sections, so we need a separate "Section" entity.

Issues & Fixes

1. Course stores Semester and Year
   This doesn't allow the same course to be offered in different semesters or years.
   Fix: Create "Section (SectionID, SectionNo, Semester, Year)" and connect it to "Course".

2. Students enroll directly in Course
   This doesn't tell us which section a student joined.
   Fix: "Student → ENROLLS → Section".

3. Instructors teach Course directly
   Different sections can have different instructors.
   Fix: "Instructor → TEACHES → Section".

4. Classroom is connected incorrectly
   A classroom should belong to a particular section, not an instructor-course pair.
   Fix: "Section → HELD_IN → Classroom".

erDiagram
    COURSE ||--o{ SECTION : "OFFERING_OF"
    STUDENT }o--o{ SECTION : "ENROLLS"
    INSTRUCTOR }o--o{ SECTION : "TEACHES"
    SECTION }o--|| CLASSROOM : "HELD_IN"

    COURSE {
        int CourseID
        string Title
        int Credits
    }

    SECTION {
        int SectionID
        int SectionNo
        string Semester
        int Year
    }

---

# Question 2 — Hospital Prescription System

The main problem is that the model doesn't properly represent individual consultations and prescriptions.

Issues & Fixes

1. Consultation is missing
   A patient can visit the same doctor many times, but the model can't distinguish the visits.
   Fix: Add "Consultation (ConsultationID, Date)".

2. Dosage is stored in Medicine
   Dosage can be different for different patients and visits.
   Fix: Store "Dosage", "Frequency", and "Duration" in "Prescription".

3. Prescribing and taking are separate
   There is no way to know which doctor prescribed a medicine to which patient.
   Fix: Create a "Prescription" entity connecting Doctor, Patient, and Medicine.

4. Prescription isn't linked to a visit
   We need to know which consultation resulted in the prescription.
   Fix: "Consultation → RESULTS_IN → Prescription".

Patient ──┐
          ▼
    Consultation
          │
     RESULTS_IN
          ▼
     Prescription
       ▲      ▲
       │      │
    Doctor  Medicine

---

# Question 3 — E-Commerce Order Fulfilment

The main problem is that order-specific information is stored at the wrong level.

Issues & Fixes

1. ShippingAddress is on Customer
   A customer may use different addresses for different orders.
   Fix: Move it to "Order".

2. Quantity is on Product
   The same product can have different quantities in different orders.
   Fix: Create "OrderLine (OrderID, ProductID, Quantity)".

3. Shipment is connected to the whole Order
   An order can be split into multiple shipments.
   Fix: Connect "Shipment" to "OrderLine".

4. ShipmentDate is on Order
   Different shipments can have different dispatch dates.
   Fix: Move it to "Shipment" as "DispatchDate".

Customer
   │
  PLACES
   ▼
Order
   │
CONTAINS
   ▼
OrderLine
   │
SHIPPED_BY
   ▼
Shipment ── FROM ──► Warehouse

---

# Question 4 — Project Staffing and Roles

The main problem is that information which depends on a particular employee-project assignment is stored on the wrong entities.

Issues & Fixes

1. HoursWorked is on Project
   Hours should be tracked for each employee and project.
   Fix: Create "Assignment".

2. Employees can't leave and rejoin a project
   The relationship has no dates.
   Fix: Add "StartDate" and "EndDate" to "Assignment".

3. Role is stored on Employee
   An employee can have different roles on different projects.
   Fix: Move "Role" to "Assignment".

4. Project manager history is missing
   The model doesn't track who manages a project or when.
   Fix: Add "ProjectManagement (ProjectID, EmpID, StartDate, EndDate)".

Employee ──► Department

Employee ◄── Assignment ──► Project
             │
             ├── Role
             ├── HoursWorked
             ├── StartDate
             └── EndDate

Project ◄── ProjectManagement ──► Employee

---

# Question 5 — Airline Booking and Seat Assignment

The main problem is that the model mixes up a flight schedule with a specific flight on a particular date.

Issues & Fixes

1. Flight mixes schedule and actual flight
   The same flight number can operate on many dates.
   Fix: Separate "Flight" and "FlightInstance".

2. Booking/PNR is missing
   A booking can contain multiple passengers and flight segments.
   Fix: Add "Booking (PNR, BookingDate)".

3. Aircraft is connected to Flight
   Different dates can use different aircraft.
   Fix: Connect Aircraft to "FlightInstance".

4. Seat assignment is incorrect
   A passenger can have different seats on different flight segments.
   Fix: Add "Segment (PNR, InstanceID, PassengerID, SeatNo)".

Flight
  │
INSTANCE_OF
  ▼
FlightInstance ── USES ──► Aircraft
       │
       ▼
    Booking
       │
       ▼
    Segment
       ▲
       │
   Passenger
