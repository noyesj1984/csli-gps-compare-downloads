# csli-gps-compare-downloads
Use Candor .can files and test phones for validation
CSLI vs GPS Compare
Run:   python csli_gui.py        (Python 3.8+ with tkinter; standard library only)
Optional: pip install python-dateutil openpyxl tzdata   (more date formats, .xlsx GPS files, named time zones on Windows)
Files: csli_gui.py (window) | csli_run.py (pipeline) | csli_core.py (parsing, matching, Vincenty, reports) | csli_kmz.py (KMZ)
Output folder gets: cumulative_report.txt, reports/<cell>.txt, kmz/<cell>.kmz


TIME-MATCH RANGE (per data type)
After you scan the .can file, each data type gets a "Range from (s)" and "to (s)" pair (default 0 to 0 = exact second only).
Exact-second matches are ALWAYS kept. A range adds neighbouring seconds on top of that:
  -1 to 1  -> a transaction matches a GPS fix in the same second, or 1 second before or after it.
If several GPS fixes fall in the window, the exact second wins; otherwise the nearest one (ties go to the earlier fix).
"GPS offset s" in the reports = matched GPS second minus transaction second. KMZ time stamps use the matched GPS second.
Whole seconds only; the range may be lopsided (e.g. 0 to 2). Each transaction is still matched individually.


CUMULATIVE MASTER DATA FILE
Tick "Add this run to the cumulative master data file", choose a .csv (existing = add to it, new = created) and optionally a test label.

  carrier + phone line + data type + transaction date/time (ms) + cell.
After each run <master>_summary.txt is rewritten: outside-polygon / outside-band rates by carrier and overall, the
Voice/Data/SMS distance stats and the Timing Advance band tables across ALL tests, and a list of the tests added.
"Rebuild summary" regenerates it from the CSV without a run. The previous master is kept as <master>.csv.bak.
Google Drive: put the master .csv in a Google Drive for desktop folder; it syncs like any file. Avoid running from two computers at once.


POINT-ONLY RECORDS AND BREAKDOWNS
Some carriers (e.g. AT&T / T-Mobile Data, Voice and Location returns) give only a point per record, no polygon. These now appear
as separate types in the scan - "Voice (point only)", "Data (point only)", "SMS (point only)", "Location (point only)" - and can be
ticked. They are compared by Vincenty distance from the device to the record's point (min / max / mean / median), plus a within/beyond
check against the record's accuracy radius when it states one. Records with no position at all ("Unlocated") still cannot be compared.
The cumulative report and the master summary start with a breakdown by carrier and by phone number (line), each with polygon,
timing advance and point-only tables.
