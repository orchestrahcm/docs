# Scheme Functions Reference

This document describes the scheme functions implemented in this folder. Each entry includes a short purpose summary, parameter usage, and a compact example.

## Function Index

Functions used in schemes. The tags show the scheme types a function can be used in:
`S` = Screen, `D` = Data, `T` = Time, `P` = Payroll. Each section also lists them under "Schemes".

Time and Payroll schemes have their own calculation functions (e.g. `GENWT`, `TOLER`, `P0008`, `NET`), which are not described here.

- [ACTION](#action) - Page Action `D`
- [API](#api) - API Call `S` `D`
- [AUTH](#auth) - Authorization Check `S`
- [BATCH](#batch) - Batch Input `S`
- [COM](#com) - Comment `S` `D` `T` `P`
- [COMMIT](#commit-obsolote) - Transaction Commit `S`
- [DELPERNR](#delpernr) - Delete Person `S`
- [GENWS](#genws) - Generate Shift `S`
- [GETCOUNT](#getcount) - Important Figures `S`
- [GETDEF](#getdef) - Get Default `S`
- [GETNODE](#getnode) - Gets selected node object data `S`
- [GETPARAM](#getparam) - Get Parameter `S`
- [GETPERNR](#getpernr) - Get Employees `S` `D`
- [GETREPORT](#getreport) - Report Retrieval `S`
- [GOURL](#gourl) - Go to URL `S`
- [INBOX](#inbox) - Inbox `S`
- [INFTY](#infty) - Infotype Operation `S`
- [JOB](#job) - Scheduled Scheme Run `D`
- [MAIL](#mail) - Send Email `S`
- [ME](#me-obsolote) - My Employee Data `S`
- [MSG](#msg) - Show Message `S`
- [NODE](#node) - Adds node to report viewer tree `S`
- [OINFTY](#oinfty) - OM Infotype Operation `S`
- [PBINFTY](#pbinfty) - Applicant Infotype Operation `S`
- [PDF](#pdf) - Generate PDF `S`
- [PEOPLE](#people-obsolote) - People (disabled)
- [PJSON](#pjson) - Process JSON `S` `D`
- [PRINT](#print) - Print Log Output `S` `D` `T` `P`
- [PROG](#prog) - Calls program `S` `D` `T` `P`
- [RUNPY](#runpy) - Run Payroll Scheme `S`
- [RUNTE](#runte) - Run Time Scheme `S`
- [SETCITEM](#setcitem) - Set Card Item `S`
- [SETDATA](#setdata) - Set Data `S`
- [SETDEF](#setdef) - Set Default `S`
- [SETENB](#setenb) - Set Enable `S`
- [SETHDR](#sethdr) - Set Header `S`
- [SETOP](#setop) - Add Options `S`
- [SETPROP](#setprop) - Set Property `S`
- [SETPROP1](#setprop1) - Set Single Property `S`
- [SETRQ](#setrq) - Set Required `S`
- [SETVS](#setvs) - Set Visible `S`
- [SMS](#sms) - Send SMS `S`
- [TABLE](#table) - Read Table `S`
- [TASK](#task) - Create Task `S` `D`
- [TRXML](#trxml) - SGK XML Download `S`
- [VERIFY](#verify) - Verification Code `S`
- [WORKDAYS](#workdays) - Working Days `S` `D`

- [Common JSONLogic Examples](#common-jsonlogic-examples)

---

## ACTION

**Schemes:** Data

Adds an action to the response of a data scheme. The page that runs the scheme executes the actions in order.

Parameters:
- `Par1`: Action name.
- `Par2`: Object name (text, URL or field name(s) depending on the action).
- `Par3`: Action parameter (optional).
- `Par4`: JSONLogic condition.

Actions:
- `MESSAGE`: Shows `Par2` as a message; `Par3` is the type `E`, `W`, `I` or `S`. Type `E` also stops the page (e.g. a task is not completed).
- `ALERT`: Shows `Par2` in an alert box.
- `NAVIGATE`: Goes to the URL in `Par2`; `Par3`: `NEW` (new tab) or `REPLACE`.
- `BACK`: Returns to the page the form was opened from.
- `HIDE`, `SHOW`, `DISABLE`, `ENABLE`: Hides, shows, disables or enables the field(s) in `Par2` (comma separated).

Example:
```text
ACTION
Par1: MESSAGE
Par2: End date can not be before begin date
Par3: E
Par4: {"<":[{"var":"Data.Endda"},{"var":"Data.Begda"}]}

ACTION
Par1: HIDE
Par2: NOTES,ATTACHMENT
Par4: {"==":[{"var":"Data.Type"},"01"]}
```

## API

**Schemes:** Screen, Data

Performs an HTTP request against an external or internal endpoint.

Parameters:
- `Par1`: options about API call
- `Par2`: DATA object in POST method
- `Par3`: Variable name in container that api response will be assigned to
- `Cond`: JSONLogic condition

Common options:
- `link`: Request URL.
- `method`: HTTP method such as GET or POST.
- `authtype`: Authentication mode such as Bearer or Basic.
- `token`, `username`, `password`: Authentication values.
- `result`: Result object for api response, check result in container

Example:
```text
API
Par1: 
{
	"link":"https://api.example.com/items",
	"method":"GET",
	"authtype":"Basic",
	"token":"=container.AuthToken",
	"username":"",
	"password":"",
	"result": "APIRES"
}
Par2: 
{
	"PERNR": container.PERNR, 
	"BEGDA": "20260101", 
	"ENDDA": "20260102", 
	"AWART": "1000" 
}
```

## AUTH

**Schemes:** Screen

Checks whether the current user is authorized for the requested action.

Parameters:
- `Par1`: Role name or user identifier.
- `Par2`: Optional check mode. Use `USER` for username comparison; leave empty for role-based checking.

Example:
```text
AUTH
Par1: EMPLOYEE
Par2:
```

```text
AUTH
Par1: john.howard@acb.com
Par2: USER
```

## BATCH

**Schemes:** Screen

Imports full data to specific infotypes. This function is used to import external data by scheme. '0000', '0001', '0002', '0003', '0105'

Parameters:
- `Par1`: Infotype Number
- `Par2`: Data array for specific infotype.

Example:
```text
BATCH
Par1: 00003
Par2: P00003
```

```text
BATCH
Par1: 0000
Par2: P0000
```

For example, you received data from external api, here you map and create new array object for BATCH function.

```text
$map(container.APIRES, function($emp) {
    {
      "PERNR": $emp.pernr,
      "BEGDA": "20000101",
      "ENDDA": "99991231",
      "MASSN": "01",
      "MASSG": "01",
      "STAT2": "3"
    }
})
```
and place this mapping with PJSON like this prior to BATCH
```text
PJSON above_map_code P0000
```

## COM

**Schemes:** Screen, Data, Time, Payroll

Writes a comment line into the schema log. This function has no affect in algorithm.

Parameters are not used in this function.
Use comment area only.

Example:
```text
COM
Comment: Starting employee processing
```

## COMMIT (Obsolote)

**Schemes:** Screen

Commits the active database transaction started with `TRX`.

Parameters:
- `Cond`: JSONLogic condition. StateId.

Example:
```text
COMMIT
Par1: {"==":[{"var":"container.Success"},true]}
```

## DELPERNR

**Schemes:** Screen

Deletes a person record by personnel number.

Parameters:
- `Par1`: Personnel number or JSONata expression.
- `Cond`: JSONLogic condition. StateId.

Example:
```text
DELPERNR
Par1: =container.SelectedPernr
Cond: {"==":[{"var":"container.ConfirmDelete"},"X"]}
```

## GENWS

**Schemes:** Screen

Generates shift data for a personnel number and date range.

Parameters:
- `Par1`: JSONata expression that resolves to a shift generation options object.
- `Cond`: JSONLogic condition. StateId.

Common options:
- `PERNR`: Personnel number.
- `BEGDA`: Begin date in `YYYYMMDD` format.
- `ENDDA`: End date in `YYYYMMDD` format.

Example:
```text
GENWS
Par1: {"PERNR":"12345","BEGDA":"20240101","ENDDA":"20240131"}
Cond:
```

## GETCOUNT

**Schemes:** Screen

Loads summary counts used in dashboards and overview screens.

Parameters:
- `Par1`: Report code.
- `Par2`: JSONata expression for filter options.
- `Par3`: Container variable name for the result.
- `Cond`: JSONLogic condition. StateId.

Example:
```text
GETCOUNT
Par1: PENDING_TASKS
Par2: {"BEGDA":"=container.StartDate","ENDDA":"=container.EndDate"}
Par3: PendingCount
Cond:
```

## GETDEF

**Schemes:** Screen

Reads a saved default value for a field or parameter. Using this function in page load provides user to display last entry in selected field.

Parameters:
- `Par1`: Field name.
- `Cond`: JSONLogic condition. StateId.

Example:
```text
GETDEF
Par1: EmployeeStatus
Cond: State01
```

## GETPARAM

**Schemes:** Screen

Reads a system parameter value by name. Par1 could be only one system parameter, or could be more than one parameter like param1, param2. Secret parameters decrypted and pushed into container.

Parameters:
- `Par1`: Parameter or parameters with comma.
- `Cond`: JSONLogic condition.

Notes:
- Typically used for values passed from page navigation, workflow context, or request parameters.

Example:
```text
GETPARAM
Par1: SAPHOST,SAPUSER
Cond:
```

## GETPERNR

**Schemes:** Screen, Data

Loads employee master data and stores the result in `container.PNP`. It works in two ways:
- **Single employee** (default): loads one employee by personnel number.
- **Several employees** (`"Mode": "M"`): loads every employee the user is authorized for (auth objects), optionally narrowed by organizational fields and infotype records. Works in both Screen and Data schemes.

Parameters:
- `Par1`: Options object (JSONata, evaluated on the container) or personnel number reference.
- `Cond`: JSONLogic condition. StateId.

Notes:
- If `Par1` is empty, `container.UserPernr` is used.
- Option keys are case-insensitive: `pernr`, `PERNR`, `Pernr`, `Mode`, `MODE` etc. are all supported.

### Single employee

```text
GETPERNR
Par1: 
{
	"PERNR": container.SelectedPernr
}
Cond: Get
```
```text
GETPERNR
Par1:
{
	"PERNR": page.Elements[FieldName="PERNR"].Data
}
Cond: Get
```
In a Data scheme:
```text
GETPERNR
Par1: {"pernr": Data.PERNR}
Cond:
```

### Several employees (Mode M)

Retrieves people data based on the auth objects of the user. `container.PNP` becomes a **list** of employees, and the records of each requested infotype are written to `container.P0000`, `container.P0001`, … (one flat list per infotype, rows of all employees).

Options:

| Option | Description |
|---|---|
| `Mode` | `"M"` – required for multiple selection. |
| `Pernr` | Personnel number(s). Optional. |
| `Bukrs`, `Werks`, `Btrtl`, `Persg`, `Persk`, `Stell`, `Orgeh`, `Abkrs`, `Plans`, `Stat2` | Organizational selection fields. A single value or an array (`["1000","2000"]`). Optional. |
| `Begda`, `Endda` | Selection period, `YYYYMMDD` (or `YYYY-MM-DD`). Optional. |
| `MaxResults` | Maximum number of employees. Optional. |
| `Infotypes` | List of infotype selections (see below). Optional. |

`Infotypes` entry:

| Field | Description |
|---|---|
| `Infty` | Infotype number, e.g. `"0000"` (padded to 4 digits). |
| `Begda`, `Endda` | Records overlapping this period are read. Defaults: `18000101` – `99991231`. |
| `Subty` | Subtype. Optional. |
| `Filter` | Field filter on the infotype records (see below). Optional. |
| `Required` | `true`: only employees that have at least one record of this selection stay in `PNP` (and in the other infotype lists). Optional. |

Only the infotypes/subtypes the user is authorized for (P_ORGIN) are read. Entries with an invalid `Infty`, date or `Subty` are skipped.

#### Filter

`Filter` is an object whose keys are column names of the `PAxxxx` table and whose values are conditions. All conditions are combined with AND.

| Syntax | Meaning |
|---|---|
| `"MASSN": "01"` | equal |
| `"MASSN": ["01", "02"]` | one of |
| `"BEGDA": {"from": "20261001", "to": "20261031"}` | range, both ends included (`from` or `to` may be left out) |
| `"MASSG": {"not": "05"}` | not equal |
| `"MASSG": {"not": ["05", "06"]}` | none of |

Notes:
- Field names are checked against the table. An unknown field (e.g. a typo) or a value that does not fit the column type makes the whole GETPERNR fail with an error – it never silently selects everybody.
- Dates are given as `YYYYMMDD` (or `YYYY-MM-DD`), numbers as number or text.
- In Screen schemes the error is shown on the page; in Data schemes it is returned as a `MESSAGE` action of type `E` (which also stops a task).

#### Examples

All authorized employees with organizational selection:
```text
GETPERNR
Par1:
{
	"MODE": "M", //mode M means multiple people will be accessed
	"BUKRS": "", //optional, company code
	"WERKS": "", //optional, personnel area
	"BTRTL": "", //optional, personnel sub area
	"PERSG": "", //optional, employee group
	"PERSK": "", //optional, employee sub group
	"STELL": "", //optional, job id
	"ORGEH": "", //optional, organization unit
	"ABKRS": "", //optional, payroll area
	"PLANS": "", //optional, position
	"STAT2": "" //optional, status
}
Cond: Get
```

Simply, if no selection is needed:
```text
GETPERNR
Par1:
{
	"MODE": "M"
}
Cond: Get
```

Without selection but with authorized infotypes (results in `container.P0000`, `container.P0001`, `container.P0002`):
```text
GETPERNR
Par1:
{
	"Mode": "M",
	"Infotypes": [
		{ "Infty": "0000", "Begda": "20260101", "Endda": "99991231", "Subty": "" },
		{ "Infty": "0001", "Begda": "20260101", "Endda": "99991231", "Subty": "" },
		{ "Infty": "0002", "Begda": "20260101", "Endda": "99991231", "Subty": "" }
	]
}
Cond: Get
```

Active employees of two personnel areas:
```text
GETPERNR
Par1:
{
	"Mode": "M",
	"Werks": ["1000", "2000"],
	"Stat2": "3"
}
Cond: Get
```

Hired in October 2026 (action type `01`, only those employees stay in `PNP`):
```text
GETPERNR
Par1:
{
	"Mode": "M",
	"Infotypes": [
		{
			"Infty": "0000",
			"Filter": {
				"MASSN": "01",
				"BEGDA": { "from": "20261001", "to": "20261031" }
			},
			"Required": true
		}
	]
}
Cond: Get
```

Hired in the last 30 days (dynamic dates with JSONata):
```text
GETPERNR
Par1:
{
	"Mode": "M",
	"Stat2": "3",
	"Infotypes": [
		{
			"Infty": "0000",
			"Filter": {
				"MASSN": "01",
				"BEGDA": {
					"from": $fromMillis($toMillis(SysDate, '[Y0001][M01][D01]') - 30*86400000, '[Y0001][M01][D01]'),
					"to": SysDate
				}
			},
			"Required": true
		}
	]
}
Cond: Get
```

Hires (`01`) and leavers (`10`) in 2026, excluding reasons `05` and `06` (`not`):
```text
GETPERNR
Par1:
{
	"Mode": "M",
	"Infotypes": [
		{
			"Infty": "0000",
			"Begda": "20260101",
			"Endda": "20261231",
			"Filter": {
				"MASSN": ["01", "10"],
				"MASSG": { "not": ["05", "06"] }
			},
			"Required": true
		}
	]
}
Cond: Get
```

Several selections combined – employees with a `0001` record in org unit `50000001` **and** an address (`0006`, subtype `1`); `0002` is read only as extra data, not as a condition:
```text
GETPERNR
Par1:
{
	"Mode": "M",
	"Infotypes": [
		{ "Infty": "0001", "Filter": { "ORGEH": "50000001" }, "Required": true },
		{ "Infty": "0006", "Subty": "1", "Required": true },
		{ "Infty": "0002" }
	]
}
Cond: Get
```
When more than one entry has `"Required": true`, an employee must have a record in **each** of them. Employees dropped by `Required` are also removed from all infotype lists.

## GETREPORT

**Schemes:** Screen

Runs a predefined report and returns its result set.

Parameters:
- `Par1`: Report code.
- `Par2`: JSONata expression for report parameters.
- `Cond`: JSONLogic condition.

Example:
```text
GETREPORT
Par1: ABSENCE_REPORT
Par2: {"PERNR":"=container.Pernr","BEGDA":"=container.Begda","ENDDA":"=container.Endda"}
Cond:
```

## GETNODE

**Schemes:** Screen

Gets selected node object and puts it to container as defined key in Par1.

Parameters:
- `Par1`: Report tree fieldname.
- `Par2`: Selected node variable name in container.
- `Cond`: JSONLogic condition.

Adds node object to reportview page element.

```text
GETNODE  repTree SELREP
```

## GOURL

**Schemes:** Screen

Navigates to another page, route, or URL.

Parameters:
- `Par1`: Target URL, route, or a special value such as `BACK`.
- `Cond`: JSONLogic condition. StateId.

Example:
```text
GOURL
Par1: BACK
Cond:
```

## INBOX

**Schemes:** Screen

Creates a workflow inbox item for a user or role.

Parameters:
- `Par1`: JSONata expression that resolves to an inbox options object.
- `Cond`: JSONLogic condition.

Common options:
- `recipient`: Target user or role.
- `taskname`: Task title.
- `priority`: Priority level.
- `duedate`: Due date.

Example:
```text
INBOX
Par1: {"recipient":"MANAGER","taskname":"Approve leave","priority":"H"}
Cond:
```

## INFTY

**Schemes:** Screen

Reads, creates, updates, or deletes personnel infotype data.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: Data payload for insert or update operations.
- `Cond`: JSONLogic condition.

Common options:
- `INFTY`: Infotype code such as `0002`.
- `COM`: Operation code such as `INS`, `GET`, `GETB`, `UPD`, or `DEL`.
- `PERNR`: Personnel number.
- `BEGDA`, `ENDDA`: Validity dates.
- `SUBTY`, `SPRPS`, `OBJPS`, `SEQNR`: Infotype control fields.

Example:
```text
INFTY
Par1: {"INFTY":"0002","COM":"GET","PERNR":"=container.Pernr"}
Par2:
Par3:
```

## JOB

**Schemes:** Data

Schedules a data scheme (`Type = Data`). An hourly background job runs the executable data schemes whose JOB row is due in the current hour (`Europe/Istanbul` time) and whose condition is met. Inside the scheme run itself the row does nothing.

Parameters:
- `Par1`: Schedule (plain JSON):
  - `freq`: `HOURLY`, `DAILY`, `WEEKLY` or `MONTHLY` (required).
  - `hour`: An hour or a list of hours (0-23). Every hour for `HOURLY` when empty, `0` for the others.
  - `days`: `WEEKLY`: weekdays `1` = Monday .. `7` = Sunday; `MONTHLY`: days `1`-`31` or `"L"` (last day). A day after the end of the month (e.g. 31 in April) runs on the last day. Required for `WEEKLY` / `MONTHLY`.
  - `begda`, `endda`: Validity of the job as `YYYYMMDD`; open when empty.
  - `user`, `pernr`, `langu`: `UserName` (default `JOB`), `UserPernr` and `Langu` (default `TR`) of the run.
  - `data`: Initial `container.Data` of the run.
- `Par4`: JSONLogic condition (or a scheme state), checked on the job container before the run.

The container of a job run has `PageEvent = "Job"`, `JobRun = true`, `JobSeqnr` (the JOB row), `JobHour`, `WfId = "0"`, no roles and the system date (`SysDate`, `SysDay`, `SysMonth`, `SysYear`).

Notes:
- A scheme runs at most once an hour, also with several due JOB rows (the first one by `Seqnr` runs it).
- Invalid `Par1` / `Par4`, scheme errors and `MESSAGE` actions (`ACTION`) of type `E` are written to the error log.
- Rows with `Deact` = `X` / `*` disable the job; `Executable = false` disables the scheme.

Example (every day at 07:00; `hour` = `0` (midnight) when empty):
```text
JOB
Par1: {"freq": "DAILY", "hour": 7}
```

Example (every day at 09:00 and 18:00 from 2027, run as user `HRADMIN` in English with initial data):
```text
JOB
Par1: {"freq": "DAILY", "hour": [9, 18], "begda": "20270101", "user": "HRADMIN", "langu": "EN", "data": {"Mode": "REMINDER"}}
```

Example (on the 1st and the 15th of the month at 06:00, and every Monday at 08:00 and 17:00 in 2027 except August):
```text
JOB
Par1: {"freq": "MONTHLY", "days": [1, 15], "hour": 6}

JOB
Par1: {"freq": "WEEKLY", "days": [1], "hour": [8, 17], "begda": "20270101", "endda": "20271231"}
Par4: {"!=":[{"var":"SysMonth"},8]}
```

## MAIL

**Schemes:** Screen

Sends an email through the configured SendGrid service.

Parameters:
- `Par1`: JSONata expression that resolves to an options object.
- `Par4`: JSONLogic condition.

Options:
- `to`: Recipient email address.
- `subject`: Email subject, up to 200 characters.
- `html`: Optional HTML body.
- `text`: Optional plain-text body. At least `html` or `text` is required.
- `from`: Optional sender address configured and verified in SendGrid.
- `bcc`: Optional array of BCC recipient addresses.
- `result`: Optional container variable name. Defaults to `MAILRESULT`.

The result variable contains `success`, `channel`, `maskedTarget` and `timestamp`. Failures also contain `error`.

Example:
```text
MAIL
Par1: {"to":container.Email,"subject":"Request approved","text":"Your request has been approved.","result":"MailResult"}
Par4: {"==":[{"var":"Status"},"APPROVED"]}
```

## ME (Obsolote)

**Schemes:** Screen

Loads the current logged-in user’s employee data.

Parameters:
- `Par1`: Optional options object.
- `Cond`: JSONLogic condition.

Example:
```text
ME
Par1:
Cond:
```

## MSG

**Schemes:** Screen

Shows a message to the user in the UI.

Parameters:
- `Par1`: Message title.
- `Par2`: Message body.
- `Par3`: Error flag. true means show message in red background color.
- `Cond`: JSONLogic condition.

Example:
```text
MESSAGE
Par1: Warning
Par2: Employee status is inactive.
Par3: false
Cond: StateId.
```
```text
MESSAGE
Par1: Error
Par2: External system cannot be accessible
Par3: true
Cond: StateId.
```

İf Par1 or Par2 starts with =, it means it support JSONata.

## NODE

**Schemes:** Screen

Adds a node to the tree of a report viewer (`OrcReportView`) page element.

Parameters:
- `Par1`: Node options (JSONata or plain JSON):
  - `fieldname`: FieldName of the report viewer element.
  - `id`: Node id.
  - `label`: Node text. `Node` when empty.
  - `icon`: Icon name. `mdi:nodejs` when empty.
  - `pid`: Parent node id. `#` (root) when empty.
  - `url`: Node URL. `#` when empty.
- `Cond`: JSONLogic condition.

Example:
```text
NODE
Par1: {"id": "folder0", "fieldname": "repTree", "icon": "glyphs-poly:folder", "label": "Legal Report", "pid": "#"}

NODE
Par1: {"id": "rep01", "fieldname": "repTree", "icon": "mdi:file", "label": "Headcount", "pid": "folder0"}
```

## OINFTY

**Schemes:** Screen

Reads or writes Organizational Management infotype data.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: Data payload.
- `Cond`: JSONLogic condition. StateId.

Common options:
- `INFTY`, `COM`, `OBJID`, `BEGDA`, `ENDDA`, `SUBTY`, `SEQNR`, `PLVAR`, `OTYPE`.

Example:
```text
OINFTY
Par1: {"INFTY":"1000","COM":"GET","OBJID":"=container.Objid"}
Par2:
Cond:
```

## PBINFTY

**Schemes:** Screen

Updates applicant infotype data used in the applicant module.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: Data payload.
- `Cond`: JSONLogic condition. StateId.

Common options:
- `INFTY`: Applicant infotype code such as `PB0002`.
- `COM`: Operation code.
- `APPNO`: Application number.
- `BEGDA`, `ENDDA`, `SUBTY`: Standard validity fields.

Example:
```text
PBINFTY
Par1: {"INFTY":"PB0002","COM":"UPD","APPNO":"=container.AppNo"}
Par2: =container.ApplicantData
Cond:
```

## PDF

**Schemes:** Screen

Generates a PDF document from a template and data.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: Specific Scenerio
- `Cond`: JSONLogic condition.

Common options:
- `template`: Template code.
- `pernr`: Personnel number.
- `begda`, `endda`: Date range.
- `mode`: M - container.PNP people

Par 2 values;
- P : Payroll, slipType needed optionally, otherwise it uses STD0 as payroll slip format
- L : Leave Agreement, if you use this option, then check AL_AWART and AL_KTART parameters
- W : Work Document

Example:
```text
PDF
Par1: {"template":"LEAVE_REPORT","pernr":"=container.Pernr","begda":"20240101","endda":"20240131"}
Cond:
```

## PEOPLE (Obsolote)

**Schemes:** none (disabled)

Works on a filtered employee list. Disabled: a PEOPLE row is not executed.

Parameters:
- `Par1`: JSONata filter expression or selection object.
- `Cond`: JSONLogic condition.

Example:
```text
PEOPLE
Par1: {"Orgeht":"1000","Status":"ACTIVE"}
Cond:
```

## PJSON

**Schemes:** Screen, Data

Transforms JSON data and stores the result in a target variable or object.

Parameters:
- `Par1`: JSONata expression.
- `Par2`: Target variable or property path.
- `Cond`: JSONLogic condition.

See also: [Common JSONata Examples](#common-jsonata-examples)

Example:
```text
PJSON
Par1: $filter(container.Employees, function($e){$e.Salary > 50000})
Par2: HighEarners
Cond:
```
## PROG

**Schemes:** Screen, Data, Time, Payroll

Runs a program (TypeScript, defined in the program editor) with the data of the scheme.

Parameters:
- `Par1`: Program name. The program must be active and its type must match the scheme:

| Scheme | Program type | Function |
|---|---|---|
| Screen | `S` | `run(page, container)` |
| Data | `D` | `run(container)` |
| Time | `T` | `run(dayRow, dayRows)` |
| Payroll | `P` | `run(container, wpbp)` |

- `Par4`: JSONLogic condition.

The program must return the objects it changes (`page` / `container` / `dayRow` / `wpbp`); only returned objects are written back.
Samples for each type (validation, time types, wage types ...): [Program Samples](./TS_Samples.md).

Example:
```text
PROG
Par1: ZPY_BONUS
Par4:
```

## PRINT

**Schemes:** Screen, Data, Time, Payroll

Prints the requested object to the log output.

Parameters:
- `Par1`: Object name such as `CONT` or `PAGE`.

Example:
```text
PRINT
Par1: CONT
```

## RUNPY

**Schemes:** Screen

Runs the payroll calculation schema.

Parameters:
- `Par1`: Payroll options object or JSONata expression.
- `Cond`: JSONLogic condition.

Common options:
- `PERNR`: Personnel number.
- `BEGDA`, `ENDDA`: Payroll period dates.
- `PAYTY`: Payroll type.

Example:
```text
RUNPY
Par1: {"PERNR":"12345","BEGDA":"20240101","ENDDA":"20240131"}
Cond:
```

## RUNTE

**Schemes:** Screen

Runs the time evaluation schema.

Parameters:
- `Par1`: Time evaluation options object or JSONata expression.
- `Cond`: JSONLogic condition.

Common options:
- `PERNR`: Personnel number.
- `BEGDA`, `ENDDA`: Period dates.
- `TEVTY`: Time evaluation type.

Example:
```text
RUNTE
Par1: {"PERNR":"12345","BEGDA":"20240101","ENDDA":"20240131","TEVTY":"MONTHLY"}
Cond:
```

## SETCITEM

**Schemes:** Screen

Sets the text of an item of a card table (`CardTable`) element on the screen.

Parameters:
- `Par1`: FieldName of the card table element.
- `Par2`: `Value` of the item to update.
- `Par3`: New text: a fixed text, or a JSONata expression starting with `=`.
- `Par4`: JSONLogic condition.

The row stops the scheme when the element, the item or the expression is invalid.

Example:
```text
SETCITEM
Par1: cardSummary
Par2: TOTAL
Par3: =$string(container.TotalDays) & " days"
Par4:
```

## SETDATA

**Schemes:** Screen

Assigns a value to a page field or object.

Parameters:
- `Par1`: Field name.
- `Par2`: Value to assign.
- `Cond`: JSONLogic condition.

Value forms:
- Plain string.
- JSON object or array.
- Container variables can be set by prefix `=`.
examples: 
	=container.PNP.Pernr
	=container.PNP.Ename

- System values such as `SY-DATUM` or `SY-UNAME`.

Example:
```text
SETDATA
Par1: PERNR
Par2: =container.PNP.Pernr
Cond: state0
```
```text
SETDATA
Par1: ENAME
Par2: =container.PNP.Ename
Cond: state0
```

## SETDEF

**Schemes:** Screen

Stores the current value of a field as the user’s default.

Parameters:
- `Par1`: Field name.
- `Cond`: JSONLogic condition.

Example:
```text
SETDEF
Par1: EmployeeStatus
Cond:
```

## SETENB

**Schemes:** Screen

Enables or disables a field.

Parameters:
- `Par1`: Field name.
- `Par2`: Boolean enable flag.
- `Cond`: JSONLogic condition.

Example:
```text
SETENB
Par1: SalaryField
Par2: false
Cond: {"==":[{"var":"container.UserRole"},"Viewer"]}
```

## SETHDR

**Schemes:** Screen

Sets the page or report header title.

Parameters:
- `Par1`: Header text.

Example:
```text
SETHDR
Par1: Employee Information
```

## SETOP

**Schemes:** Screen

Populates a field with dynamic option values.

Parameters:
- `Par1`: Field name.
- `Par2`: JSON options object.
- `Par3`: Predefined option set code.
- `Cond`: JSONLogic condition.

Common options:
- `TABLE`, `VALUE`, `TEXT`: Source table and field mappings.
- `VNAME`: View or data view code.
- `VTYPE`: Display mode, such as `V`, `T`, or `VT`.
- `FILTER`: Include only the listed values.
- `EXFILTER`: Exclude the listed values.
- `WHERE`: Optional JSONLogic row filter.

Display modes:
- `V`: Show only the value.
- `T`: Show only the text.
- `VT`: Show value and text together.

Example:
```text
SETOP
Par1: StatusField
Par2: {"VNAME":"SCH_STATUS","VALUE":"CODE","TEXT":"TEXT","VTYPE":"VT","FILTER":"A,B,C"}
Par3:
Cond:
```

## SETPROP

**Schemes:** Screen

Sets multiple properties on a field or component.

Parameters:
- `Par1`: Field name.
- `Par2`: Property object or JSONata expression.
- `Cond`: JSONLogic condition.

Example:
```text
SETPROP
Par1: ErrorField
Par2: {"color":"red","fontWeight":"bold"}
Cond:
```

## SETPROP1

**Schemes:** Screen

Sets a single property on a field or component.

Parameters:
- `Par1`: Field name.
- `Par2`: Property name.
- `Par3`: Property value.
- `Cond`: JSONLogic condition.

Example:
```text
SETPROP1
Par1: Title
Par2: fontSize
Par3: 16px
Cond:
```

## SETRQ

**Schemes:** Screen

Marks a field as required or optional.

Parameters:
- `Par1`: Field name.
- `Par2`: Required flag.
- `Cond`: JSONLogic condition.

Example:
```text
SETRQ
Par1: EmployeeID
Par2: true
Cond:
```

## SETVS

**Schemes:** Screen

Shows or hides a field.

Parameters:
- `Par1`: Field name.
- `Par2`: Visible flag.
- `Cond`: JSONLogic condition.

Example:
```text
SETVS
Par1: InternalNotes
Par2: false
Cond: {"==":[{"var":"container.UserRole"},"Employee"]}
```

## SMS

**Schemes:** Screen

Sends an SMS through the configured Posta Guvercini service.

Parameters:
- `Par1`: JSONata expression that resolves to an options object.
- `Par4`: JSONLogic condition.

Options:
- `to`: Recipient phone number. Formatting characters and a leading `+` or `00` are normalized.
- `message`: SMS content, up to 1000 characters.
- `result`: Optional container variable name. Defaults to `SMSRESULT`.

The result variable contains `success`, `channel`, `maskedTarget` and `timestamp`. Failures also contain `error`.

Example:
```text
SMS
Par1: {"to":container.Mobile,"message":"Your request has been approved.","result":"SmsResult"}
Par4: {"==":[{"var":"Status"},"APPROVED"]}
```

## TABLE

**Schemes:** Screen

Reads records from a database table and stores them in the container.

Parameters:
- `Par1`: Model or table code.
- `Par2`: JSONLogin Filtee
- `Par3`: no use.
- `Par4`: Container key for the result.
- `Cond`: JSONLogic condition for function.

Example 1: Static filtering on view object
```text
TABLE
Par1: V_THRPA03
Par2: {"WERKS": '3400'}
```

Example 2: Dynamic filtering on view object, (PostBack on WERKS field)
```text
TABLE
Par1: V_THRPA03
Par2: {"WERKS": page.Elements[FieldName="WERKS"].Data}
```

## TASK

**Schemes:** Screen, Data

Creates a workflow task record and stores workflow data with it.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: Workflow data payload.
- `Cond`: JSONLogic condition.

Common options:
- `instatus` : Starting status of workflow step.
- `currentstatus` : Output status of workflow step.
- `wfstatus` : Workflow status. (WFDATA)
- `resotype`, `resobjid`: Responsible object type and identifier.
- `completewf` : false if workflow continues, true if workflow completed. This prop is optional, if not supplied, system checks page statuses with laststatus. is true.
- `taskname` : definition of task, no affect, just data in line.
Example:
```text
TASK
Par1: options object sample 
{
	"instatus" : "NEW",
	"currentstatus" : "SUBMITTED",
	"wfstatus" :  "SUBMITTED",
	"resotype" : "P",
	"resobjid" : container.PNP.Manid
}
Par2: data object sample
{
	"AWART" : "1000",
	"PERNR": page.Elements[FieldName="PERNR"].Data,
	"DATES": page.Elements[FieldName="DATES"].Data,
	"BEGDA": $substring(page.Elements[FieldName="DATES"].Data, 0, 8),
	"ENDDA": $substring(page.Elements[FieldName="DATES"].Data, 8, 8),
	"NOTES": page.Elements[FieldName="NOTES"].Data
}
Cond (Par4):
{
  "and": [
    {
      "==": [
        {
          "var": "WIId"
        },
       "0"
      ]
    },
    {
      "==": [
        {
          "var": "PageEvent"
        },
        "Post"
      ]
    },
    {
      "==": [
        {
          "var": "PostFieldName"
        },
        "DOSUBMIT"
      ]
    }
  ]
}
```

## TRXML

**Schemes:** Screen

Creates an SGK notification XML (legal requirement for Turkey) and gives it to the page for download.

Parameters:
- `Par1`: Options (JSONata or plain JSON):
  - `pernr`: Personnel number (max 8 digits, required).
  - `xmltype`: `1` = İşe Giriş Bildirgesi, `2` = İşten Çıkış Bildirgesi, `3` = Aylık Prim ve Hizmet Belgesi (required).
  - Common fields: `sgkNo`, `unvan`, `tckn`, `ad`, `soyad`, `sigortaTuru`.
  - Type 1: `dogumTarihi`, `cinsiyet`, `iseGirisTarihi`, `meslekKodu`, `ucret`.
  - Type 2: `istenCikisTarihi`, `cikisNedeni`.
  - Type 3: `yil`, `ay`, `calismaSayisi`, `gunSayisi`, `primMatrahi`.
- `Par4`: JSONLogic condition.

Notes:
- Fields that are not given are filled with sample values, so pass all the fields.
- An invalid `pernr` or `xmltype` shows an error message on the page and stops the scheme.

Example:
```text
TRXML
Par1: {"pernr": container.Pernr, "xmltype": "2", "tckn": container.Tckn, "ad": container.Vorna, "soyad": container.Nachn, "istenCikisTarihi": container.Endda, "cikisNedeni": "03"}
Par4: {"==":[{"var":"PostFieldName"},"BTNXML"]}
```

## VERIFY

**Schemes:** Screen

Sends or checks a six-digit verification code through the email or SMS service.

Note: Not usable yet. The storage of the codes is not implemented, so the function returns an error.

Parameters:
- `Par1`: Verification channel: `EMAIL` or `SMS`.
- `Par2`: Operation: `SEND` or `CHECK`.
- `Par3`: JSONata expression that resolves to an options object.
- `Par4`: JSONLogic condition.

`SEND` options:
- `target`: Recipient email address or phone number.
- `purpose`: Optional context key. Defaults to `LOGIN`.
- `language`: Optional `TR` or `EN`.
- `result`: Optional container variable name. Defaults to `VERIFYRESULT`.

The `SEND` result contains `challengeId`, `status: "SENT"`, `maskedTarget` and `expiresAt`. Pass its `challengeId` into the later `CHECK` call.

`CHECK` options:
- `challengeId`: ID returned by `SEND`.
- `code`: Six-digit code entered by the user.
- `purpose`: Must match the value used by `SEND`.
- `result`: Optional container variable name.

The `CHECK` result has `verified: true` and `status: "VERIFIED"` when the code is correct. Codes expire after five minutes and allow at most five failed attempts.

Example:
```text
VERIFY
Par1: EMAIL
Par2: SEND
Par3: {"target":container.Email,"purpose":"LOGIN","result":"Verification"}
Par4:

VERIFY
Par1: EMAIL
Par2: CHECK
Par3: {"challengeId":container.Verification.challengeId,"code":container.VerificationCode,"purpose":"LOGIN","result":"Verification"}
Par4:
```

## WORKDAYS

**Schemes:** Screen, Data

Calculates the working days of an employee between two dates (both included) and writes the number into the container.

Parameters:
- `Par1`: Options object (JSONata on the container, plain JSON also works):
  - `pernr`: Employee; `container.UserPernr` when empty.
  - `begda`, `endda`: First and last day as `YYYYMMDD`, `YYYY-MM-DD` or a date; `endda` = `begda` when empty.
  - `sap`: `true` reads the SAP service `zorc_abs_day`. Otherwise the work schedule is used (the same `ABWTG` that leave approval / `INFTY` writes to PA2001).
  - `field`: Container element of the result; a path such as `Data.TotalDays` is allowed. `WORKDAYS` when empty.
- `Par4`: JSONLogic condition.

Notes:
- Missing `pernr` / `begda` skips the row without an error.
- Errors (invalid dates, SAP failures) stop the scheme: a page message in screen schemes, a `MESSAGE` action of type `E` in data schemes (which also stops a task).
- In data schemes the dates of `Data` arrive as `YYYYMMDD`.

Example (data scheme of a workflow form, recalculated when a date changes):
```text
WORKDAYS
Par1: {"pernr": Data.Pernr, "begda": Data.Begda, "endda": Data.Endda, "sap": true, "field": "Data.TotalDays"}
Par4: {"in":[{"var":"PostBackField"},["BEGDA","ENDDA"]]}
```

## Common JSONLogic Examples

These examples cover the most common condition patterns used across schema functions.

### Equality Check

Use this when you want to continue only if a value matches exactly.

```json
{"==":[{"var":"container.UserRole"},"Admin"]}
```

### Not Equal Check

Use this when one value must differ from another.

```json
{"!=":[{"var":"container.Status"},"INACTIVE"]}
```

### String Contains / Text Match

Use `in` for membership checks and `contains` for text containment when supported by your JSONLogic implementation.

```json
{"in":["HR",{"var":"container.RoleList"}]}
```

### Logical AND

Use this when all conditions must be true.

```json
{"and":[
	{"==":[{"var":"container.UserRole"},"Manager"]},
	{"==":[{"var":"container.IsActive"},true]}
]}
```

### Logical OR

Use this when any one of the conditions is enough.

```json
{"or":[
	{"==":[{"var":"container.UserRole"},"Admin"]},
	{"==":[{"var":"container.UserRole"},"SuperUser"]}
]}
```

### Boolean Presence Check

Use this to require a flag or truthy value.

```json
{"!!":{"var":"container.ConfirmDelete"}}
```

### Empty Value Check

Use this to make sure a field has a value before continuing.

```json
{"!":[{"var":"container.EmployeeId"}]}
```

### Numeric Comparison

Use this for threshold-based decisions.

```json
{"and":[
	{">=":[{"var":"container.Amount"},1000]},
	{"<":[{"var":"container.Amount"},5000]}
]}
```

### Value Membership

Use this for allow-lists and deny-lists.

```json
{"in":[{"var":"container.CountryCode"},["TR","DE","NL"]]}
```

### Combined Condition Example

Use this pattern when a function should run only for a specific user role and a valid data state.

```json
{"and":[
	{"==":[{"var":"container.UserRole"},"Manager"]},
	{"==":[{"var":"container.Status"},"APPROVED"]},
	{"!!":{"var":"container.RequestId"}}
]}
```

### Typical Usage Pattern in Functions

Most functions accept the JSONLogic object in the last parameter and evaluate it before continuing.

```text
SETENB
Par1: SalaryField
Par2: true
Cond: {"==":[{"var":"container.UserRole"},"Admin"]}
```

## Common JSONata Examples

These examples cover the JSONata patterns that appear most often in scheme functions such as `PJSON`, `SETDATA`, `TABLE`, `GETREPORT`, `INBOX`, `RUNPY`, and `RUNTE`.

### Simple Field Access

Read a value directly from the container.

```text
container.EmployeeName
```

### String Concatenation

Build a combined value from multiple fields.

```text
container.FirstName & ' ' & container.LastName
```

### Conditional Value

Return one value when the condition is true and another when it is false.

```text
$boolean(container.IsActive) ? 'ACTIVE' : 'INACTIVE'
```

### Filter Array Items

Keep only the rows that match the condition.

```text
$filter(container.Employees, function($e){$e.Salary > 50000})
```

### Map Array Items

Transform each item in a collection.

```text
$map(container.Employees, function($e){
	{
		"Value": $e.Pernr,
		"Text": $e.FirstName & ' ' & $e.LastName
	}
})
```

### Object Construction

Create a new object from existing values.

```text
{
	"PERNR": container.Pernr,
	"NAME": container.FirstName & ' ' & container.LastName,
	"STATUS": container.Status
}
```

### Fallback Value

Use a default when the primary value is empty or missing.

```text
container.AppName ? container.AppName : 'UNKNOWN'
```

### Nested Lookup

Access a property inside a nested structure.

```text
container.Person.Address.City
```

### Array Join

Combine array values into a single string.

```text
$join(container.RoleList, ', ')
```

### Number Conversion

Convert a string to a number before using it in math operations.

```text
$number(container.Amount)
```

### Typical PJSON Pattern

Use JSONata to produce a derived value and store it in a container key.

```text
PJSON
Par1: $map(container.Employees, function($e){
	{
		"Value": $e.Pernr,
		"Text": $e.FirstName & ' ' & $e.LastName
	}
})
Par2: EmployeeOptions
Cond:
```

---

## Notes

- Most functions accept JSONata-based option values when a parameter starts with `=`.
- Most conditional checks use JSONLogic.
- Several functions write into `page`, `container`, or both.
