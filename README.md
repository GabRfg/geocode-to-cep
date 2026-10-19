# Geocode to CEP

Python script that fills in missing Brazilian postal codes (CEP) in a spreadsheet by reverse-geocoding each row's latitude and longitude with the Google Geocoding API.

**Stack:** Python · pandas · requests · Google Geocoding API

---

## The problem

A dataset had reliable coordinates but missing or inconsistent postal codes. Fixing them by hand wasn't viable, and the coordinates were already the most trustworthy field — so the CEP could be derived from them.

## How it works

1. Reads the Excel file with pandas
2. Drops rows without `latitude` or `longitude`
3. For each row, calls the Geocoding API (`latlng=lat,lon`) and extracts the `postal_code` component from the first result
4. Writes the result to a new `Zipcode` column and saves the spreadsheet
5. Prints progress and the total number of rows that got a CEP

Rows where the API finds no postal code (or the request fails) are left empty instead of stopping the run.

## Usage

**Requirements:** Python 3.9+ and a Google Cloud API key with the Geocoding API enabled.

```bash
pip install pandas requests openpyxl
```

Edit the two variables at the top of `geocode_to_cep.py`:

```python
api_key = 'YOUR_GOOGLE_API_KEY'
file_path = r'C:\path\to\your_file.xlsx'
```

The spreadsheet needs `latitude` and `longitude` columns. Then run:

```bash
python geocode_to_cep.py
```

**Input**

| latitude | longitude |
| --- | --- |
| -25.4284 | -49.2733 |

**Output**

| latitude | longitude | Zipcode |
| --- | --- | --- |
| -25.4284 | -49.2733 | 80010-000 |

> ⚠️ The script **overwrites the input file** and removes rows without coordinates. Keep a copy of the original.

## Notes

- One API request per row — check Google's pricing and quotas before running on large files. A delay between requests is available (commented `time.sleep`) to respect rate limits.

## Possible improvements

- [ ] Read the API key from an environment variable
- [ ] Take input and output paths as command-line arguments and write to a new file
- [ ] Cache repeated coordinates to save API calls
