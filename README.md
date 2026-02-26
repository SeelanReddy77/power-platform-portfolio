Clock‑In / Clock‑Out & Expense Vouching
Power Apps • Geolocation • Timezone Handling • SharePoint • Digital Signatures
A robust mobile timekeeping app that prevents double clock‑in, captures GPS, handles local/UTC, calculates hours, supports previous day logs, uploads receipt images, and captures signatures—all stored in SharePoint for payroll and audits.
Highlights

Double‑clock‑in protection
Local time + correct ShiftDate (no UTC drift)
GPS capture at clock‑in/out
Past day logging (date/time reconstruction)
Expense image + signature capture
Hours worked calculation (capped to avoid anomalies)

Architecture (Mermaid)

SharePoint List: Clock In Clock Out
Columns: EmployeeName, ClockInTime, ClockOutTime, ShiftDate, HoursWorked, WorkSite, Location (lat,long), VouchedExpenses (image), VouchedExpensesTotal, Signature (image), OvernightAllowance (bool), Status (Choice), Unique (Text), SubmissionDate.
Key Power Apps Formulas
Prevent Double Clock‑In
Plain Textpowerapps isn’t fully supported. Syntax highlighting is based on Plain Text.Set(ExistingClockInRecord, LookUp('Clock In Clock Out', EmployeeName = User().FullName && Status = "Clocked In"));If(!IsBlank(ExistingClockInRecord), Notify("You are already clocked in. Please clock out first.", NotificationType.Error), /* proceed */)Show more lines
Clock‑In (Local Time, ShiftDate, Unique)
MermaidNo diagram type detected matching given configuration for text: Set(CurrentLocation, Location);
Set(CurrentTime, Now());
Set(CorrectedShiftDate, Date(Year(CurrentTime), Month(CurrentTime), Day(CurrentTime)));
Set(UniqueValue, User().FullName & "_" & Text(CurrentTime, "yyyyMMdd_HHmmss"));

Set(ClockInRecord,
  Patch('Clock In Clock Out', Defaults('Clock In Clock Out'),
  {
    EmployeeName: User().FullName,
    ClockInTime: CurrentTime,
    Location: If(!IsBlank(CurrentLocation.Latitude) && !IsBlank(CurrentLocation.Longitude),
                 Text(CurrentLocation.Latitude) & ", " & Text(CurrentLocation.Longitude),
                 "Location unavailable"),
    Status: "Clocked In",
    SubmissionDate: CurrentTime,
    Unique: UniqueValue,
    ShiftDate: CorrectedShiftDate,
    OvernightAllowance: false
  })
);
Set(ClockInID, UniqueValue);Set(CurrentLocation, Location);Set(CurrentTime, Now());Set(CorrectedShiftDate, Date(Year(CurrentTime), Month(CurrentTime), Day(CurrentTime)));Set(UniqueValue, User().FullName & "_" & Text(CurrentTime, "yyyyMMdd_HHmmss"));Set(ClockInRecord,  Patch('Clock In Clock Out', Defaults('Clock In Clock Out'),  {    EmployeeName: User().FullName,    ClockInTime: CurrentTime,    Location: If(!IsBlank(CurrentLocation.Latitude) && !IsBlank(CurrentLocation.Longitude),                 Text(CurrentLocation.Latitude) & ", " & Text(CurrentLocation.Longitude),                 "Location unavailable"),    Status: "Clocked In",    SubmissionDate: CurrentTime,    Unique: UniqueValue,    ShiftDate: CorrectedShiftDate,    OvernightAllowance: false  }));Set(ClockInID, UniqueValue);Show more lines
Clock‑Out (Hours Worked + GPS)
MermaidNo diagram type detected matching given configuration for text: Set(CurrentLocation, Location);
Set(ExistingClockInRecord, LookUp('Clock In Clock Out', EmployeeName = User().FullName && Status = "Clocked In"));
If(IsBlank(ExistingClockInRecord), Notify("You need to clock in first.", NotificationType.Error),
  Set(HoursWorkedCalc, Min(24, Round(DateDiff(ExistingClockInRecord.ClockInTime, Now(), TimeUnit.Minutes)/60, 2)));

  Patch('Clock In Clock Out', ExistingClockInRecord,
    {
      ClockOutTime: Now(),
      Location: If(!IsBlank(CurrentLocation.Latitude) && !IsBlank(CurrentLocation.Longitude),
                   Text(CurrentLocation.Latitude) & ", " & Text(CurrentLocation.Longitude),
                   "Location unavailable"),
      HoursWorked: HoursWorkedCalc,
      Status: "Clocked Out"
    }
  );
  Notify("Clock-out successful! Hours worked today: " & Text(HoursWorkedCalc, "[$-en-US]0.00") & " hours", NotificationType.Success);
);Set(CurrentLocation, Location);Set(ExistingClockInRecord, LookUp('Clock In Clock Out', EmployeeName = User().FullName && Status = "Clocked In"));If(IsBlank(ExistingClockInRecord), Notify("You need to clock in first.", NotificationType.Error),  Set(HoursWorkedCalc, Min(24, Round(DateDiff(ExistingClockInRecord.ClockInTime, Now(), TimeUnit.Minutes)/60, 2)));  Patch('Clock In Clock Out', ExistingClockInRecord,    {      ClockOutTime: Now(),      Location: If(!IsBlank(CurrentLocation.Latitude) && !IsBlank(CurrentLocation.Longitude),                   Text(CurrentLocation.Latitude) & ", " & Text(CurrentLocation.Longitude),                   "Location unavailable"),      HoursWorked: HoursWorkedCalc,      Status: "Clocked Out"    }  );  Notify("Clock-out successful! Hours worked today: " & Text(HoursWorkedCalc, "[$-en-US]0.00") & " hours", NotificationType.Success););Show more lines
Upload Expense Image + Save Job Details
Plain Textpowerapps isn’t fully supported. Syntax highlighting is based on Plain Text.// Upload receipt to the active shift recordIf(!IsBlank(ClockInID),  Set(ClockInRecord, First(Filter('Clock In Clock Out', Unique = ClockInID)));  If(!IsBlank(ClockInRecord),    Patch('Clock In Clock Out', ClockInRecord, { VouchedExpenses: VouchedImage.Image })  ));// Save details + signatureIf(!IsBlank(ClockInID),  Set(ClockInRecord, First(Filter('Clock In Clock Out', Unique = ClockInID)));  If(!IsBlank(ClockInRecord),    Patch('Clock In Clock Out', ClockInRecord,      {        VouchedExpenses: VouchedImage.Image,        Signature: sigSignature.Image,        MainRole: MainRoleDropdown.Selected.Value,        AdditionalInfoInput: AdditionalInfoInput.Text,        WorkSite: WorkSiteInput.Text,        VouchedExpensesTotal: Value(VouchedExpensesTotal.Text),        OvernightAllowance: tglOvernightAllowance.Value      }    )  ));Show more lines
Setup (High-Level)

Create SharePoint list Clock In Clock Out with the columns above.
Build/import the Power App; wire up formulas and screens.
(Optional) Add Power Automate exports, reminders (e.g., missing clock‑out).
Create views for Payroll Period / Open Clock‑Ins.

Reuse Ideas

Construction sites, field services, healthcare shifts, logistics, security patrols, consulting timesheets.
