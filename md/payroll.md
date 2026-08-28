# Submitting variable payroll data

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#payroll

## Submitting variable payroll data

> **Note: Available now**
>
> Payroll used to be read-only through file exports. Three endpoints let you add variable salary items (hours, allowances, bonuses) directly to an employee's payslip.

The flow is three calls. You need the employee first, then the payslip that is still editable, then you post the item.

```
1. GET  /core/v1/employees/short-info
      → find the employee, take their employeeId

2. GET  /core/v1/payroll/employees/{employeeId}/addable-salary-items
      → returns which item types may be added, plus contractId and payslipId

3. POST /core/v1/payroll/contracts/{contractId}/payslips/{payslipId}/salary-items
      → adds the item and recalculates the month
```

### What you can submit

Salary items come in the types **Base**, **Percent**, **Quantity**, **Amount** and **Comments**. Step 2 tells you which types the payslip accepts. Do not guess, the answer depends on the employee's contract.

### Finding the right employee

`GET /core/v1/employees/short-info` returns a lightweight directory: id, name, email, employee number, workplace and status. It supports `search-key`, `filter-by-status`, `workplace-ids`, `sort-field` and `sort-direction`. This is the most capable filtering in the whole API today.

> **Warning: Two constraints worth knowing before you build**
>
> - **Only editable payslips.** Once a payslip is sealed, it no longer accepts items. Your integration needs to run before the salary run is closed, so plan the timing.
>
> - **Recalculation is triggered per call.** Pass `recalculate=false` when adding several items in sequence, then recalculate once at the end. Otherwise you recompute the month for every single item.

### Retries and corrections

> **Warning: Adding a salary item is not idempotent**
>
> A repeated call appends a second item, or fails if the item type is not duplicatable. There is no idempotency key. If a call times out, read the payslip back before you send it again rather than retrying blind. With no test environment, a duplicate is real payroll data.

There is no `PUT` and no `DELETE` on salary items today, so a wrong entry is corrected in the KLARA interface, not through the API. Both verbs are confirmed as required for the final version of the endpoint, but they do not exist yet.
