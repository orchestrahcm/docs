# Scheme Functions Reference

This document describes the scheme functions implemented in this folder. Each entry includes a short purpose summary, parameter usage, and a compact example.

## Function Index

Functions used in schemes.

- [API](#api) - API Call
- [AUTH](#auth) - Authorization Check
- [BATCH](#batch) - Batch Input
- [COM](#com) - Comment
- [COMMIT](#commit) - Transaction Commit
- [DELPERNR](#delpernr) - Delete Person
- [GENWS](#genws) - Generate Shift
- [GETCOUNT](#getcount) - Important Figures
- [GETDEF](#getdef) - Get Default
- [GETPARAM](#getparam) - Get Parameter
- [GETPERNR](#getpernr) - Get Employees
- [GETREPORT](#getreport) - Report Retrieval
- [GETNODE](#getnode) - Gets selected node object data
- [GOURL](#gourl) - Go to URL
- [INBOX](#inbox) - Inbox
- [INFTY](#infty) - Infotype Operation
- [ME](#me) - My Employee Data
- [MSG](#msg) - Show Message
- [NODE](#node) - Adds node to reportview
- [OINFTY](#oinfty) - OM Infotype Operation
- [PBINFTY](#pbinfty) - Applicant Infotype Operation
- [PDF](#pdf) - Generate PDF
- [PEOPLE](#people) - People
- [PJSON](#pjson) - Process JSON
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
- [NODE](#node) - Create Node in TreeView

- [Common JSONLogic Examples](#common-jsonlogic-examples)

---

## API

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

Writes a comment line into the schema log. This function has no affect in algorithm.

Parameters are not used in this function.
Use comment area only.

Example:
```text
COM
Comment: Starting employee processing
```

## COMMIT (Obsolote)

Commits the active database transaction started with `TRX`.

Parameters:
- `Cond`: JSONLogic condition. StateId.

Example:
```text
COMMIT
Par1: {"==":[{"var":"container.Success"},true]}
```

## DELPERNR

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

Loads employee master data by personnel number and stores the result in `container.PNP`.

Parameters:
- `Par1`: Options object or personnel number reference.
- `Cond`: JSONLogic condition. StateId.

Notes:
- If `Par1` is empty, `container.UserPernr` is used.
- Variants such as `pernr`, `PERNR`, and `Pernr` are supported.

Example:
```text
GETPERNR
Par1: 
{
	"PERNR": container.SelectedPernr
}
Cond: state01
```
```text
GETPERNR
{
"PERNR": page.Elements[FieldName="PERNR"].Data
}
Cond: 

## GETREPORT

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

## ME (Obsolote)

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

Parameters:
- `Par1`: Options.
- `Cond`: JSONLogic condition.

Adds node object to reportview page element.

```text
{
     "id": "folder0", //node id
     "fieldname": "repTree", //report viewer fieldname
     "icon": "glyphs-poly:folder",
     "label": "Legal Report",   
     "pid": "#". //parent node id
}
```

## OINFTY

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

Generates a PDF document from a template and data.

Parameters:
- `Par1`: Options object or JSONata expression.
- `Cond`: JSONLogic condition.

Common options:
- `template`: Template code.
- `pernr`: Personnel number.
- `begda`, `endda`: Date range.

Example:
```text
PDF
Par1: {"template":"LEAVE_REPORT","pernr":"=container.Pernr","begda":"20240101","endda":"20240131"}
Cond:
```

## PEOPLE

Works on a filtered employee list.

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

## SETDATA

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

## TABLE

Reads records from a database table and stores them in the container.

Parameters:
- `Par1`: Tenant ID.
- `Par2`: Model or table code.
- `Par3`: JSONata filter expression.
- `Par4`: Container key for the result.
- `Cond`: JSONLogic condition.

Example:
```text
TABLE
Par1: =container.TenantId
Par2: Employee
Par3: $filter(records, function($r){$r.Status="ACTIVE"})
Par4: ActiveEmployees
Cond:
```

## TASK

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

## NODE

Inserts new node in treeview.

Parameters:
- `Par1`: Node options.

Example:
```text
{
	"fieldname" :"reporttree1",
	"icon" :"mdi:folder",
	"label" :"ESS Reports",
	"pid" :"#"
}


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
- Some seeded function codes exist in the seed file but are not implemented as `fu*.ts` files in this folder.
