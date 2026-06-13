[Dentist Lead Scraper](https://apify.com/crawlerbros/dentist-lead-scraper?fpr=data)

# Dentist & Healthcare Lead Scraper

Scrape **dentist, doctor, clinic, orthodontist, chiropractor, veterinarian, pharmacy, physical therapist, dermatologist, optometrist** (or any custom query) leads from Google Maps. Each lead is enriched with **email addresses** and **social media profiles** from the business's website.

## Input

| Field | Type | Description |
| --- | --- | --- |
| `businessType` | enum | Dentist / Doctor / Clinic / Orthodontist / Chiropractor / Veterinarian / Pharmacy / Physical Therapist / Dermatologist / Optometrist / Custom |
| `location` | string | City, state, or ZIP (e.g. "Austin, TX"). Required. |
| `searchQuery` | string | Custom query used when businessType = "Custom". |
| `maxResults` | integer | Max leads per run (1-500, default 50). |
| `enrichEmails` | boolean | Visit each website to extract emails. Default on. |
| `enrichSocials` | boolean | Extract social-profile URLs. Default on. |
| `outputFormat` | enum | `full`, `hubspot`, `salesforce`. |
| `language` | enum | Google Maps language (`en`, `es`, `fr`, `de`, `it`, `nl`, `pt`, `pl`, `ja`). |

## Output (full format)

Per lead: `name`, `category`, `address`, `mapsUrl`, `phone`, `website`, `rating`, `reviewCount`, `placeId`, `cid`, `latitude`, `longitude`, `email`, `allEmails`, `socialLinks` (object with facebook / instagram / linkedin / twitter / youtube / tiktok), `businessType`, `scrapedAt`.

**HubSpot format**: `company`, `address`, `phone`, `website`, `email`, `industry`, `hs_lead_status`, `hs_object_source`, `hs_latitude`, `hs_longitude`, `facebook_url`, `linkedin_url`, `instagram_url`, `twitter_url`.

**Salesforce format**: `Company`, `Street`, `Phone`, `Website`, `Email`, `Industry`, `LeadSource`, `Latitude__c`, `Longitude__c`, `Facebook__c`, `LinkedIn__c`.

## How it works

1. Build the Google Maps search query from `businessType` + `location` (or custom).
2. Launch stealth Chromium and collect up to `maxResults` place cards (name, address, phone, rating, website, coordinates).
3. For each place with a website, crawl the homepage + common contact pages (`/contact`, `/about`) via `httpx` and extract `mailto:` emails, plain-text emails, and social profile links.
4. Normalise into the selected output format and push one record per lead.

## FAQ

**Do I need a proxy?** No — the scraper tries direct first and auto-escalates to Apify RESIDENTIAL US if Google blocks the datacenter.
**Why some leads have no email?** Not every business has an email on their website, and placeholder / `noreply@…` values are filtered out.