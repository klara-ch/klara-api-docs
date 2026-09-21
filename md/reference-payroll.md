# Payroll

3 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### GET /core/v1/employees/short-info

List the company's employees as short-info entries, filtered and sorted.

Returns a lightweight directory of the authenticated company's employees (id, name, email, employee number, workplace, status), resolved from the compensation and person records. Each entry's `id` is the `employeeId` consumed by other employee-scoped endpoints (e.g. `GET /payroll/employees/{employeeId}/addable-salary-items`) — call this endpoint first to resolve an employee's id. Results can be narrowed with a free-text `search-key` (matched against name, employee number and email), restricted to specific workplaces via `workplace-ids`, and filtered by contract `status`; ordering is controlled by `sort-field` and `sort-direction`. The tenant and company are derived from the authenticated token; the caller must hold the `COMPENSATION_TIME_TRACKING` permission on that company.

- operationId: `listEmployeesShortInfo`
- Required permission: `COMPENSATION_TIME_TRACKING`
- Roles that carry it: Administrator, External user, Mitarbeiter und Lohn, Zeit- und Materialrapporte

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `filter-by-status` | query | no | string | Renamed to `status`. This name is accepted until 2027-09-01; when both are sent, `status` wins. After that date, a request sending `filter-by-status` without `status` is rejected with 400. Deprecated — use `status` instead. Contract-status filter. Allowed values: ALL, ACTIVE, INACTIVE, DRAFT. Defaults to ALL. |
| `search-key` | query | no | string | Case-insensitive substring matched against the employee's full name, employee number and email. When omitted, no text filter is applied. |
| `sort-direction` | query | no | SortDirection | Sort direction. Allowed values: ASC, DESC. |
| `sort-field` | query | no | string | Field to sort by. Allowed values: NAME, EMAIL, EMPLOYEE_NUMBER. Defaults to NAME. |
| `status` | query | no | string | Contract-status filter. Allowed values: ALL, ACTIVE, INACTIVE, DRAFT. Defaults to ALL. |
| `workplace-ids` | query | no | array | Restrict results to employees whose current contract workplace is in this set. Repeat the parameter for multiple ids (e.g. workplace-ids=12&workplace-ids=13). When omitted, employees of all workplaces are returned. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Successful operation |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/v1/payroll/contracts/{contractId}/payslips/{payslipId}/salary-items

Add a salary item to an employee's payslip.

Adds a single salary item (Base / Percent / Quantity / Amount / Comments) to the employee's editable (non-sealed) payslip identified by `payslipId`, then recalculates that month unless `recalculate=false`. Returns the added salary item with its server-assigned `id`. This operation is not idempotent: repeated calls append duplicate items or throw an error when the item is not duplicatable (subject to the item's duplicatable flag). Prerequisite: call `GET /payroll/employees/{employeeId}/addable-salary-items` first — its response provides the `contractId` and `payslipId` to target and the addable `code`s; resolve `employeeId` beforehand via `GET /employees/short-info`. The tenant and company are derived from the authenticated token; the caller must hold the `COMPENSATION` permission on that company.

- operationId: `addSalaryItem`
- Required permission: `COMPENSATION`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `contractId` | path | yes | integer | Id of the employee's contract to add the salary item to. Obtained from the `contractId` field of the `GET /payroll/employees/{employeeId}/addable-salary-items` response — call that endpoint first to select the employee and obtain this id. |
| `payslipId` | path | yes | integer | Id of the target (editable, non-sealed) payslip. Obtained from the `payslipId` field of the `GET /payroll/employees/{employeeId}/addable-salary-items` response — call that endpoint first to select the employee and obtain this id. |
| `recalculate` | query | no | boolean | Whether to recalculate the payslip after adding the item. Defaults to true. |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `code` | string | yes | Salary-type code identifying the item to add. Must be one of the codes returned by GET /payroll/employees/{employeeId}/addable-salary-items (salaryItems[].code). |
| `salaryItemValues` | object | no |  |
| `remark` | string | no | Optional free-text comment (GUI "Comments"). |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PayslipSalaryItem | Successful operation |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Error codes for this response (the `code` field of the response body):`auth.credentials_missing` — Neither an Authorization: Bearer header nor an API key header was present on the request.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/payroll/employees/{employeeId}/addable-salary-items

Get the salary items that can be added to an employee's payslip.

Resolves the employee's main (latest) contract and its editable payslip, then returns the salary-item types that can be added to that payslip as blank templates (numeric value fields cleared, distinct by `code`), together with the resolved `contractId` and `payslipId` so the response can be chained into the add-salary-item call. Prerequisite: call `GET /employees/short-info` first to resolve the `employeeId` path parameter from the returned entries' `id` field. When `month` is provided the specified month's payslip is used (read-only); when it is omitted the employee's current editable payslip is used, which may be computed on first access. The tenant and company are derived from the authenticated token; the caller must hold the `COMPENSATION` permission on that company.

- operationId: `listAddableSalaryItems`
- Required permission: `COMPENSATION`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `employeeId` | path | yes | integer | Id of the employee whose addable salary items are resolved. Obtained from the `id` field of an entry returned by `GET /employees/short-info`. |
| `month` | query | no | string | Payslip month in the format MM.yyyy (e.g. 06.2025). If omitted, the employee's current editable payslip is used. |
| `Accept-Language` | header | no | string | Preferred language for localised salary-item descriptions, as an IETF language tag. Examples: de-CH, fr-CH, it-CH, en. Defaults to the tenant's language when omitted. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | AddablePayslipSalaryItems | Successful operation |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
