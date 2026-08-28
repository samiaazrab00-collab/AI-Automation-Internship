# Explanation – AI Appointment Booking Assistant

## Overview
I built a complete appointment booking system using Zapier Chatbots and Tables. The system allows users to book appointments through natural conversation and automatically creates structured records while preventing double bookings.

## How the System Works

1. **Chatbot Conversation**
   - The user starts a conversation with the Clinic Appointment Assistant.
   - The bot identifies the Doctor, Date, and Time from natural language.
   - It converts relative dates (e.g. “tomorrow”, “the day after tomorrow”) into real dates.
   - It then collects Patient Name, Phone, Reason, Doctor, Date, and Time using a form.

2. **Data Collection**
   - All collected information is stored in the “Clinic Appointment Assistant Collected Data” table.
   - This table acts as the trigger for the automation.

3. **Automation (Zap)**
   - **Trigger**: New record in the Collected Data table.
   - **Code by Zapier**: Generates a unique Appointment ID (format: APT-YYYYMMDD-HHMM).
   - **Time Standardization**: Normalizes the Time format for consistent matching.
   - **Find Records**: Checks if an appointment with the same Doctor + Date + Time already exists.
   - **Filter**: Stops the Zap if a matching record is found (prevents double booking).
   - **Create Record**: Creates a new appointment in the Appointments table only if the slot is free.
   - Status is set to “Confirmed”.

## Key Features Implemented
- Natural language understanding
- Relative date conversion
- Unique Appointment ID generation
- Double booking prevention using Find Records + Filter
- Structured data storage in Zapier Tables

## Result
The system successfully creates appointment records and blocks duplicate bookings for the same Doctor, Date, and Time combination.