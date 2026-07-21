# Common JSONata Examples

These examples cover the JSONata patterns that appear most often in scheme functions such as `PJSON`, `SETDATA`, `TABLE`, `GETREPORT`, `INBOX`, `RUNPY`, and `RUNTE`.

## Simple Field Access

Read a value directly from the container.

```text
container.EmployeeName
```
## Read Page Query Param

Read page query paramater in URL, here com=... is read

```text
page.QueryParams.com
```
## Read Page Element Data

Read page element data, FieldName = SLART Data prop read

```text
page.Elements[FieldName="SLART"].Data
```

## String Concatenation

Build a combined value from multiple fields.

```text
container.FirstName & ' ' & container.LastName
```

## Conditional Value

Return one value when the condition is true and another when it is false.

```text
$boolean(container.IsActive) ? 'ACTIVE' : 'INACTIVE'
```

## Filter Array Items

Keep only the rows that match the condition.

```text
$filter(container.Employees, function($e){$e.Salary > 50000})
```

## Map Array Items

Transform each item in a collection.

```text
$map(container.Employees, function($e){
	{
		"Value": $e.Pernr,
		"Text": $e.FirstName & ' ' & $e.LastName
	}
})
```

## Object Construction

Create a new object from existing values.

```text
{
	"PERNR": container.Pernr,
	"NAME": container.FirstName & ' ' & container.LastName,
	"STATUS": container.Status
}
```

## Fallback Value

Use a default when the primary value is empty or missing.

```text
container.AppName ? container.AppName : 'UNKNOWN'
```

## Nested Lookup

Access a property inside a nested structure.

```text
container.Person.Address.City
```

## Array Join

Combine array values into a single string.

```text
$join(container.RoleList, ', ')
```

## Number Conversion

Convert a string to a number before using it in math operations.

```text
$number(container.Amount)
```

## Typical PJSON Pattern

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

