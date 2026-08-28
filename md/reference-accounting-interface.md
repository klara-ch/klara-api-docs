# Accounting Interface

1 operation of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### POST /core/latest/payroll-interface-file

Return payroll interface file

Return the payroll accounting interface file for a given salary run or payslips in CSV or JSON format

- Required permission: `ACCOUNTING_INTERFACE`
- Roles that carry it: Administrator, External user, Mitarbeiter und Lohn

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `file-format` | query | no | object | File format |
| `payslip-ids` | query | no | string | List of payslip ids, separate by comma |
| `salary-run-id` | query | no | integer | Salary run id |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/octet-stream | Return the payroll accounting interface file for a given salary run or payslips in CSV or JSON format |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | ErrorMessage | Resource not found |
| `429` | none declared | API rate limit exceeded |
| `500` | none declared | Something went wrong on our side while processing the request. Please kindly contact our support. |
