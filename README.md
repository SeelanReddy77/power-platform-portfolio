2) Emergency Medications Bag Inspection & Monitoring
Power Apps • SharePoint • Power Automate • Alerts • Compliance
A field-ready mobile inspection platform that ensures no expired or missing emergency medications. Medics record expiry dates, quantities, issues, and the app triggers notifications before stockouts/expiries occur.
Highlights

Bag types: Entonox, Oxygen, Medications, Response, LIFEPAK, EKG, LSU, LPD
Dynamic screens per bag type
Unique inspection session ID ties related records
Automated alerts: approaching expiry, missing items, low stock
Audit-ready logs for compliance and reporting
SharePoint Lists (Core)

MedicationBagCheck: BagType (Choice), BagID (Choice), Location (Choice), CheckedBy, CheckDate (UTC), UniqueRecord, BagImage
MedicationMedications: itemised expiries/quantities/notes per bag
MedicationOxygen / MedicationEntonox: gas-specific checks
MedicationUsageLog: decrement on use/loss
MedicationAlerts: generated alerts for review

Sample Power Apps Patterns
Generate Unique ID
Plain Textpowerapps isn’t fully supported. Syntax highlighting is based on Plain Text.Set(varUniqueRecord, Text(Now(), "yyyymmddhhmmss") & "-" & Substitute(varBagType," ","_") & "-" & Substitute(User().FullName," ","_"));Show more lines
UTC-Accurate Check Date
Plain Textpowerapps isn’t fully supported. Syntax highlighting is based on Plain Text.Set(varCheckDateLocal, Now());Set(varCheckDateUTC, DateAdd(varCheckDateLocal, -TimeZoneOffset(varCheckDateLocal), TimeUnit.Minutes));Show more lines
Dynamic Navigation by Bag Type
Plain Textpowerapps isn’t fully supported. Syntax highlighting is based on Plain Text.Switch(varBagType, "Entonox Bag",     Navigate(EntonoxScreen, ScreenTransition.Fade), "Oxygen Bag",      Navigate(OxygenScreen, ScreenTransition.Fade), "Medications Bag", Navigate(MedicationsScreen, ScreenTransition.Fade), "Response Bag",    Navigate(ResponseScreen, ScreenTransition.Fade), "LIFEPAK Bag",     Navigate(LifePakScreen, ScreenTransition.Fade), "EKG Bag",         Navigate(EKGScreen, ScreenTransition.Fade), "Laerdal Suction Units", Navigate(LSUScreen, ScreenTransition.Fade), "Lifepak 1000 Defibs Bag", Navigate(LPDScreen, ScreenTransition.Fade));Show more lines
Power Automate (Core Jobs)

Daily Recurrence: find items expiring soon or missing → send alerts
On Inspection Create: log to MedicationUsageLog, cascade updates
Rollups for management reporting

Setup (High-Level)

Provision the SharePoint lists named above (Choice fields for BagType/BagID/Location).
Build/import Power Apps screens per bag, wire Patch() to lists.
Add flows for expiry monitoring, low stock alerts, usage logs.
Test with staged Bag IDs and sample expireies.

Reuse Ideas

Hospital meds rooms, ambulance kits, first-aid stations, pharma warehouses.
