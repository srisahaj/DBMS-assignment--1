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

erDiagram
    PATIENT ||--o{ CONSULTATION : "HAS"
    DOCTOR ||--o{ CONSULTATION : "CONDUCTS"
    CONSULTATION ||--o{ PRESCRIPTION : "RESULTS_IN"
    PATIENT ||--o{ PRESCRIPTION : "RECEIVES"
    DOCTOR ||--o{ PRESCRIPTION : "PRESCRIBES"
    MEDICINE ||--o{ PRESCRIPTION : "CONTAINS"

    CONSULTATION {
        int ConsultationID
        date Date
    }

    PRESCRIPTION {
        int PrescriptionID
        date DatePrescribed
        string Dosage
        string Frequency
        string Duration
    }

    MEDICINE {
        int MedicineID
        string Name
        string Manufacturer
    }

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

erDiagram
    CUSTOMER ||--o{ ORDER : "PLACES"
    ORDER ||--|{ ORDER_LINE : "CONTAINS"
    ORDER_LINE ||--o{ SHIPMENT : "SHIPPED_BY"
    WAREHOUSE ||--o{ SHIPMENT : "FROM"
    PRODUCT ||--o{ ORDER_LINE : "INCLUDES"

    ORDER {
        int OrderID
        date OrderDate
        string ShippingAddress
    }

    ORDER_LINE {
        int OrderID
        int ProductID
        int Quantity
    }

    SHIPMENT {
        int ShipmentID
        string Carrier
        date DispatchDate
    }

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

erDiagram
    EMPLOYEE }o--|| DEPARTMENT : "BELONGS_TO"
    EMPLOYEE ||--o{ ASSIGNMENT : "WORKS_ON"
    PROJECT ||--o{ ASSIGNMENT : "HAS"
    PROJECT ||--o{ PROJECT_MANAGEMENT : "MANAGED_BY"
    EMPLOYEE ||--o{ PROJECT_MANAGEMENT : "MANAGES"

    ASSIGNMENT {
        int EmpID
        int ProjectID
        string Role
        string Period
        float HoursWorked
        date StartDate
        date EndDate
    }

    PROJECT_MANAGEMENT {
        int ProjectID
        int EmpID
        date StartDate
        date EndDate
    }

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

erDiagram
    FLIGHT ||--o{ FLIGHT_INSTANCE : "INSTANCE_OF"
    AIRCRAFT ||--o{ FLIGHT_INSTANCE : "USES"
    BOOKING }o--o{ PASSENGER : "HAS"
    BOOKING }o--o{ FLIGHT_INSTANCE : "INCLUDES"
    BOOKING ||--o{ SEGMENT : "CONTAINS"
    PASSENGER ||--o{ SEGMENT : "ASSIGNED_TO"
    FLIGHT_INSTANCE ||--o{ SEGMENT : "HAS"

    FLIGHT {
        string FlightNo
    }

    FLIGHT_INSTANCE {
        int InstanceID
        date Date
        time DepartureTime
    }

    BOOKING {
        string PNR
        date BookingDate
    }

    SEGMENT {
        string PNR
        int InstanceID
        int PassengerID
        string SeatNo
    }
