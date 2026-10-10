# Schema

Two files, same shape: a block of metadata about the dataset, then the records.

Both files carry `dataset`, `description`, `source`, `method`, `license`, `license_url`,
`attribution`, `how_to_read`, `counts` and `corrections` at the top level. Those exist so a
file separated from this repo still explains itself and still names its licence.

---

## `data/clinics.json`

Top-level key: **`clinics`** — an array, sorted by `slug`.

### Identity

- **`name`** — the clinic's trading name.
- **`slug`** — our stable identifier. Safe to use as a join key; it does not change.
- **`profile_url`** — our page for this clinic.
- **`website`** — the clinic's own site. Absent if we hold none.
- **`city`**, **`country`** — where the clinic operates.
- **`founded_year`** — as stated by the clinic. Absent where unstated.
- **`entry_type`** — `clinic` or `facilitator`.
  A **facilitator** is a booking agency that places patients into a hospital it may not
  name. It stays in the dataset, labelled, because leaving it out would hide the category
  from anyone studying the market. **When counting clinics, filter on
  `entry_type == "clinic"`** — the `counts` block already does.

### `surgeon` — who operates

The most important object in the file, and the one most easily misused.

- **`state`** — one of four values, **not interchangeable**:
  - `named` — the clinic names a physician on its own website.
  - `checked_names_nobody` — we read the clinic's own website on `checked_on` and found no
    physician named. **An observation on a date, not a claim about who holds the punch.**
  - `not_checked` — we have not looked, or the site refused automated access. **No claim in
    either direction. Do not read this as a clinic withholding anything.**
  - `agency_does_not_operate` — this entry is a booking agency; by its own description it
    performs no procedures, so it has no surgeon of its own to name.
- **`name`** — the physician(s) named. Present only when `state` is `named`.
- **`checked_on`** — `YYYY-MM-DD`, the date we read the site. Present when we looked.
- **`note`** — the same fact in a sentence, so a row read alone cannot be misread.

Why four states and not a boolean: a boolean would have to answer "does this clinic name a
surgeon?" with `false` for a clinic nobody ever checked — collapsing "we don't know" into an
accusation, in a file licensed for anyone to republish.

### `price`

- **`state`** — `published` or `our_estimate`.
  - `published` — the clinic puts these rates on its own website.
  - `our_estimate` — the clinic does not publish rates. The figures are our approximation
    from comparable clinics. **Not a quote, and not the clinic's price.**
- **`per_graft_usd`**, **`min_usd`**, **`max_usd`** — USD. `null` where we hold no figure;
  never `0`, because a zero would be read as a price. A clinic that publishes package prices
  only has `per_graft_usd: null` with `min_usd`/`max_usd` set: we never divide a package
  price into a per-graft rate.
- **`note`** — the same distinction in a sentence.

### Other fields

- **`accreditations`** — array of accreditation markers, checked against the issuing body
  where one is reachable. `[]` where none are claimed or none survived checking.
- **`techniques`** — array (FUE, DHI, Sapphire FUE, FUT…).
- **`verified`** — whether the record has been checked against primary sources.
- **`verified_on`** — `YYYY-MM-DD`, UTC. Absent where never verified.
- **`summary`** — our own editorial description of the clinic. Covered by the licence like
  everything else here.

**Deliberately absent: ratings and review counts.** We hold no review volume we can source.

### `counts`

Derived from the rows, so it cannot disagree with them:

`entries`, `clinics`, `agencies`, `surgeon_named`, `surgeon_checked_names_nobody`,
`surgeon_not_checked`, `publishes_prices`, `price_is_our_estimate`.

---

## `data/rejected.json`

Top-level key: **`rejected`** — an array, sorted by `checked_on` then `name`.

Every record is a business we checked and did not list.

- **`name`** — trading name as we recorded it, or the domain where no trading name was.
- **`domain`** — absent where no domain was ever verified. Three records are names from our
  own early seed data that resolved to no real business; the finding there is about our data
  quality, not about anyone's clinic.
- **`city`** — absent where we could not place the business.
- **`checked_on`** — `YYYY-MM-DD`. **Always present.** A record read without its date is
  being read wrongly.
- **`reason_code`** — see below.
- **`reason`** — the observed fact in a sentence, with its date. **Never a verdict.**
- **`evidence_url`** — the page we read. Absent where the finding was an absence of any
  source rather than something read on a page.
- **`recorded_in`** — the database migration that first recorded this, so a published claim
  traces back to the commit that made it.

### `reason_code`

Also carried in the file itself under `reason_codes`, with the same wording.

- **`no_named_surgeon`** — the site named no physician when we read it, on `checked_on`.
  *A substantive finding against our published bar.*
- **`not_a_clinic`** — an agency or intermediary rather than an operating clinic.
  *A substantive finding: it says what the business is, not how good it is.*
- **`unverifiable`** — no primary source existed for the claims a listing would need.
  *Bookkeeping about our data, not a finding about the business.*
- **`duplicate_domain`** — a second domain for a practice already in the directory.
  *Bookkeeping only. It says nothing about the practice.*

### `counts`

`total`, and `by_reason_code` as an object keyed by code.

---

## Stability

- `slug` is stable and safe to join on.
- Fields are added, not renamed or repurposed. A field that stops being meaningful is
  removed and noted in the commit.
- Output is deterministic: rows are sorted and nothing carries a generation timestamp, so a
  re-run with no data change produces no diff.
