# WhatsApp Contact Extractor

Paste an exported WhatsApp chat and get every contact people shared, and every
number they typed, sorted into three groups:

- **Contacts** — shared contact cards with a real name *and* number, parsed from
  `BEGIN:VCARD…END:VCARD` blocks.
- **Cards shared, no number available** — a contact card was shared, but the chat
  export only kept the filename, not the vCard data. Name only, honestly labeled.
- **Other numbers mentioned** — bare digits typed straight into messages, not tied
  to any contact card.

**Live:** https://hedaprateek.github.io/wa-number-extractor/

One HTML file. No build step, no dependencies, no server. Everything runs in the
browser — nothing you paste is ever sent anywhere. Open `index.html` directly and
it works offline too.

## Where to get vCard data from

WhatsApp only writes the actual name-and-number data into a separate `.vcf` file
per shared contact, and only when you export a chat **with** media. A plain export
(or "without media") leaves just a reference line like
`<attached: 00000027-Alex Vendor.vcf>` in the `.txt` — the number itself was never
written to that file. To get the full contact:

1. Export the chat **with** media, then open the `.vcf` file(s) in a text editor
   and paste their contents in, or
2. Paste the `.txt` export as-is — you'll still get every name-only reference and
   every bare number typed in messages, just not numbers for shared cards.

## How the parsing works

- **vCard blocks:** unfolds wrapped lines, reads `FN`/`N` for the name, and prefers
  WhatsApp's own `waid=` parameter on the `TEL` line for the number — it's the
  cleanest source, since it's literally the number that contact is reachable at on
  WhatsApp. Falls back to the raw `TEL` value otherwise. Type labels (Mobile/Work)
  come from the paired `itemN.X-ABLabel` line, which is how WhatsApp/Apple-style
  vCards actually carry them (not a `TYPE=` parameter).
- **Card references:** matches the Android (`<attached: NAME.vcf>`) and iOS
  (`NAME.vcf (file attached)`) placeholder formats, stripping the leading numeric
  prefix WhatsApp adds. Also tallies `Contact card omitted` lines, which carry no
  recoverable name at all.
- **Bare numbers:** strips WhatsApp's bidi control marks, matches 7–15 digit runs
  allowing spaces/dashes/dots/parens (not colons or slashes, which keeps clock
  times and dates out), and dedupes by digits regardless of formatting.
- Numbers starting with `+` get a country label from a lookup of ~65 common
  calling codes.

It can't perfectly tell a bare phone number apart from any other digit sequence
with no separators (an order number, say) — that's a disclosed limitation, not a
bug to chase.
