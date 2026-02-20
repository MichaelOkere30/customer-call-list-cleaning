# Customer Call List – Data Cleaning Project

## Overview

This project focuses on cleaning a raw customer dataset to prepare it for use by a call centre team. The goal is to produce a reliable, well-formatted list of contactable customers — removing noise, fixing inconsistencies, and filtering out anyone who shouldn't be called.

---

## Dataset

**Source file:** `Customer Call List.xlsx`  
**Records:** 21 rows (20 unique after deduplication)  
**Columns:** CustomerID, First_Name, Last_Name, Phone_Number, Address, Paying Customer, Do_Not_Contact, Not_Useful_Column

---

## Cleaning Steps

### 1. Load the Data
The dataset was loaded from an Excel file using pandas. An initial inspection with `df.info()` revealed missing values in `Last_Name`, `Phone_Number`, and `Do_Not_Contact`, as well as inconsistent formatting throughout.

### 2. Remove Duplicates
One duplicate row was identified (Anakin Skywalker, CustomerID 1020) and removed using `df.drop_duplicates()`, bringing the dataset down to 20 records.

### 3. Drop Unnecessary Columns
The `Not_Useful_Column` field was dropped entirely, as it added no analytical value.

### 4. Clean the Last Name Column
Several last names contained stray punctuation (e.g. `/White`, `...Potter`, `Flenderson_`). These were stripped using `str.strip("123._/")`, producing clean values like `White`, `Potter`, and `Flenderson`.

### 5. Standardize Yes/No Columns
The `Paying Customer` and `Do_Not_Contact` columns contained a mix of values — `Yes`, `No`, `Y`, `N`. All values were standardised to `Y` / `N` for consistency using a replacement mapping.

### 6. Clean and Format Phone Numbers
Phone numbers were entered in multiple formats using different separators (`-`, `/`, `|`). All non-alphanumeric characters were stripped and numbers were reformatted uniformly as `XXX-XXX-XXX`. Null/missing entries were converted to empty strings.

### 7. Parse the Address Column
The `Address` column was split into three separate fields — `Street_Address`, `State`, and `Postal_Code` — using a comma delimiter with a max split of 2. This makes address components individually accessible for filtering or analysis.

### 8. Handle Missing and Null Values
- `nan--` and `Na--` artefacts in `Phone_Number` (created during formatting) were replaced with empty strings.
- Remaining `NaN` values across the dataframe were filled with empty strings using `df.fillna('')`.
- `N/a` strings in `Address`, `Paying Customer`, and `Street_Address` were cleared.

### 9. Replace Empty Do_Not_Contact with 'N'
Any blank entries in `Do_Not_Contact` were treated as implicitly contactable and replaced with `N`.

### 10. Drop Do-Not-Contact Rows
All rows where `Do_Not_Contact == 'Y'` were removed. These customers have explicitly opted out and must not be called.

### 11. Drop Rows with No Phone Number
Customers without a phone number are unreachable and were dropped from the dataset.

### 12. Reset the Index
After all filtering, the index was reset to produce a clean sequential order from 0.

### 13. Export Cleaned Data
The final dataset was exported to `Cleaned_Customer_Call_List.csv` for use by the call centre team.

---

## Final Output

The cleaned dataset contains **13 contactable customers**, ready for call centre use.

| CustomerID | First Name | Last Name  | Phone Number |
|------------|------------|------------|--------------|
| 1001       | Frodo      | Baggins    | 123-545-542  |
| 1003       | Walter     | White      | 706-695-039  |
| 1005       | Jon        | Snow       | 876-678-346  |
| 1008       | Sherlock   | Holmes     | 876-678-346  |
| 1010       | Peter      | Parker     | 123-545-542  |
| 1012       | Harry      | Potter     | 706-695-039  |
| 1013       | Don        | Draper     | 123-543-234  |
| 1014       | Leslie     | Knope      | 876-678-346  |
| 1015       | Toby       | Flenderson | 304-762-246  |
| 1016       | Ron        | Weasley    | 123-545-542  |
| 1017       | Michael    | Scott      | 123-643-977  |
| 1018       | Clark      | Kent       | 706-695-039  |
| 1020       | Anakin     | Skywalker  | 876-678-346  |

---

## Tools Used

- **Python 3**
- **pandas** – data manipulation and cleaning

---

## Key Decisions

- Customers with `Do_Not_Contact = Y` were removed regardless of whether they are paying customers, to respect their preference.
- Customers with no phone number were removed as they cannot be reached by a call centre.
- Creed Braton (CustomerID 1019) was removed because both their phone number and contact preference were missing, making them unreachable and unqualified.
- Phone numbers were truncated to 9 digits due to the raw data only containing 10-digit numbers and the formatting approach taking the first 9 characters — this could be revisited if full 10-digit formatting is required.
