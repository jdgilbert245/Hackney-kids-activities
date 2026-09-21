# Weekly update instructions

You maintain `events.json`, the data behind a public weekly calendar of activities for children in Hackney, London (and places just over the border that Hackney families use). Each run, update the file and open one pull request for a human to review. Nothing you do goes live until they merge it.

## What to do each run

1. Read `events.json` and `checked-by-hand.md`.
2. Remove any entries with `"example": true`.
3. Remove one-off events whose `date` is in the past, and recurring events whose `until` is in the past.
4. Extend `until` on recurring sessions that are still listed by their source.
5. Check every source below for children's and family activities from today up to four weeks ahead. Add new events and correct existing ones that have changed (times, prices, cancellations).
6. Run a few open web searches (e.g. "kids activities Hackney this week", "family events Hackney half term") for anything the sources miss.
7. Set `"updated"` to today's date.
8. Check the file is valid: `python3 -m json.tool events.json`.
9. Create a branch named `weekly-update-YYYY-MM-DD`, commit, push, and open a pull request with `gh pr create`.

## Sources

- Hackney children's centre timetables (PDFs on education.hackney.gov.uk). These are termly; use the term dates as `from` and `until`.
- Hackney Libraries events
- Young Hackney (youth hubs, adventure playgrounds, holiday programmes)
- Hackney SEND Local Offer "What's on" (shortbreaks.hackney.gov.uk)
- Britannia Leisure Centre and Clissold Leisure Centre (Stoke Newington): family swims, soft play, kids' sessions
- Museum of the Home, Hoxton
- Young V&A, Bethnal Green
- Hackney Museum, Sutton House, Hackney City Farm
- Baby and parent-and-baby cinema screenings: Hackney Picturehouse, Rio Cinema (Dalston), and any others in or near Hackney
- Eventbrite searches for children's and family events in Hackney
- Love Hackney magazine - https://www.lovehackney.uk/whats-on

## Drop-in venues

Places families can visit any time they're open (museums, city farms, adventure playgrounds with open sessions) are listed as drop-in venues, not events. Keep one entry per venue with its regular opening days and hours, and check each run for changes and upcoming closures.

Venues to include: Museum of the Home, Young V&A, Hackney Museum, Sutton House, Hackney City Farm, and similar free or low-cost places in or near Hackney that you find.

## Checked by hand

`checked-by-hand.md` holds details a person has confirmed that aren't published online, each with a recheck date.

- If a fact's recheck date hasn't passed, treat it as confirmed and use it to fill the gap it covers. The session itself must still appear on a source you opened this run; if it has disappeared, remove the entry as usual.
- If a fact's recheck date has passed, don't use it. Leave that event out and list the fact under "Hand checks due".
- Also list under "Hand checks due" any fact whose recheck date is in the next 14 days.
- Never edit `checked-by-hand.md` yourself.

## Hand-added entries

Entries in `events.json` with `"manual": true` were added by a person because their source can't be read automatically. Don't edit or remove them. They carry a `checkBy` date, after which the website hides them. List any with a `checkBy` date in the next 14 days, or already past, under "Hand checks due".

## Rules

- Only include events you found on a page you actually opened this run. Never guess or fill in missing details. If an event's day, time or age range is unclear, leave it out and list it in the pull request under "Couldn't confirm".
- One entry per activity. A weekly session is one recurring entry, not one entry per week. Don't add duplicates of existing entries; update them instead.
- Every entry must have a working `link`. If you can't find one, leave the event out and list it under "Couldn't confirm".
- Only activities aimed at or suitable for children (0 to 17) and their families.
- `ageMin`/`ageMax`: use the ages the organiser states. "Under 5s" is 0 to 4. "All ages" or "families" is 0 to 17.
- `cost`: 0 for free. Otherwise the price of a child ticket in pounds as a number. If the price varies, use the lowest.

## Entry format

Recurring (weekly):
```json
{ "id": "rhyme-time-hackney-central-library", "title": "Rhyme time", "venue": "Hackney Central Library, E8",
  "weekdays": [4], "from": "2026-09-08", "until": "2026-12-12", "start": "10:30", "end": "11:00",
  "ageMin": 0, "ageMax": 4, "cost": 0, "booking": false,
  "link": "https://...", "source": "https://..." }
```
Drop-in venue:
```json
{ "id": "museum-of-the-home", "dropIn": true, "title": "Museum of the Home", "venue": "Hoxton, E2",
  "weekdays": [2, 3, 4, 5, 6, 7], "start": "10:00", "end": "17:00",
  "ageMin": 0, "ageMax": 17, "cost": 0, "booking": false,
  "note": "Gardens and family trails", "closed": ["2026-12-24", "2026-12-25"],
  "link": "https://...", "source": "https://..." }
```
- `note`: optional, one short phrase on what's there for children.
- `closed`: dates it's shut on a normal opening day (holidays, special closures). Remove past dates.

Optional fields for any recurring entry or drop-in venue:
- `closed`: list of dates it doesn't run (e.g. `["2026-09-23"]` when a library shuts for training). Use this for one-off cancellations rather than changing `from`.
- `hours`: different times on particular weekdays, overriding `start`/`end` for those days, e.g. `"hours": { "6": ["11:00", "16:00"] }` for different Saturday hours.

Monthly (a named week of the month): use `monthly` instead of `weekdays`, with `from`/`until` as usual.
```json
"monthly": { "weekday": 6, "week": 2 }
```
- `week`: 1 to 4 for the 1st to 4th, -1 for the last, or a list such as `[1, 3]` for the 1st and 3rd.

One-off: same fields as recurring, but `"date": "2026-10-03"` instead of `weekdays`/`from`/`until`.

- `weekdays`: 1 = Monday ... 7 = Sunday.
- `id`: short lowercase slug, unique and stable across runs.
- `link`: required. The most specific page where families can check the details or book: the event's own page or booking page if there is one, otherwise the venue's what's-on page. Never a homepage if a more specific page exists. `source`: the page where you found it (can be the same as `link`).
- Times are 24-hour, "HH:MM".

## Pull request description

Keep it short and easy to review on a phone:
- **Added**: one line per event (title, venue, when).
- **Changed**: what changed and why.
- **Removed**: anything removed other than expired events.
- **Couldn't confirm**: possible events you left out, with links.
- **Sources that failed**: any source you couldn't read.
- **Hand checks due**: facts in `checked-by-hand.md` and `"manual": true` entries that are expired or due in the next 14 days.
