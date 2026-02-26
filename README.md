1) Emergency Rescue‑Planning Automation
Power Apps • Power Automate • SharePoint • Word to PDF Generation • Image Embedding
A mobile app used by emergency medics to perform onsite risk assessments and auto‑generate a professional rescue plan (Word → PDF) with dynamic images, stored in SharePoint and automatically emailed to clients and internal teams.
Highlights

Mobile, guided data capture (hazards, access routes, classifications, images)
SharePoint as the single source of truth (lists + attachments)
Power Automate maps all fields (incl. multi-choice) to a Word template, converts to PDF, and distributes via email
Full audit trail and standardised report format
SharePoint Lists

RescuePlan: Worksite, AccessRoute, Risks, RescueMethod, Classification, Technician, Date, ClientEmail, etc.
Attachments: Site photos uploaded from Power Apps (used in template).

Power Automate Flow (Core Stages)

Trigger: When item is created/modified in RescuePlan
Get attachments → Filter array by expected names
Get attachment content (base64 for images)
Populate Microsoft Word template: map all content controls
Create file → Convert to PDF
Email final PDF to Client + ERT team

Power Apps Notes

Structured screens with dropdowns/text/choices
Validation to prevent missing critical data
Consistent image naming for template mapping

Setup (High-Level)

Create SharePoint list RescuePlan (columns for all fields).
Prepare Word template with content controls (text + image placeholders).
Build/import Power Apps (connect to SharePoint).
Configure Power Automate flow (map fields, images, conversion, email).
Test with sample assessment + attachments.

Reuse Ideas

Safety plans, incident forms, site surveys, insurance assessments, HSE reports.
