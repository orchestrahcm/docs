# Common JSONLogic Examples

These examples cover the most common condition patterns used across schema functions.

## Equality Check

Use this when you want to continue only if a value matches exactly.

```json
{"==":[{"var":"container.UserRole"},"Admin"]}
```

## Not Equal Check

Use this when one value must differ from another.

```json
{"!=":[{"var":"container.Status"},"INACTIVE"]}
```

## String Contains / Text Match

Use `in` for membership checks and `contains` for text containment when supported by your JSONLogic implementation.

```json
{"in":["HR",{"var":"container.RoleList"}]}
```

## Logical AND

Use this when all conditions must be true.

```json
{"and":[
	{"==":[{"var":"container.UserRole"},"Manager"]},
	{"==":[{"var":"container.IsActive"},true]}
]}
```

## Logical OR

Use this when any one of the conditions is enough.

```json
{"or":[
	{"==":[{"var":"container.UserRole"},"Admin"]},
	{"==":[{"var":"container.UserRole"},"SuperUser"]}
]}
```

## Boolean Presence Check

Use this to require a flag or truthy value.

```json
{"!!":{"var":"container.ConfirmDelete"}}
```

## Empty Value Check

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

## Value Membership

Use this for allow-lists and deny-lists.

```json
{"in":[{"var":"container.CountryCode"},["TR","DE","NL"]]}
```

## Combined Condition Example

Use this pattern when a function should run only for a specific user role and a valid data state.

```json
{"and":[
	{"==":[{"var":"container.UserRole"},"Manager"]},
	{"==":[{"var":"container.Status"},"APPROVED"]},
	{"!!":{"var":"container.RequestId"}}
]}
```

## Typical Usage Pattern in Functions

Most functions accept the JSONLogic object in the last parameter and evaluate it before continuing.

```text
SETENB
Par1: SalaryField
Par2: true
Par3: {"==":[{"var":"container.UserRole"},"Admin"]}
```
