# WhatsApp Number Extractor

Paste an exported WhatsApp chat and get every unique phone number it mentions —
sender lines, "joined the group" notices, numbers typed into messages — deduplicated
across formatting differences, with a count of how many times each one appears.

**Live:** https://hedaprateek.github.io/wa-number-extractor/

One HTML file. No build step, no dependencies, no server. Everything runs in the
browser — nothing you paste is ever sent anywhere. Open `index.html` directly and
it works offline too.

## How it detects numbers

- Strips the bidi control marks (`‎`, `‏`, etc.) WhatsApp wraps numbers in.
- Matches digit runs of 7–15 digits, allowing spaces/dashes/dots/parens as separators
  (so `+91 98765 43210`, `987-654-3210`, and `+1 (415) 555-0192` all match) — but not
  colons or slashes, which keeps timestamps and dates out of the results.
- Dedupes by digits only, so the same number in different formats counts once, with
  an accurate occurrence count.
- Labels the country for any number starting with `+`, from a lookup of ~65 common
  calling codes.

It can't perfectly tell a phone number apart from any other digit sequence with no
separators (an order number, say) — that's a disclosed limitation, not a bug to chase.
