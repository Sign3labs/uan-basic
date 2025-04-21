# uan-basic
This document outlines how to integrate with the UAN Basic API provided by Sign3. The API accepts PAN / Mobile and returns various details for UAN (Employment) related to given PAN / Mobile.

---

## Endpoint

```
POST https://you.sign3.in/v1/uan_basic
```

---

## Authorization

This API requires **Basic Auth** via the `Authorization` header.

> Replace the encoded string with your own API credentials.

```
Authorization: Basic <Base64(tenantId:tenantSecret)>
```

---

## Request

### Headers

| Header              | Value                                                                 |
|---------------------|-----------------------------------------------------------------------|
| `Content-Type`      | `application/json`                                                    |
| `Authorization`     | `Basic <your_api_token>`                                              |

### Body

| Key           | Value                                                                                                          | Optional     |
|--------------|-----------------------------------------------------------------------------------------------------------------|--------------|
| `pan_number` | `PAN Number Length: 10 Pattern:^([a-zA-Z]{3})([abcfghljpteABCFGHLJPTE]{1})([a-zA-Z]{1})([0-9]{4})([a-zA-Z]{1})$`| true         |
| `phone`      | `Valid Indian mobile number (10 digit)`                                                                         | true         |

#### Notes: either phone or pan_number is required



```json
{
  "pan_number": "FQYPK****K",
  "phone": "9671145**6"
}
```

---

## Example cURL Request

```bash
curl --location 'https://you.sign3.in/v1/uan_basic' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic <your_api_token>' \
--data '{
    "pan_number": "FQYPK****K",
    "phone": "9671145**6"
}'
```

---

## Successful Response cases (200)

### If provided PAN or Phone is valid and we are able to fetch details related to same.

```json
{
  "request_id": "93b9226e-171e-4835-9ea5-d44f00f0bfdc",
  "uan_basic_details": {
        "user_exist": true,
        "uan_list": [
            "101124***38*"
        ],
        "summary": {
            "recent_employer_data": {
                "member_id": "DSNHP0023**400**017***",
                "establishment_id": "DSNHP002***4000",
                "date_of_exit": "2022-07-01",
                "date_of_joining": "2021-02-11",
                "establishment_name": "xyz",
                "matching_uan": "10112460***6"
            },
            "matching_uan": "10112460***6",
            "is_employed": false,
            "uan_count": 1,
            "date_of_exit_marked": true
        },
        "uan_details": {
            "101124609386": {
                "basic_details": {
                    "gender": "MALE",
                    "date_of_birth": "1995-01-01",
                    "name": "Manish KUMAR",
                    "mobile": "",
                    "aadhaar_verification_status": -1
                },
                "employment_details": {
                    "member_id": "DSNHP0023**400**017***",
                    "establishment_id": "DSNHP002***4000",
                    "date_of_exit": "2022-07-01",
                    "date_of_joining": "2021-02-11",
                    "leave_reason": "",
                    "establishment_name": "xyz"
                }
            }
        },
        "uan_source": [
            {
                "uan": "10112460***6",
                "source": "pan and mobile"
            }
        ]
    }
}
```

### Response Field Details

| Field                               | Type      | Description                                       |
|-------------------------------------|---------- |---------------------------------------------------|
| `request_id`                        | string    | Unique identifier for the API request             |
| `uan_basic_details`                 | dict      | contains all UAN details for provided input     |
| `.user_exist`                       | bool      | Bool indicating if UAN exists for provided input                    |
| `.uan_list`                         | list      | Array containing all found UAN (string) for given input.    |
| `.summary`                          | dict      | Final result with current employer details and employee   |
| `..matching_uan`                    | string    | UAN with respect to summary is fetched |
| `..is_employed`                     | bool      | true / false (employee hasn’t exited from the company then true also employee PF filing is found in the latest 3 months else false)|
| `..uan_count`                       | int       | count of UAN numbers found |
| `..date_of_exit_marked`             | bool      | if date of exit is marked or not |
| `..recent_employer_data`            | dict      | Recent employer data like name, establishment id and confidence score              |
| `...establishment_id`               | string    | Establishment ID of the current organization             |
| `...establishment_name `            | string    | Name of the establishment linked with member id of the customer               |
| `...date_of_exit`                   | string    | yyyy-mm-dd date of exit from the company |
| `...date_of_joining`                | string    | yyyy-mm-dd date of joining with the employer |
| `...matching_uan`                   | string    | matching UAN with recent employer |
| `...member_id`                      | string    | Member id of the employee in the related establishment    |
| `..uan_details`                     | dict      | Dictionary with UAN in the key and user details linked with that UAN number              |
| `...{<uan_number>}`                 | dict      | Dictionary with user details linked              |
| `....basic_details`                 | dict      | Dictionary consisting basic details linked to UAN number              |
| `.....gender`                       | string    | M/F/T gender of the user M - Male F - Female T - Third gender              |
| `.....date_of_birth`                | string    | yyyy-mm-dd date of birth of the user         |
| `.....name`                         | string    | Name of the user linked with UAN         |
| `.....aadhaar_verification_status`  | int       | -1 aadhaar verification status of the employee(user) -1 - No data found        |
| `.....mobile`                       | string    | Mobile number linked with UAN Note: Currently the Mobile number linked with the UAN is not provided by the Source and hence it is given as an empty string. It will become available once the source makes it available.         |
| `....employment_details`            | dict      | Consists details of the employer like establishment_name,establishment_id,member_id      |
| `.....member_id`                    | string      | Member id of the employee in the related establishment     |
| `.....establishment_id`             | string      | Establishment ID of the current organization     |
| `.....date_of_exit`                 | string      | yyyy-mm-dd date of exit from the company     |
| `.....date_of_joining`              | string      | yyyy-mm-dd date of joining with the employer     |
| `.....leave_reason`                 | string      | Leave reason marked by the employer     |
| `.....establishment_name`           | string      | Name of the establishment linked with member id of the customer     |
| `..uan_source`                      | list        | List of dictionaries with the UAN number source explaining from which source the linked UAN number is found.       |
| `...{uan}`                          | string      | UAN linked with the source       |
| `...{source}`                       | string      | Source of the UAN number   |

---

## No UAN Details Found for given PAN/phone input

```json
{
  "request_id": "39e3e6b0-6c26-4614-bdda-83b8fafd4d79",
  "uan_basic_details": {
    "user_exist": false
  }
}
```

---

## Some error occured while getting UAN details

```json
{
  "request_id": "39e3e6b0-6c26-4614-bdda-83b8fafd4d79",
  "uan_basic_details": {
    "error": true
  }
}
```

---

## Invalid input

```json
{
  "request_id": "39e3e6b0-6c26-4614-bdda-83b8fafd4d79",
  "uan_basic_details": {
    "error_msg": "Invalid Login id"
  }
}
```


## Other Possible non 200 response codes.

### 4XX
```json
{"error_msg": "Bad Request"}
```

### 5XX
```json
{"error_msg": "Sign3 Server Error" || <message related to error>}
```

---

## Notes

- Ensure that your PAN/phone input is in valid format.

---

## Changelog

### April 21, 2025

- Initial release of Sign3 UAN Basic api.
- Added `request curl`, `header` and `payload` details.
- Included UAN details response structure and field explanations.
- Provided different response examples.

---
