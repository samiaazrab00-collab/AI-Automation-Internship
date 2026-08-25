# How the Automation Works

1. User interacts with the Clinic Appointment Assistant chatbot.
2. The bot identifies Doctor, Date, and Time from the user’s message.
3. It converts relative dates (like “tomorrow”) into a real date.
4. The bot collects Patient Name, Phone, Reason, Doctor, Date, and Time using a form.
5. A new record is created in the “Clinic Appointment Assistant Collected Data” table.
6. The Zap is triggered:
   - Code by Zapier generates a unique Appointment ID (APT-YYYYMMDD-HHMM)
   - Time is standardized for consistent matching
   - Find Records checks if the same Doctor + Date + Time already exists
   - Filter stops the Zap if a matching record is found (prevents double booking)
   - If the slot is free, a new record is created in the Appointments table with Status = Confirmed

## Key Features
- Natural language understanding
- Relative date conversion
- Unique Appointment ID generation
- Double booking prevention using Find Records + Filter
- Structured storage in Zapier Tables