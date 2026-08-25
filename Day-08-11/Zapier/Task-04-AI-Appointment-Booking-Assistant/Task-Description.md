# Task 04 – AI Appointment Booking Assistant

## Task Description
Build an AI chatbot for a fictional medical clinic that can book appointments through natural conversation.

The chatbot:
- Understands natural language requests (e.g. “I want to see Dr. Sara tomorrow at 4 PM”)
- Extracts Doctor, Date, and Time
- Converts relative dates like “tomorrow” into actual dates
- Collects Patient Name, Phone, and Reason
- Creates an appointment record in Zapier Tables
- Generates a unique Appointment ID
- Prevents double booking for the same Doctor + Date + Time
- Sets Status to Confirmed

## Components
- **Chatbot**: Clinic Appointment Assistant
- **Table**: Appointments
- **Table**: Clinic Appointment Assistant Collected Data
- **Zap**: Clinic Appointment Booking

## Test Results

**Test 1 – Successful Booking**
- Patient: Hajra
- Doctor: Dr. Ali
- Date: 26/08/2026
- Time: 6:00 PM
- Result: Appointment created successfully

**Test 2 – Successful Booking**
- Patient: Alia
- Doctor: Dr. Sara
- Date: 26/08/2026
- Time: 10:00 AM
- Result: Appointment created successfully

**Test 3 – Relative Date Handling**
- Patient: samia
- Doctor: Dr. Ahmed
- Request: “the day after tomorrow at 2:30 PM”
- Result: Correctly converted and booked for 27/08/2026

**Test 4 – Double Booking Prevention**
- Patient: amna
- Doctor: Dr. Ali
- Date: 26/08/2026
- Time: 6:00 PM
- Attempt: Tried to book the same slot again
- Result: Zap correctly blocked the duplicate (no new record created in the Appointments table)