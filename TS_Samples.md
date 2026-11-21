# Program (PROG) Samples

Programs are written in TypeScript in the program editor and called from a scheme with the `PROG` function (see [PROG](./Scheme_Functions.md#prog)).
The program type must match the scheme it is called from:

| Type | Scheme | Function to write | Return |
|---|---|---|---|
| `S` Screen | Screen scheme | `run(page: IPageDesign, container: IContainer)` | `{ continueNext, page, container }` |
| `D` Data | Data scheme (workflow forms, JOB) | `run(container: IDataContainer)` | `{ continueNext, container }` |
| `T` Time | Time evaluation scheme | `run(dayRow: ITimeContainerRow, dayRows: ITimeContainerRow[])` | `{ continueNext, dayRow }` |
| `P` Payroll | Payroll scheme | `run(container: IPayrollContainer, wpbp: IWPBPRow)` | `{ continueNext, container, wpbp }` |

Rules for all types:
- The program runs in a sandbox: `import`, `require`, `fetch`, `eval`, `process` and `globalThis` are not available.
- The program must finish in 2 seconds.
- The objects are copies. **Only the objects you return are written back**, so always return them (`page` / `container` / `dayRow` / `wpbp`).
- Stopping: `continueNext: false` stops the remaining rows only in **screen** schemes. In the other schemes:
  - Data: set `container.Stop` (see below).
  - Payroll: set `wpbp.HasError = true` and `wpbp.ErrorMessage`; the remaining rows are skipped for the employee and it is listed as errored.
  - Time: write the error to `dayRow.Message` and set `dayRow.HasError = true`.

- [Screen Programs (S)](#screen-programs-s)
- [Data Programs (D)](#data-programs-d)
- [Time Programs (T)](#time-programs-t)
- [Payroll Programs (P)](#payroll-programs-p)

---

# Screen Programs (S)

## Simple Validation Sample Code

```ts
// page: IPageDesign
// container: IContainer

function run(page: IPageDesign, container: IContainer) {
  const pernr = container.UserPernr;
  const lang = page.Langu;

  page.Messages = page.Messages || [];

  if (page.PageEvent == "Get") {
    //page loading
    container.zhr_01 = "ZHR_01 executed in page load";
  }
  else if (page.PageEvent == "Post") {
    //page posted by button
    if (page.PostFieldName == "DOSUBMIT") {
      var _elem = page.Elements.filter(o => o.FieldName == "PHOTOFILE")[0];
      //
      if (_elem == null) {
        page.Messages.push({
          Title: "Error",
          Text: `PHOTOFILE not found in form`,
          IsError: true
        });
        return {
          continueNext: false,          // opsiyonel (yoksa true kabul ediliyor)
          page,                        // page data
          container                    // container data
        };
      }
      else {
        if (pernr == '00000002') {
          page.Messages.push({
            Title: "Error",
            Text: `Employee 00000002 cannot post this form ` + _elem.Data,
            IsError: true
          });
          return {
            continueNext: false,         // if false, stops scheme execution
            page,                        // page data
            container                    // container data
          };
        }

      }
    }
  }


  return {
    continueNext: true,          
    page,                        // page data
    container                    // container data
  };
} 
```

## App Technical Name Check
```ts
 if (page.AppCode == "zides-unpaid-request-leave") {
  //write here
 }
```
## Show Error to User and Stop Executing Next Lines in Scheme
```ts
try
{
  //in some conditions..
  throw Error("No Manager Assigned");


}
catch(error)
{
    page.Messages = page.Messages || [];
    page.Messages.push({
    Title: "Logical error",
    Text: `${error}`,
    IsError: true
  });
  return {
    continueNext: true,  //do it to show the message to user
    page,                        // page data
    container                    // container data
  };
}
```

## Read an Element and Assign Value
```ts
  var _elemMANAGER = page.Elements.filter(o => o.FieldName === "MANAGER")[0];
  if (_elemMANAGER) {
    _elemMANAGER.Data = "Bekir Karadeniz";
  }
```
## Add Variable Value to Container
```ts
  container.zhr_01 = "ZHR_01 executed in page load";
```
## Read Page Events
```ts
  if (page.PageEvent == "Get") {
    //occurs when page loads
  }
  else if (page.PageEvent == "Post") {
    //occurs on button click
    if(page.PostFieldName =="DOSUBMIT")
    {
        container._Manager = "00000002";
        container._NewStatus = "SUBMITTED";
    }
  }
  else if (page.PageEvent == "PostBack") {
    //occurs on page element change
    if(page.PostBackField =="LAND1")
    {
      //LAND1 select has been changed
    }
  }
```

---

# Data Programs (D)

A data program gets the container of the data scheme. The form data is in `container.Data`; its dates are `YYYYMMDD` strings.
The `OrcDate` helpers work with these dates: `toDate`, `toYYYYMMDD`, `isValid`, `diffDays`, `addDays`, `dayOfWeek` (1 = Monday .. 7 = Sunday).

To block a request, set `container.Stop` to the message. The scheme stops and the task is not run.
`container.StopType` is `E` (default), `W` or `I`.

## Validate a Leave Request

```ts
function run(container: IDataContainer) {
  const data = container.Data || {};

  // only on submit
  if (container.PageEvent !== "Post" || container.PostFieldName !== "DOSUBMIT") {
    return { continueNext: true, container };
  }

  if (!OrcDate.isValid(data.Begda) || !OrcDate.isValid(data.Endda)) {
    container.Stop = "Başlangıç ve bitiş tarihi girilmelidir.";
    return { continueNext: true, container };
  }

  if (data.Endda < data.Begda) {
    container.Stop = "Bitiş tarihi başlangıç tarihinden önce olamaz.";
    return { continueNext: true, container };
  }

  // max days from the app parameter MAXDAY (e.g. "10")
  const maxDays = Number(container.AppParam?.["MAXDAY"] || 0);
  const days = OrcDate.diffDays(data.Begda, data.Endda) + 1;
  if (maxDays > 0 && days > maxDays) {
    container.Stop = `En fazla ${maxDays} gün talep edilebilir.`;
  }

  return { continueNext: true, container };
}
```

## Calculate Fields of the Form

```ts
function run(container: IDataContainer) {
  const data = container.Data || {};

  // recalculate when a date changes
  if (container.PageEvent === "PostBack" && ["BEGDA", "ENDDA"].includes(container.PostBackField || "")) {
    if (OrcDate.isValid(data.Begda)) {
      // return date: the day after the leave, Monday when it is on the weekend
      let ret = OrcDate.addDays(data.Endda || data.Begda, 1);
      while (OrcDate.dayOfWeek(ret) > 5) {
        ret = OrcDate.addDays(ret, 1);
      }
      data.ReturnDate = ret;
    }
    container.Data = data;
  }

  return { continueNext: true, container };
}
```

## Show a Warning without Stopping

```ts
function run(container: IDataContainer) {
  if (container.SysDay > 25) {
    container.Actions = container.Actions || [];
    container.Actions.push({ Action: "MESSAGE", ObjName: "Bu ayın bordrosu kapanmak üzere.", Param: "W" });
  }
  return { continueNext: true, container };
}
```

---

# Time Programs (T)

A time program runs for **one employee day** in the time evaluation scheme:
- `dayRow`: The day being evaluated (`Datum`, planned and actual hours, pairs, absences, wages ...).
- `dayRows`: All days of the employee in the run.

Time types are written to `dayRow.Wages` as `{ Lgart, Anzhl }` (`Anzhl` = hours or days). In a productive run they are saved to PA2021.
Every `Lgart` must be defined in the time types table (THRTM11), otherwise the run shows "Tanımsız Zaman Türleri" and does not save.

Useful fields:
- `DwsClass`: `"0"` = day off in the work schedule, otherwise a work day.
- `HolClass`: `"0"` = no public holiday, otherwise a public holiday.
- `PlannedHours`, `ActualHours`, `OvertimeHours` (unapproved), `AOvertimeHours` (approved), `AbsenceHours`, `MissingHours`.
- `APairs` / `RPairs`: Actual / rounded clock-in and clock-out pairs (`Begti`, `Endti` as `HH:MM`, `Hours`).
- `Pairs`: Pairs with a time class (`S` planned, `F` unapproved overtime, `M` approved overtime, `A` absence, `P` attendance, `X` public holiday ...).
- `Message`: Message of the day, shown in the result.

Place the `PROG` row after `TIMTP` (pairs are created there) and `GENWT`, e.g.:
```text
TOLER  ...
TIMTP
GENWT  ...
PROG   ZTM_WAGES
```

## Write Holiday and Weekend Work as Time Types

```ts
function addWage(dayRow: ITimeContainerRow, lgart: string, hours: number) {
  if (hours <= 0) return;
  const existing = dayRow.Wages.find(w => w.Lgart === lgart);
  if (existing) existing.Anzhl += hours;
  else dayRow.Wages.push({ Lgart: lgart, Anzhl: hours });
}

function run(dayRow: ITimeContainerRow, dayRows: ITimeContainerRow[]) {
  try {
    if (dayRow.ActualHours > 0) {
      if (dayRow.HolClass !== "0") {
        addWage(dayRow, "5080", dayRow.ActualHours);      // Resmi Tatil FM
      }
      else if (dayRow.DwsClass === "0") {
        addWage(dayRow, "5070", dayRow.ActualHours);      // Hafta Sonu FM
      }
    }

    // approved overtime on a work day
    if (dayRow.DwsClass !== "0" && dayRow.HolClass === "0" && dayRow.AOvertimeHours > 0) {
      addWage(dayRow, "5060", dayRow.AOvertimeHours);     // Hafta İçi FM
    }

    return { continueNext: true, dayRow };
  }
  catch (error) {
    dayRow.Message = `ZTM_WAGES: ${error}`;
    dayRow.HasError = true;
    return { continueNext: true, dayRow };
  }
}
```

## Night Work Hours (20:00 - 06:00)

```ts
const toMin = (hhmm: string) => {
  const [h, m] = hhmm.split(":").map(Number);
  return h * 60 + m;
};

// minutes of [beg, end) inside [from, to)
const overlap = (beg: number, end: number, from: number, to: number) =>
  Math.max(0, Math.min(end, to) - Math.max(beg, from));

function run(dayRow: ITimeContainerRow, dayRows: ITimeContainerRow[]) {
  let minutes = 0;

  for (const pair of dayRow.RPairs) {
    if (!pair.Begti || !pair.Endti || pair.HasError) continue;
    const beg = toMin(pair.Begti);
    let end = toMin(pair.Endti);
    if (end <= beg) end += 24 * 60;                       // pair goes over midnight

    minutes += overlap(beg, end, 0, 6 * 60);              // 00:00 - 06:00
    minutes += overlap(beg, end, 20 * 60, 30 * 60);       // 20:00 - 06:00 next day
  }

  if (minutes > 0) {
    dayRow.Wages.push({ Lgart: "5090", Anzhl: Math.round(minutes / 60 * 100) / 100 });  // Gece Çalışması (sample code)
  }

  return { continueNext: true, dayRow };
}
```

## Weekly Hours over 45 (uses dayRows)

Runs on Sundays and writes the hours over 45 of the week (Monday - Sunday). The days of the week must be in the same run.

```ts
function run(dayRow: ITimeContainerRow, dayRows: ITimeContainerRow[]) {
  const day = new Date(dayRow.Datum);
  if (day.getUTCDay() !== 0) {                            // 0 = Sunday
    return { continueNext: true, dayRow };
  }

  const weekStart = day.getTime() - 6 * 86400000;
  const weekHours = dayRows
    .filter(r => {
      const t = new Date(r.Datum).getTime();
      return t >= weekStart && t <= day.getTime();
    })
    .reduce((sum, r) => sum + (r.ActualHours || 0), 0);

  if (weekHours > 45) {
    dayRow.Wages.push({ Lgart: "5060", Anzhl: weekHours - 45 });   // Hafta İçi FM
    dayRow.Message = `Haftalık çalışma ${weekHours} saat`;
  }

  return { continueNext: true, dayRow };
}
```

---

# Payroll Programs (P)

A payroll program runs for **one employee (WPBP row)** in the payroll scheme:
- `container`: The payroll run (`Period`, `Begda`, `Endda`, `ActualMode`, `AllWages`, `TaxTable` ...).
- `wpbp`: The employee: org. data (`Bukrs`, `Werks`, `Persg`, `Persk`, `Orgeh` ...), `HireDate`, `LawNo` and the wage table `IT`.

Wage types are written to `wpbp.IT` as `{ Lgart, Betrg, Anzhl, Betpe }`. At the end of the run `IT` is the result (RT); in a productive run it is saved to PA3021.
Every `Lgart` must be defined in the wage types table (THRPY01). Its processing classes decide how CUMU, TRSSK and TRTAX use it (gross, SGK, tax, stamp tax).

Messages: `wpbp.ProgMessages.push("...")` or `console.log(...)` write to the scheme log.

Place the `PROG` row after the wage types it reads and **before the calculations** that should include the new wage type:
```text
P0008             Read Basic Pay
P0014             Read Recurring Pay/Ded
P0015             Read Add Pay/Ded
P2010             Read Remun Payments
P0769             Read SGK
TRSSKD            SSK Days
PROG   ZPY_BONUS  <- new gross wage types here (before PRORATA when they should be prorated)
PRORATA
TRNET  ...
CUMU
TRSSK
TRTAX
NET
```

Note: An employee with an org. change in the period has more than one WPBP row (`Apznr` = `01`, `02` ...). Add fixed monthly amounts only once, e.g. in the row `Apznr === "01"`.

## Seniority Bonus (Percentage of the Basic Pay)

10% of the monthly salary (`1000`) as bonus (`2010`) for employees with 5+ years of service.

```ts
function run(container: IPayrollContainer, wpbp: IWPBPRow) {
  if (wpbp.Apznr !== "01" || !wpbp.HireDate) {
    return { continueNext: true, container, wpbp };
  }

  const hire = new Date(wpbp.HireDate);
  const end = new Date(container.Endda);
  let years = end.getUTCFullYear() - hire.getUTCFullYear();
  if (end.getUTCMonth() < hire.getUTCMonth() ||
     (end.getUTCMonth() === hire.getUTCMonth() && end.getUTCDate() < hire.getUTCDate())) {
    years--;
  }

  const salary = wpbp.IT.find(w => w.Lgart === "1000");
  if (years >= 5 && salary?.Betrg) {
    const amount = Math.round(salary.Betrg * 10) / 100;   // 10%, 2 decimals
    wpbp.IT.push({ Lgart: "2010", Betrg: amount, Anzhl: 0, Betpe: 0 });
    wpbp.ProgMessages.push(`${wpbp.Pernr}: ${years} yıl kıdem, prim ${amount}`);
  }

  return { continueNext: true, container, wpbp };
}
```

## Fixed Allowance by Employee Subgroup

```ts
// travel allowance (2090) per employee subgroup
const TRAVEL: Record<string, number> = {
  "01": 1500,
  "02": 2000,
  "03": 2500
};

function run(container: IPayrollContainer, wpbp: IWPBPRow) {
  const amount = TRAVEL[wpbp.Persk];
  const exists = wpbp.IT.some(w => w.Lgart === "2090");   // e.g. entered in PA0014

  if (wpbp.Apznr === "01" && amount && !exists) {
    wpbp.IT.push({ Lgart: "2090", Betrg: amount, Anzhl: 0, Betpe: 0 });
  }

  return { continueNext: true, container, wpbp };
}
```

## Pay Time Types from the Time Evaluation

Pays the hours of a time type with the hourly rate of the monthly salary (salary / 225).
The time type must reach the payroll as a wage type in `IT`, e.g. through PA2010 (`P2010`), with the hours in `Anzhl`.

```ts
function run(container: IPayrollContainer, wpbp: IWPBPRow) {
  const salary = wpbp.IT.find(w => w.Lgart === "1000")?.Betrg || 0;
  const hours = wpbp.IT.find(w => w.Lgart === "5060")?.Anzhl || 0;   // overtime hours

  if (salary > 0 && hours > 0) {
    const rate = salary / 225;
    const amount = Math.round(hours * rate * 1.5 * 100) / 100;       // 50% overtime premium
    wpbp.IT.push({ Lgart: "2200", Betrg: amount, Anzhl: hours, Betpe: Math.round(rate * 150) / 100 });  // Fazla Mesai (sample code)
  }

  return { continueNext: true, container, wpbp };
}
```

## Stop the Employee with an Error

```ts
function run(container: IPayrollContainer, wpbp: IWPBPRow) {
  if (!wpbp.IT.some(w => w.Lgart === "1000")) {
    wpbp.HasError = true;
    wpbp.ErrorMessage = `${wpbp.Pernr}: Aylık maaş (1000) bulunamadı`;   // remaining rows are skipped for this employee
    return { continueNext: true, container, wpbp };
  }
  return { continueNext: true, container, wpbp };
}
```
