# Task 04 – AI Appointment Booking Assistant

## Objective
Build an AI-powered appointment booking assistant for a fictional medical clinic using Zapier Chatbots and Tables.

## Requirements
- The chatbot should understand natural language booking requests (e.g. “I want to see Dr. Sara tomorrow at 4 PM”).
- Extract Doctor, Date, and Time from the user’s message.
- Convert relative dates (like “tomorrow”) into actual dates.
- Collect Patient Name, Phone, and Reason.
- Create an appointment record in a structured table.
- Generate a unique Appointment ID.
- Prevent double booking for the same Doctor + Date + Time combination.
- Set Status to “Confirmed” when the appointment is created.

## Available Doctors
- Dr. Ahmed – General Physician
- Dr. Sara – Dermatologist
- Dr. Ali – Cardiologist

## Tools Used
- Zapier Chatbots
- Zapier Tables
- Code by Zapier
- Filter by Zapier
- AI by Zapier (for data cleaning)

## Test Cases

| Test Case | What was tested | Sample Input | Expected Result | Status |
|-----------|-----------------|--------------|-----------------|--------|
| TC-01 | Successful booking | Book an appointment with Dr. Ali tomorrow at 6:00 PM | Appointment is created in the Appointments table with a unique ID and Status = Confirmed | Passed |
| TC-02 | Different doctor, different time | Book an appointment with Dr. Sara tomorrow at 10:00 AM | A new appointment is created because the slot is free | Passed |
| TC-03 | Relative date conversion | I need to see Dr. Ahmed the day after tomorrow at 2:30 PM | “The day after tomorrow” is converted into a real date and the appointment is created | Passed |
| TC-04 | Double booking prevention | Book the same Doctor + Date + Time again | Zap finds the existing record and does not create a duplicate in the Appointments table | Passed |
| TC-05 | New slot with the same doctor | Book Dr. Sara at a different time on the same date | Appointment is created because the time is different | Passed |