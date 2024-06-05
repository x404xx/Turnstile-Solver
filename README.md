<div align="center">

<img src="assets/logo.png" width="300" height="auto">

**Bypass-Turnstile** is a Rest-API that helps you bypass `Cloudflare Turnstile`

</div>

## Made With

-   [Quart](https://quart.palletsprojects.com/en/latest/index.html) - Using Hypercorn or an alternative ASGI server

## Features

| SERVICES                        | FUNCTION                                                       |
| ------------------------------- | -------------------------------------------------------------- |
| _**Cloudflare Checker**_        | To determine if a website is utilizing Cloudflare's services.  |
| _**Solve Turnstile Challenge**_ | To obtain a turnstile challenge token and solve it afterwards. |

## Endpoint

https://x404x-bye-turnstile-ae128f0c.koyeb.app/

## Parameter

```sh
check_cf
solve
```

## Usage Example (Reverse Engineer - https://haveibeenpwned.com)

<details>
<summary style="font-size: 19px; font-weight: bold;">Cloudflare Checker</summary>

```python
import httpx

ENDPOINT = "https://x404x-bye-turnstile-ae128f0c.koyeb.app/"
TARGET_URL = "https://haveibeenpwned.com/"

params = {"target_url": TARGET_URL}
response = httpx.get(f"{ENDPOINT}check_cf", params=params, timeout=20)
response_json = response.json()
print(response_json)

```

</details>

<details>
<summary style="font-size: 19px; font-weight: bold;">Output</summary>

```yml
{
    "code": 200,
    "cloudflare": True,
    "challenge": False,
    "chl-bypass": False,
    "cf-ray": True,
    "cf-cache": True,
    "cf-bm": True,
    "turnstile": True,
    "iuam": False,
    "turnstile_sitekey": "0x4AAAAAAADY3UwkmqCvH8VR",
}
```

</details>

<hr style="border-bottom: 1px solid yellow;">

<details>
<summary style="font-size: 19px; font-weight: bold;">Solve Turnstile Challenge</summary>

```python
from urllib.parse import quote_plus

import httpx

ENDPOINT = "https://x404x-bye-turnstile-ae128f0c.koyeb.app/"
TARGET_URL = "https://haveibeenpwned.com/"

email = input("Your email: ")
encoded_email = quote_plus(email)


params = {"target_url": TARGET_URL}
response = httpx.get(f"{ENDPOINT}check_cf", params=params, timeout=20)
response_json = response.json()
turnstile_sitekey = response_json.get("turnstile_sitekey")
if turnstile_sitekey is None:
    raise ValueError("Turnstile sitekey is not present")

json_data = {
    "sitekey": turnstile_sitekey,
    "url": TARGET_URL,
}

response = httpx.post(f"{ENDPOINT}solve", json=json_data, timeout=20)
response_json = response.json()
token = response_json.get("token")

headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/115.0",
    "Accept": "*/*",
    "Accept-Language": "en-US,en;q=0.5",
    "Referer": "https://haveibeenpwned.com/",
    "cf-turnstile-response": token,
    "X-Requested-With": "XMLHttpRequest",
    "DNT": "1",
    "Connection": "keep-alive",
    "Sec-Fetch-Dest": "empty",
    "Sec-Fetch-Mode": "cors",
    "Sec-Fetch-Site": "same-origin",
}

response = httpx.get(
    f"https://haveibeenpwned.com/unifiedsearch/{encoded_email}", headers=headers
)
if not response.is_success:
    print("Good news — no pwnage found!")
else:
    print(response.json())
```

</details>

<details>
<summary style="font-size: 19px; font-weight: bold;">Output</summary>

```yml
Your email: johnny.rotten@yahoo.com
{
    'Breaches': [
        {
            'Name': 'Collection1',
            'Title': 'Collection #1',
            'Domain': '',
            'BreachDate': '2019-01-07',
            'AddedDate': '2019-01-16T21:46:07Z',
            'ModifiedDate': '2019-01-16T21:50:21Z',
            'PwnCount': 772904991,
            'Description': 'In January 2019, a large collection of
credential stuffing lists (combinations of email addresses and passwords
used to hijack accounts on other services) was discovered being distributedon a popular hacking forum. The data contained almost 2.7 <em>billion</em>
records including 773 million unique email addresses alongside passwords
those addresses had used on other breached services. Full details on the
incident and how to search the breached passwords are provided in the blog
post <a
href="https://www.troyhunt.com/the-773-million-record-collection-1-data-reach" target="_blank" rel="noopener">The 773 Million Record "Collection #1"
Data Breach</a>.',
            'LogoPath':
'https://haveibeenpwned.com/Content/Images/PwnedLogos/List.png',
            'DataClasses': ['Email addresses', 'Passwords'],
            'IsVerified': False,
            'IsFabricated': False,
            'IsSensitive': False,
            'IsRetired': False,
            'IsSpamList': False,
            'IsMalware': False,
            'IsSubscriptionFree': False
        },
        {
            'Name': 'VerificationsIO',
            'Title': 'Verifications.io',
            'Domain': 'verifications.io',
            'BreachDate': '2019-02-25',
            'AddedDate': '2019-03-09T19:29:54Z',
            'ModifiedDate': '2019-03-09T20:49:51Z',
            'PwnCount': 763117241,
            'Description': 'In February 2019, the email address validation
service <a
href="https://securitydiscovery.com/800-million-emails-leaked-online-by-email-verification-service" target="_blank" rel="noopener">verifications.io
suffered a data breach</a>. Discovered by <a
href="https://twitter.com/mayhemdayone" target="_blank" rel="noopener">Bob
Diachenko</a> and <a href="https://twitter.com/vinnytroia" target="_blank"
rel="noopener">Vinny Troia</a>, the breach was due to the data being storedin a MongoDB instance left publicly facing without a password and resulted
in 763 million unique email addresses being exposed. Many records within
the data also included additional personal attributes such as names, phone
numbers, IP addresses, dates of birth and genders. No passwords were
included in the data. The Verifications.io website went offline during the
disclosure process, although <a
href="https://web.archive.org/web/20190227230352/https://verifications.io/"target="_blank" rel="noopener">an archived copy remains viewable</a>.',
            'LogoPath':
'https://haveibeenpwned.com/Content/Images/PwnedLogos/VerificationsIO.png',            'DataClasses': [
                'Dates of birth',
                'Email addresses',
                'Employers',
                'Genders',
                'Geographic locations',
                'IP addresses',
                'Job titles',
                'Names',
                'Phone numbers',
                'Physical addresses'
            ],
            'IsVerified': True,
            'IsFabricated': False,
            'IsSensitive': False,
            'IsRetired': False,
            'IsSpamList': False,
            'IsMalware': False,
            'IsSubscriptionFree': False
        }
    ],
    'Pastes': None
}
```

</details>

## Legal Disclaimer

> [!Note]
> This was made for educational purposes only, nobody which directly involved in this project is responsible for any damages caused. **_You are responsible for your actions._**
