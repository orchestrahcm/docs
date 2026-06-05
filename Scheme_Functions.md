# Scheme Functions Reference

This document describes the scheme functions implemented in this folder. Each entry includes a short purpose summary, parameter usage, and a compact example.

## Function Index

- [API](#api) - API Call
- [AUTH](#auth) - Authorization Check
- [COM](#com) - Comment
- [COMMIT](#commit) - Transaction Commit
- [DELPERNR](#delpernr) - Delete Person
- [GENWS](#genws) - Generate Shift
- [GETCOUNT](#getcount) - Important Figures
- [GETDEF](#getdef) - Get Default
- [GETPERNR](#getpernr) - Get Employees
- [GETREPORT](#getreport) - Report Retrieval
- [GOURL](#gourl) - Go to URL
- [INBOX](#inbox) - Inbox
- [INFTY](#infty) - Infotype Operation
- [ME](#me) - My Employee Data
- [MESSAGE](#message) - Show Message
- [OINFTY](#oinfty) - OM Infotype Operation
- [PBINFTY](#pbinfty) - Applicant Infotype Operation
- [PDF](#pdf) - Generate PDF
- [PEOPLE](#people) - People
- [PJSON](#pjson) - Process JSON
- [Common JSONata Examples](#common-jsonata-examples)
- [PRINT](#print) - Print Log Output
- [PROG](#prog) - Calls program
- [RUNPY](#runpy) - Run Payroll Scheme
- [RUNTE](#runte) - Run Time Scheme
- [SETDATA](#setdata) - Set Data
- [SETDEF](#setdef) - Set Default
- [SETENB](#setenb) - Set Enable
- [SETHDR](#sethdr) - Set Header
- [SETOP](#setop) - Add Options
- [SETPROP](#setprop) - Set Property
- [SETPROP1](#setprop1) - Set Single Property
- [SETRQ](#setrq) - Set Required
- [SETVS](#setvs) - Set Visible
- [TABLE](#table) - Read Table
- [TASK](#task) - Create Task
- [TRX](#trx) - Start Transaction
- [WFLOAD](#wfload) - Load Workflow
- [WFSTART](#wfstart) - Start Workflow
- [WFSTEP](#wfstep) - Workflow Step
- [Common JSONLogic Examples](#common-jsonlogic-examples)

---

## API

Performs an HTTP request against an external or internal endpoint.

Parameters:
- `Par1`: JSONata expression that resolves to an options object.
- `Par2`: JSONLogic condition that controls execution.

Common options:
- `link`: Request URL.
- `method`: HTTP method such as GET or POST.
- `authtype`: Authentication mode such as Bearer or Basic.
- `token`, `username`, `password`: Authentication values.

Example:
```text
API
Par1: {"link":"https://api.example.com/items","method":"GET","authtype":"Bearer","token":"=container.AuthToken"}
Par2: {"==":[{"var":"container.UserRole"},"Admin"]}
```

## AUTH

Checks whether the current user is authorized for the requested action.

Parameters:
- `Par1`: Role name or user identifier.
- `Par2`: Optional check mode. Use `USER` for username comparison; leave empty for role-based checking.

Example:
```text
AUTH
Par1: HR_Manager
Par2:
```

## COM

Writes a comment line into the schema log.

Parameters:
- `Par1`: Comment text.

Example:
```text
COM
Par1: Starting employee processing
```

## COMMIT

Commits the active database transaction started with `TRX`.

Parameters:
- `Par1`: JSONLogic condition.

Example:
```text
COMMIT
Par1: {"==":[{"var":"container.Success"},true]}
```

## DELPERNR

Deletes a person record by personnel number.

Parameters:
- `Par1`: Personnel number or JSONata expression.
- `Par2`: JSONLogic condition.

Example:
```text
DELPERNR
Par1: =container.SelectedPernr
Par2: {"==":[{"var":"container.ConfirmDelete"},"X"]}
```

## GENWS

Generates shift data for a personnel number and date range.

Parameters:
- `Par1`: JSONata expression that resolves to a shift generation options object.
- `Par2`: JSONLogic condition.

Common options:
- `PERNR`: Personnel number.
- `BEGDA`: Begin date in `YYYYMMDD` format.
- `ENDDA`: End date in `YYYYMMDD` format.

Example:
```text
GENWS
Par1: {"PERNR":"12345","BEGDA":"20240101","ENDDA":"20240131"}
Par2:
```

## GETCOUNT

Loads summary counts used in dashboards and overview screens.

Parameters:
- `Par1`: Report code.
- `Par2`: JSONata expression for filter options.
- `Par3`: Container variable name for the result.
- `Par4`: JSONLogic condition.

Example:
```text
GETCOUNT
Par1: PENDING_TASKS
Par2: {"BEGDA":"=container.StartDate","ENDDA":"=container.EndDate"}
Par3: PendingCount
Par4:
```

## GETDEF

Reads a saved default value for a field or parameter.

Parameters:
- `Par1`: Field name.
- `Par2`: JSONLogic condition.

Example:
```text
GETDEF
Par1: EmployeeStatus
Par2:
```

## GETPERNR

Loads employee master data by personnel number and stores the result in `container.PNP`.

Parameters:
- `Par1`: Options object or personnel number reference.
- `Par2`: JSONLogic condition.

Notes:
- If `Par1` is empty, `container.UserPernr` is used.
- Variants such as `pernr`, `PERNR`, and `Pernr` are supported.

Example:
```text
GETPERNR
Par1: {"PERNR":"=container.SelectedPernr"}
Par2:
```

## GETREPORT

Runs a predefined report and returns its result set.

Parameters:
- `Par1`: Report code.
- `Par2`: JSONata expression for report parameters.
- `Par3`: JSONLogic condition.

Example:
```text
GETREPORT
Par1: ABSENCE_REPORT
Par2: {"PERNR":"=container.Pernr","BEGDA":"=container.Begda","ENDDA":"=container.Endda"}
Par3:
```

## GOURL

Navigates to another page, route, or URL.

Parameters:
- `Par1`: Target URL, route, or a special value such as `BACK`.
- `Par2`: JSONLogic condition.

Example:
```text
GOURL
Par1: BACK
Par2:
```

## INBOX

Creates a workflow inbox item for a user or role.

Parameters:
- `Par1`: JSONata expression that resolves to an inbox options object.
- `Par2`: JSONLogic condition.

Common options:
- `recipient`: Target user or role.
- `taskname`: Task title.
- `priority`: Priority level.
- `duedate`: Due date.

Example:
```text
INBOX
Par1: {"recipient":"MANAGER","taskname":"Approve leave","priority":"H"}
Par2:
```

## INFTY

Reads, creates, updates, or deletes personnel infotype data.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: Data payload for insert or update operations.
- `Par3`: JSONLogic condition.

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

## ME

Loads the current logged-in user’s employee data.

Parameters:
- `Par1`: Optional options object.
- `Par2`: JSONLogic condition.

Example:
```text
ME
Par1:
Par2:
```

## MESSAGE

Shows a message to the user in the UI.

Parameters:
- `Par1`: Message title.
- `Par2`: Message body.
- `Par3`: Error flag.
- `Par4`: JSONLogic condition.

Example:
```text
MESSAGE
Par1: Warning
Par2: Employee status is inactive.
Par3: false
Par4: {"==":[{"var":"container.Status"},"INACTIVE"]}
```

## OINFTY

Reads or writes Organizational Management infotype data.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: Data payload.
- `Par3`: JSONLogic condition.

Common options:
- `INFTY`, `COM`, `OBJID`, `BEGDA`, `ENDDA`, `SUBTY`, `SEQNR`, `PLVAR`, `OTYPE`.

Example:
```text
OINFTY
Par1: {"INFTY":"1000","COM":"GET","OBJID":"=container.Objid"}
Par2:
Par3:
```

## PBINFTY

Updates applicant infotype data used in the applicant module.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: Data payload.
- `Par3`: JSONLogic condition.

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
Par3:
```

## PDF

Generates a PDF document from a template and data.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: JSONLogic condition.

Common options:
- `template`: Template code.
- `pernr`: Personnel number.
- `begda`, `endda`: Date range.

Example:
```text
PDF
Par1: {"template":"LEAVE_REPORT","pernr":"=container.Pernr","begda":"20240101","endda":"20240131"}
Par2:
```

## PEOPLE

Works on a filtered employee list.

Parameters:
- `Par1`: JSONata filter expression or selection object.
- `Par2`: JSONLogic condition.

Example:
```text
PEOPLE
Par1: {"Orgeht":"1000","Status":"ACTIVE"}
Par2:
```

## PJSON

Transforms JSON data and stores the result in a target variable or object.

Parameters:
- `Par1`: JSONata expression.
- `Par2`: Target variable or property path.
- `Par3`: JSONLogic condition.

See also: [Common JSONata Examples](#common-jsonata-examples)

Example:
```text
PJSON
Par1: $filter(container.Employees, function($e){$e.Salary > 50000})
Par2: HighEarners
Par3:
```
## PROG

Calls program defined in Par1 with consitions in Cond column.

Parameters:
- `Par1`: Name of program.
- `Par4`: Conditions.


Example:
This executes ZHR_01, calls program with page and container objects.
```text
PROG
Par1: ZHR_01
Cond:
```

## PRINT

Prints the requested object to the log output.

Parameters:
- `Par1`: Object name such as `CONT` or `PAGE`.

Example:
```text
PRINT
Par1: CONT
```

## RUNPY

Runs the payroll calculation schema.

Parameters:
- `Par1`: Payroll options object or JSONata expression.
- `Par2`: JSONLogic condition.

Common options:
- `PERNR`: Personnel number.
- `BEGDA`, `ENDDA`: Payroll period dates.
- `PAYTY`: Payroll type.

Example:
```text
RUNPY
Par1: {"PERNR":"12345","BEGDA":"20240101","ENDDA":"20240131"}
Par2:
```

## RUNTE

Runs the time evaluation schema.

Parameters:
- `Par1`: Time evaluation options object or JSONata expression.
- `Par2`: JSONLogic condition.

Common options:
- `PERNR`: Personnel number.
- `BEGDA`, `ENDDA`: Period dates.
- `TEVTY`: Time evaluation type.

Example:
```text
RUNTE
Par1: {"PERNR":"12345","BEGDA":"20240101","ENDDA":"20240131","TEVTY":"MONTHLY"}
Par2:
```

## SETDATA

Assigns a value to a page field or object.

Parameters:
- `Par1`: Field name.
- `Par2`: Value to assign.
- `Par3`: JSONLogic condition.

Value forms:
- Plain string.
- JSON object or array.
- Container reference starting with `=`.
- System values such as `SY-DATUM` or `SY-UNAME`.

Example:
```text
SETDATA
Par1: EmployeeStatus
Par2: ACTIVE
Par3:
```

## SETDEF

Stores the current value of a field as the user’s default.

Parameters:
- `Par1`: Field name.
- `Par2`: JSONLogic condition.

Example:
```text
SETDEF
Par1: EmployeeStatus
Par2:
```

## SETENB

Enables or disables a field.

Parameters:
- `Par1`: Field name.
- `Par2`: Boolean enable flag.
- `Par3`: JSONLogic condition.

Example:
```text
SETENB
Par1: SalaryField
Par2: false
Par3: {"==":[{"var":"container.UserRole"},"Viewer"]}
```

## SETHDR

Sets the page or report header title.

Parameters:
- `Par1`: Header text.

Example:
```text
SETHDR
Par1: Employee Information
```

## SETOP

Populates a field with dynamic option values.

Parameters:
- `Par1`: Field name.
- `Par2`: JSON options object.
- `Par3`: Predefined option set code.
- `Par4`: JSONLogic condition.

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
Par4:
```

## SETPROP

Sets multiple properties on a field or component.

Parameters:
- `Par1`: Field name.
- `Par2`: Property object or JSONata expression.
- `Par3`: JSONLogic condition.

Example:
```text
SETPROP
Par1: ErrorField
Par2: {"color":"red","fontWeight":"bold"}
Par3:
```

## SETPROP1

Sets a single property on a field or component.

Parameters:
- `Par1`: Field name.
- `Par2`: Property name.
- `Par3`: Property value.
- `Par4`: JSONLogic condition.

Example:
```text
SETPROP1
Par1: Title
Par2: fontSize
Par3: 16px
Par4:
```

## SETRQ

Marks a field as required or optional.

Parameters:
- `Par1`: Field name.
- `Par2`: Required flag.
- `Par3`: JSONLogic condition.

Example:
```text
SETRQ
Par1: EmployeeID
Par2: true
Par3:
```

## SETVS

Shows or hides a field.

Parameters:
- `Par1`: Field name.
- `Par2`: Visible flag.
- `Par3`: JSONLogic condition.

Example:
```text
SETVS
Par1: InternalNotes
Par2: false
Par3: {"==":[{"var":"container.UserRole"},"Employee"]}
```

## TABLE

Reads records from a database table and stores them in the container.

Parameters:
- `Par1`: Tenant ID.
- `Par2`: Model or table code.
- `Par3`: JSONata filter expression.
- `Par4`: Container key for the result.
- `Par5`: JSONLogic condition.

Example:
```text
TABLE
Par1: =container.TenantId
Par2: Employee
Par3: $filter(records, function($r){$r.Status="ACTIVE"})
Par4: ActiveEmployees
Par5:
```

## TASK

Creates a workflow task record and stores workflow data with it.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Par2`: Workflow data payload.
- `Par3`: JSONLogic condition.

Common options:
- `resotype`, `resobjid`: Responsible object type and identifier.
- `wfstatus`, `instatus`, `currentstatus`: Workflow state fields.
- `isactive`, `completetask`, `newtask`: Task flow control flags.

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

## TRX

Starts a database transaction.

Parameters:
- `Par1`: JSONLogic condition.

Example:
```text
TRX
Par1:
```

## WFLOAD

Loads a workflow definition from the database.

Parameters:
- `Par1`: JSONLogic condition.

Example:
```text
WFLOAD
Par1:
```

## WFSTART

Starts a new workflow instance.

Parameters:
- `Par1`: Workflow status or JSONLogic condition depending on schema usage.
- `Par2`: Optional JSONLogic condition.

Example:
```text
WFSTART
Par1: NEW
Par2:
```

## WFSTEP

Adds or updates a workflow step.

Parameters:
- `Par1`: Task or step name.
- `Par2`: Direct JSON options object.
- `Par3`: Responsible agent or user.
- `Par4`: JSONLogic condition.

Example:
```text
WFSTEP
Par1: ApproveRequest
Par2: {"status":"NEW"}
Par3: MANAGER
Par4:
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
Par3: {"==":[{"var":"container.UserRole"},"Admin"]}
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
Par3:
```

---

## Notes

- Most functions accept JSONata-based option values when a parameter starts with `=`.
- Most conditional checks use JSONLogic.
- Several functions write into `page`, `container`, or both.
- Some seeded function codes exist in the seed file but are not implemented as `fu*.ts` files in this folder.
