# Turkish Hair Transplant Clinic Data

An open dataset of hair transplant clinics in Turkey, verified against primary sources —
**including every clinic we checked and did not list, with the reason and the date.**

Free to use, commercially or otherwise, under [CC BY 4.0](LICENSE). Credit
[Compare Hair Transplant](https://comparehairtransplant.com) with a link.

- **`data/clinics.json`** — every clinic in the directory, with two facts the industry does
  not publish about itself: whether the clinic names the surgeon who operates, and whether
  it puts a price in writing.
- **`data/rejected.json`** — every clinic we checked and did not list, with what we observed,
  the date we observed it, and the page we read.
- **[`SCHEMA.md`](SCHEMA.md)** — every field, what it means, and how it was checked.
- **Live figures:** <https://comparehairtransplant.com/data>

---

## Why the rejections are the interesting half

Every hair transplant directory publishes who it included. None publish who they left out.
That makes the inclusions impossible to audit — you cannot tell a curated list from a list
of whoever paid, because the only difference between the two is the part nobody shows you.

So this dataset ships both halves. If our standards are bad, this file is what you need to
say so.

## Snapshot

Current counts live inside each file under `counts`, so they can never disagree with the
rows they describe. At the time of the last export:

- **44** businesses checked
- **36** clinics listed, plus **2** booking agencies labelled as agencies
- **32** of 36 clinics name a physician on their own website
- **7** of 36 clinics publish a price in writing; every other figure is labelled as our estimate
- **8** businesses checked and not listed

## Read this before you use it

**`surgeon.state` has four values and they are not interchangeable.**

- `named` — the clinic names a physician on its own website.
- `checked_names_nobody` — we read the clinic's own website on the date in `checked_on` and
  it named no physician. That is an observation on a date, not a claim about who operates.
- `not_checked` — **we have not looked, or the site refused automated access. We make no
  claim in either direction.**
- `agency_does_not_operate` — a booking agency, which by its own description does not
  perform procedures.

**`not_checked` must never be republished or aggregated as though it meant a clinic hides
something.** It means we do not know. Collapsing "we don't know" into "they won't say" turns
a gap in our research into an accusation against a business, and that is the one way this
dataset can do real harm. Anything built on it should keep the four states apart.

The same discipline applies to `rejected.json`: every `reason` is a **dated observation of a
public page**, never a verdict about a business. Two of the four reason codes carry no
criticism at all — `duplicate_domain` and `unverifiable` are bookkeeping about our own data.

**These records have a shelf life.** A clinic that named no physician in July may name one
next year. `checked_on` is inside every record for exactly that reason; a record read
without its date is being read wrongly.

Nothing here is an assessment of clinical quality or safety, and nothing here is medical
advice.

## How the data was collected

- **The clinic's own website is the source** for who operates and what it costs. Not a
  directory, not a review site, not a listing that quotes them. If the clinic did not say
  it, we did not record it.
- **Accreditation claims are checked against the issuing body** where one is reachable. A
  badge on a clinic's own page is a claim, not a certificate.
- **A failed page load is not evidence.** Several clinics block automated fetching; a human
  opened each one in a browser instead. We never record "names nobody" on the strength of a
  403.
- **Ratings and review counts are deliberately absent.** We hold no review volume we can
  source, and an unsourced star rating inside a dataset about provenance would be a joke at
  our own expense.
- Full method, including what it deliberately leaves out:
  <https://comparehairtransplant.com/how-we-rank>

## Corrections and appeals

**Named a surgeon since we checked? Started publishing prices? Tell us and we will
re-check.**

That is not a courtesy line. A published negative claim obliges us to keep it current, and a
clinic that has changed is the correction we most want to make. Same goes for anything you
think we got wrong.

- Open an issue on this repo, or
- Use <https://comparehairtransplant.com/contact>

**We also re-check every domain in `rejected.json` quarterly and publish the result whichever
way it goes.** A domain that now names a surgeon gets its date bumped and moves into the
directory or out of the file.

## Updates

The files are regenerated from the live database whenever clinic records change. Output is
deterministic — sorted rows, no generation timestamp — so a re-run with no data change
produces no diff, and the one line that did change is the one you see in `git log`.
**Per-record dates are the authority on freshness; this repo's commit history is the
changelog.**

## How to cite

> Compare Hair Transplant, *Turkish Hair Transplant Clinic Data*, CC BY 4.0.
> https://comparehairtransplant.com/data

For a page or an article, a link to <https://comparehairtransplant.com> satisfies the
attribution requirement.

## Licence

[Creative Commons Attribution 4.0 International](LICENSE) (CC BY 4.0). Use it, sell it,
build on it, remix it. Credit **Compare Hair Transplant** with a link to
<https://comparehairtransplant.com>, and say if you changed anything. That is the entire
deal.
