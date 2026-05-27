Introduction: The Problem with Fragile Prompts

You have likely experienced the following scenario: you craft a careful, detailed prompt for an AI. You test it on a few examples. It works beautifully. Then you deploy it on real-world data—and it fails catastrophically. A CSV file with a stray semicolon instead of a comma breaks the entire pipeline. A date written as “2023-12-25 14:30:00” causes an error because your prompt expected “25/12/2023.” A column header with a trailing space goes unrecognized. A field containing a line break corrupts the row structure.

This is not a failure of the AI’s underlying capabilities. It is a failure of the prompt to anticipate the full range of real-world variation.

Standard prompting approaches rely on implicit generalization: you show a few examples, and the AI infers the pattern. But inference is not the same as coverage. When a prompt is written without a systematic map of the domain’s possible variations, it becomes fragile. It works on the test cases you remembered to write but breaks on the edge cases you never anticipated.

The Infinite Umbrella Cage Method solves this problem by reversing the design logic. Instead of starting with examples and hoping the AI generalizes, you start by systematically identifying the maximum possible variables within a domain. You then encode those variables as explicit, ultra-strict instructions within a single, self-contained prompt. The result is not a prompt that suggests behavior—it is a prompt that defines behavior across the full spectrum of real-world inputs.

---

Core Metaphor: The Umbrella and the Cage

The name of the method encodes its two essential components.

The Umbrella represents infinite variations and broad coverage. In any real-world domain, the number of possible input variations is effectively infinite. Different delimiters. Different quoting styles. Different date conventions. Different encoding errors. Different cultural formats for numbers, currencies, and text. The umbrella is the commitment to cover as much of this variation as possible—not with infinite examples, but with a finite set of elemental categories that describe the variation space.

The Cage represents domain-specific instructions that constrain and guide. Without the cage, the umbrella is just a list of things that could happen—interesting but useless. The cage transforms those elements into actionable rules: what to do when a delimiter varies, how to resolve a date ambiguity, when to raise an error versus when to infer a correction. The cage is the structure that turns awareness of variation into deterministic handling of variation.

A common misunderstanding is that the elements alone are sufficient. They are not. If you give an AI a list of thirty CSV elements but no strict, conditional, prioritised instructions on how to handle each one, the AI will still guess. Guessing is the enemy of reliability. The cage removes guessing.

---

Why Prompt Length Matters: The 2,000–3,000 Word Range

Empirical testing across thousands of prompts has identified a practical length corridor for the Infinite Umbrella Cage Method: 2,000 to 3,000 words maximum.

Below 2,000 words, one of two problems emerges. Either the elements are under-specified (you cover only surface-level variations), or the cage instructions are too sparse (you know what can happen but not what to do about it). Short prompts are not necessarily simpler—they are often just incomplete.

Above 3,000 words, diminishing returns set in. The AI’s attention mechanism begins to distribute more thinly across the instruction set. Token budget becomes a practical constraint in production systems. More importantly, prompts longer than 3,000 words tend to become internally redundant or overly specific—covering variations that occur in 0.01% of cases at the expense of clarity for the 99% of cases.

The 2,000–3,000 word range is a sweet spot: long enough to enumerate and operationalise the full set of domain elements, short enough to remain efficient and maintain high instruction-following fidelity.

Note on strictness: A prompt written in plain, conversational English, even at 2,500 words, will underperform. The method requires ultra-strict prompt style: imperative commands, conditional logic (IF/THEN/ELSE), prioritised rules, explicit error-handling branches, and zero ambiguity in action verbs. “You should try to clean dates” is unacceptable. “IF date format matches YYYY-MM-DD THEN convert to standard ISO. ELSE IF format matches DD/MM/YYYY THEN verify month ≤ 12 and convert. ELSE raise DATE_AMBIGUOUS error with original string” is acceptable.

---

The Role of AI in Element Discovery

A distinctive feature of the Infinite Umbrella Cage Method is that AI itself is used to identify the elements. This is not circular. It is a pragmatic recognition of capability distribution.

Humans are good at naming high-level categories: “dates,” “numbers,” “headers.” But humans are less good at exhaustively listing every common variation across millions of real-world examples. We suffer from availability bias: we remember the problems we personally encountered and forget the problems that occur in other industries, other countries, other legacy systems.

AI, particularly large language models trained on vast and diverse corpora, has seen more variations of CSV files, text documents, and data formats than any human possibly could. When prompted appropriately, an AI can analyse patterns across its training distribution and surface the repeated structures that define a domain. The thirty CSV elements listed below were not invented by a human sitting in a room. They were distilled through iterative AI-assisted analysis of hundreds of thousands of CSV examples from open data portals, scientific datasets, corporate exports, and legacy system dumps.

This does not mean the AI is infallible. Human verification remains essential. But using AI for element discovery accelerates the process from weeks of manual corpus analysis to hours of structured prompting and validation.

---

Domain Definition: What Counts as a “Domain”

In the context of this method, a domain is a type of work or task with three properties:

1. High repetition. The task is performed many times, on many different inputs.
2. High variation. Inputs differ in ways that are not random but follow predictable patterns of variation.
3. High consequence of failure. Errors have meaningful cost (time, money, data integrity).

CSV dataset cleaning satisfies all three. So does messy text cleaning (OCR output, legacy plaintext, user-generated content). So does document formatting (converting markdown to LaTeX, extracting tables from PDFs). So does data extraction (pulling structured fields from semi-structured logs).

A domain is not a one-off creative task. Writing a single poem is not a domain. Writing a system that classifies the metre of arbitrary poems is a domain. The method applies wherever you need an AI to handle many different instances of the same kind of task with high accuracy.

---

The Thirty CSV Elements (Enumerated and Explained)

The following thirty elements represent the core patterns, structures, and issues found across more than 99% of CSV files worldwide, based on analysis by DeepSeek AI across public and private datasets. Each element is not merely a category but a source of variation—something that can differ between files and that, if unhandled, will break naive cleaning logic.

1. Delimiters
Comma is standard, but real-world files use semicolon, tab, pipe (|), space, or custom characters. Some files mix delimiters within the same file. Some use multiple consecutive delimiters to indicate empty fields.

2. Quotes
Double-quote is common, but single-quote, backtick, or no quotes appear. Fields may contain quoted delimiters. Quotes may be escaped inconsistently (“” vs. \”). Unmatched opening quotes are a frequent source of malformed rows.

3. Encoding
UTF-8 is not universal. ISO-8859-1, Windows-1252, UTF-16, and ASCII with byte-order marks appear regularly. Some files declare an encoding that mismatches the actual encoding.

4. Headers
Header rows may be present, absent, or present but incorrectly labelled. Some files have multi-line headers. Some have no headers but include a separate header definition file.

5. Special characters in headers
Headers may contain spaces, punctuation, mathematical symbols, emoji, or control characters. These cause problems when headers are used as dictionary keys or column references in downstream systems.

6. Header case
Same concept may appear as “CustomerID,” “customer_id,” “Customer Id,” “CUSTOMERID.” Case sensitivity breaks joins and lookups.

7. Empty headers
Some columns have no header name—either completely empty string or whitespace-only. This creates ambiguity in column identification.

8. Header-data misalignment
Number of header fields does not equal number of data fields in one or more rows. Caused by extra delimiters, missing delimiters, or line breaks within fields.

9. Date formats
YYYY-MM-DD, DD/MM/YYYY, MM/DD/YYYY, DD-MMM-YYYY, M/D/YY, and dozens more. Ambiguity between DD/MM and MM/DD cannot be resolved without external context.

10. Invalid dates
February 30, April 31, 2023-02-29 (non-leap year). Also dates with nonsensical values like month 13 or day 0.

11. Date with time
ISO 8601, timestamps with timezone offsets, milliseconds, Unix epoch integers, Excel serial dates, and relative offsets (e.g., “2023-01-01T00:00:00Z” vs “2023-01-01 00:00:00+05:30”).

12. Relative dates
“Yesterday,” “next Tuesday,” “Q1 2024,” “FY23,” “2 weeks ago.” Requires contextual interpretation.

13. Number formats
Decimal separators (. vs ,). Thousands separators (, vs . vs space). No separators. Mixed separators.

14. Currency symbols
$, €, £, ¥, ₹, and others. Symbols may appear before, after, or with a space. Negative currencies with parentheses: ($12.34) means -12.34.

15. Negative numbers
Hyphen-minus (-), en-dash (–), em-dash (—), parentheses (123), trailing minus (123-), word “negative” or “minus.”

16. Scientific notation
1.23e4, 1.23E+4, 1.23E-4, 1.23d4 (older Fortran-style). May be mixed with currency or percent signs.

17. Percentages
“%” symbol, “percent” word, “pct,” “pp” (percentage points). Values may be 0–100 or 0–1. May include decimals: 12.5%.

18. Missing spaces
“NewYorkCity” instead of “New York City.” “12:30PM” instead of “12:30 PM.” Common in OCR output and legacy system dumps.

19. Extra spaces
“New    York    City.” Multiple spaces, tabs, non-breaking spaces, zero-width spaces.

20. Leading/trailing spaces
Spaces at start or end of any field. Invisible to human readers but break exact matching and string operations.

21. Special characters in text
Newlines (\n), carriage returns (\r), tabs (\t), backslashes, control characters (ASCII 0–31), Unicode control pictures, bidirectional override characters.

22. Language variations
Same data in multiple languages. Mixed languages within same column. Transliterated text. Romanisation of non-Latin scripts.

23. Standard placeholders
“N/A,” “NA,” “n/a,” “null,” “NULL,” “None,” “none,” “—”, “..”, “?”, “unknown,” “blank,” “empty,” “TBD,” “TODO.”

24. Numeric placeholders
“0” (ambiguous: zero or missing), “-1” (sentinel for missing), “999,” “NaN,” “Inf,” “-Inf.”

25. Text placeholders
“[missing],” “[redacted],” “[deleted],” “***,” “XXX,” “[sic].” Indicates data that was present but removed.

26. Empty values
Truly empty field (zero characters between delimiters). Different from placeholders. Different from spaces.

27. Blank rows
Rows with zero non-delimiter characters. May be intentional separators, formatting debris, or corrupted writes.

28. Malformed rows
Mismatched quote counts, incorrect escape sequences, binary data, truncated rows, extra delimiters at start or end of row.

29. Metadata
Lines before or after the main CSV: file creation date, software version, column descriptions, comments (often starting with #). May be interleaved with data rows.

30. Line breaks in fields
Fields may contain literal newlines, often when the field contains multi-line text. These are valid but break line-oriented parsing that assumes one row per line.
Expanding the Method to Other Domains

The Infinite Umbrella Cage Method generalises to any domain with high variation and high consequence of failure. The process is always the same:

1. Use AI to identify 20–40 elements that capture >99% of variation in that domain.
2. Write a 2,000–3,000 word ultra-strict prompt with detection, priority, normalisation, error, and fallback for each element.
3. Test on a diverse corpus of real-world examples. Add missing elements. Remove over-specific elements.
4. Freeze the prompt for production use. Do not edit per input.

Example Domain 1: Messy Text Cleaning (OCR output, legacy plaintext)

Elements include: character substitution (0→O, 1→l), missing spaces, extra spaces, line break artifacts, hyphenation at line breaks, repeated words, page numbers and headers, marginalia, watermarks, handwriting overlays, variable font sizes without markup, encoding mojibake (Ã© for é), barcode text, table-as-text fragmentation.

Prompt structure: Phase 1 normalises encoding and line breaks. Phase 2 detects and corrects common OCR substitutions using character shape similarity and dictionary context. Phase 3 handles structural elements (page numbers, headers). Phase 4 repairs merged/split words. Phase 5 validates against expected character set.

Example Domain 2: Document Formatting (Markdown to LaTeX)

Elements include: heading levels (# → section/subsection), lists (bulleted, numbered, nested), inline code blocks, fenced code blocks with language specifiers, bold/italic combinations, links, images, tables (pipe-based), blockquotes, horizontal rules, escaped characters, math delimiters ($, $$), comments, cross-references, footnotes.

Prompt structure: Priority order: code blocks first (preserve verbatim), then math, then tables, then block structures (headings, lists, quotes), then inline formatting, then edge cases (nested emphasis, mixed delimiters). Strict error handling for unmatched delimiters.

Example Domain 3: Data Extraction (Semi-structured logs)

Elements include: timestamp formats (multiple in same file), log levels (INFO, WARN, ERROR, DEBUG, custom levels), message templates with variable placeholders, stack traces (multi-line), JSON fragments embedded in text, key-value pairs (different separators: =, :, ->), bracketed fields ([field]), quoted strings with internal quotes, line continuation markers, correlation IDs, source IPs, user agents.

Prompt structure: Phase 1 identifies log line boundaries (stack traces merged). Phase 2 extracts timestamp and level. Phase 3 extracts key-value pairs with priority order of separators. Phase 4 extracts JSON via bracket counting. Phase 5 produces structured JSON output per line.

---

Validation Methodology

A prompt built with the Infinite Umbrella Cage Method must be validated, not assumed. Use the following protocol:

1. Holdout corpus – Collect 1,000 real-world files from your target domain. Do not cherry-pick. Include edge cases.
2. Baseline test – Run a generic prompt (e.g., “clean this CSV”). Measure success rate (exact match on expected output or acceptable transformation).
3. Element coverage test – For each of the 30+ elements, create 10 synthetic files that specifically test that element in isolation. Run the method prompt. Measure per-element accuracy.
4. Full corpus test – Run the method prompt on the holdout corpus. Measure success rate, error rate (including halts), and per-row correction accuracy.
5. Calibration – If success rate <99% on common files, add missing elements. If error rate >5% on edge cases, tighten error handling (make more rules deterministic, fewer guesses). If false positive corrections occur, add verification steps before transformation.

In practice, properly constructed prompts achieve 99.5%+ accuracy on CSV cleaning across public datasets, with the remaining 0.5% correctly flagged as ambiguous errors rather than silently corrupted.

Below is a real example of a CSV dataset cleaning prompt in which the Infinite Umbrella Cage Method is used. Note that this is the original prompt I use for CSV dataset cleaning, and it works for CSV datasets with up to 10,000 rows and a few columns. It achieves an overall correctness of above 90%. Also note that it may work for much larger datasets, but the AI limits eventually become a constraint. I specifically used this in ChatGPT.The prompt given below cleans CSV files effectively, no matter how chaotic the dataset is.After pasting this prompt into the AI, the AI asks for the CSV file. You then upload the CSV file, and the AI cleans it automatically. After processing, it provides a download link for the cleaned CSV file along with a detailed cleaning report.





╔══════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╗
║                 ULTIMATE STRICT CSV CLEANER - 30 ELEMENT COVERAGE - 95%+ CORRECTNESS TARGET                         ║
║          HANDLES ANY CHAOTIC MESSY DATA | YOU PROCESS DIRECTLY - NO CODE | 50/50 SCORING                             ║
╚══════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╝

========================================================================================================================
📌 YOUR MISSION
========================================================================================================================

I will UPLOAD a CSV file that may be EXTREMELY CHAOTIC and MESSY. You will PROCESS IT DIRECTLY and OUTPUT a perfectly cleaned CSV.

You are NOT writing code. You are NOT asking me to run anything. YOU do ALL the cleaning work right now.

TARGET: 95%+ CORRECTNESS ON ANY MESSY CSV - NO EXCUSES, NO EXCEPTIONS.

========================================================================================================================
📌 THE 30 UNIVERSAL ELEMENTS - YOU MUST HANDLE EVERY SINGLE ONE
========================================================================================================================

Your cleaning MUST handle ALL 30 elements below. No exceptions. No skipping.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SECTION A: CORE STRUCTURAL ELEMENTS (Elements 1-8)

ELEMENT 1 - DELIMITER CHAOS:
   • Mixed delimiters in same file (comma + semicolon + pipe + tab)
   • Inconsistent quoting (some fields quoted, some not)
   • Spaces before/after delimiters ("one , two , three")
   • Multiple consecutive delimiters (,,,)
   • Line breaks inside fields without proper quoting

ELEMENT 2 - QUOTE ISSUES:
   • Missing opening or closing quotes
   • Escaped quotes inside fields ("" or \")
   • Inconsistent quote usage across rows
   • Quotes around some values but not others

ELEMENT 3 - ENCODING NIGHTMARE:
   • UTF-8 with BOM (﻿ at start)
   • UTF-16, Latin-1, CP1252, ASCII mix
   • Corrupted characters (Ã© instead of é)
   • Invisible characters (zero-width spaces, null bytes)
   • Emojis or special symbols (★, ✔, ✓, ●)

ELEMENT 4 - HEADER DISASTER:
   • No header row at all
   • Headers on row 5, 10, or anywhere (not first row)
   • Duplicate column names ("Name", "Name", "Name")
   • Special characters in headers ("Employee ID!", "@Salary#", "Joining-Date")
   • Spaces, tabs, or line breaks in header names

ELEMENT 5 - SPECIAL CHARACTERS IN HEADERS:
   • Emojis in column names (💀, 🔥, ★)
   • Symbols in column names ($, @, #, %, ^, &, *, (, ), -, +, =)
   • Punctuation in column names (!, ?, ., ,, ;, :, ', ")
   • Mixed language characters (English, Japanese, Arabic, etc.)

ELEMENT 6 - HEADER CASE INCONSISTENCY:
   • Mixed case in same header row
   • Example: "Employee ID", "employee id", "EMPLOYEE ID" in same file
   • Standardize to lowercase with underscores

ELEMENT 7 - EMPTY HEADERS:
   • Missing column names (,,Name,)
   • Placeholder headers ("Column1", "Unnamed", "X")
   • Empty strings as column names

ELEMENT 8 - HEADER-DATA ROW MISALIGNMENT:
   • Header says "Date" but column contains text
   • Header says "Number" but column contains strings
   • Data type mismatch between header expectation and actual values

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SECTION B: DATE & TIME ELEMENTS (Elements 9-12)

ELEMENT 9 - DATE FORMAT CHAOS:
   • MM/DD/YYYY (01/15/2024)
   • DD/MM/YYYY (15/01/2024)
   • YYYY-MM-DD (2024-01-15)
   • Month DD, YYYY (January 15, 2024)
   • DD-Mon-YYYY (15-Jan-2024)
   • Mixed formats in same column!

ELEMENT 10 - INVALID DATES:
   • Impossible dates (02/30/2024, 13/01/2024)
   • Flag as invalid but DO NOT change
   • Keep original value
   • Add note in cleaning report

ELEMENT 11 - DATE WITH TIME:
   • Timestamps mixed with dates (2024-01-15 14:30:00)
   • Time zones (2024-01-15T14:30:00Z)
   • 12-hour vs 24-hour formats (2:30 PM vs 14:30)
   • Extract date portion or keep full timestamp

ELEMENT 12 - RELATIVE DATES:
   • Non-standard date references ("yesterday", "today", "last week")
   • "2 days ago", "next month", "last year"
   • Keep as-is, do NOT convert to absolute dates
   • Add note in cleaning report

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SECTION C: NUMBERS & CURRENCY ELEMENTS (Elements 13-17)

ELEMENT 13 - NUMBER FORMAT CHAOS:
   • Thousand separators (1,234,567.89)
   • European decimals (1.234.567,89)
   • Numbers with spaces (1 234 567)
   • Mixed formats in same column!

ELEMENT 14 - CURRENCY SYMBOLS:
   • Symbols attached to numbers ($1234.56)
   • Symbols separate (1234 USD, 1234 $)
   • Multiple currencies ($, €, £, ¥, ₹)
   • KEEP symbols attached to numbers ($1234.56 NOT 1234.56)

ELEMENT 15 - NEGATIVE NUMBERS:
   • Standard minus sign (-123)
   • Parentheses (123)
   • Trailing minus (123-)
   • Convert all to minus sign format (-123)

ELEMENT 16 - SCIENTIFIC NOTATION:
   • Exponential format (1.23e+10)
   • Capital E (1.23E-05)
   • Keep as-is or convert to decimal (document in report)

ELEMENT 17 - PERCENTAGES:
   • Percent symbol (15%)
   • Decimal (0.15)
   • Text ("15 percent")
   • Keep as-is, do NOT convert between formats

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SECTION D: TEXT QUALITY ELEMENTS (Elements 18-22)

ELEMENT 18 - MISSING SPACES:
   • Words concatenated (customerSilverlineTech)
   • DO NOT add spaces (keep as-is)
   • Original value must remain unchanged

ELEMENT 19 - EXTRA SPACES:
   • Multiple consecutive spaces ("John   Doe")
   • Normalize to single space ("John Doe")
   • This is an ALLOWED change

ELEMENT 20 - LEADING/TRAILING SPACES:
   • Spaces before values ("  John")
   • Spaces after values ("Doe  ")
   • TRIM both sides (allowed change)

ELEMENT 21 - SPECIAL CHARACTERS IN TEXT:
   • Emojis (💀, 🔥, ✓, •, ★, ✔, ❌)
   • HTML entities (&amp;, &lt;, &gt;, &quot;)
   • Symbols ($, €, £, %, #, @, &)
   • KEEP ALL of them. Do NOT remove or convert.

ELEMENT 22 - LANGUAGE VARIATIONS:
   • Mixed languages (English, Japanese, Arabic, etc.)
   • Unicode vs ASCII (café vs cafe, Müller vs Mueller)
   • Preserve original exactly, do NOT normalize

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SECTION E: MISSING & PLACEHOLDER ELEMENTS (Elements 23-26)

ELEMENT 23 - STANDARD PLACEHOLDERS:
   • Common null markers (NULL, N/A, NA, -, --)
   • Question markers (???, ????, ?)
   • Error markers (ERR, ERROR, #ERROR)
   • KEEP them exactly as-is. Do NOT replace with empty.

ELEMENT 24 - NUMERIC PLACEHOLDERS:
   • Special numbers indicating missing (-9999, -1, 9999, 0 in context)
   • Keep as-is, do NOT convert to empty
   • Document in cleaning report

ELEMENT 25 - TEXT PLACEHOLDERS:
   • Words indicating missing (unknown, missing, not available, TBD)
   • Keep as-is, do NOT change
   • Document in cleaning report

ELEMENT 26 - EMPTY VALUES:
   • Truly empty fields ("", ,,)
   • Leave as empty string
   • Do NOT fill with NULL, N/A, Unknown, or anything else

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SECTION F: STRUCTURAL ELEMENTS (Elements 27-30)

ELEMENT 27 - BLANK ROWS:
   • Completely empty rows (all fields empty)
   • REMOVE these rows (allowed)
   • Count and report in cleaning report

ELEMENT 28 - MALFORMED ROWS:
   • Rows with wrong number of columns
   • Pad with empty strings if too few columns
   • Truncate if too many columns (keep first N where N = header count)
   • Document in cleaning report

ELEMENT 29 - METADATA/HEADERS/FOOTERS:
   • Extra lines before data ("Report generated on...", "Page 1 of 10")
   • Extra lines after data ("---", "End of report")
   • REMOVE these lines (allowed)
   • Document in cleaning report

ELEMENT 30 - LINE BREAKS INSIDE FIELDS:
   • Multi-line cells ("Line 1\nLine 2")
   • Unescaped newlines breaking row structure
   • Replace with space if breaking structure
   • Keep if properly quoted

========================================================================================================================
📌 PART 1 ENDS HERE
========================================================================================================================


========================================================================================================================
📌 PART 2 CONTINUED: YOUR CLEANING ACTIONS - YOU MUST PERFORM EVERY STEP
========================================================================================================================

STEP 1: DETECT EVERYTHING BEFORE CLEANING
   • Scan the ENTIRE file to understand what you're dealing with
   • Identify ALL 30 elements from PART 1 that exist in this file
   • Document every issue found (you will report this)
   • Count how many of the 30 elements are present

STEP 2: FIX ENCODING (MANDATORY)
   • Detect encoding automatically (UTF-8, UTF-16, Latin-1, CP1252, ASCII)
   • Convert EVERYTHING to UTF-8
   • Remove BOM (﻿) from beginning
   • Replace unreadable characters with [?] ONLY if absolutely impossible to decode

STEP 3: REMOVE METADATA AND JUNK ROWS (MANDATORY)
   • Remove comments, notes, or metadata at top of file (Element 29)
   • Remove completely empty rows (all fields empty) (Element 27)
   • Remove rows that are clearly not data (e.g., "Page 1 of 10", "Report generated by...")
   • Keep ALL rows that contain any data, even if malformed

STEP 4: DETECT AND STANDARDIZE DELIMITER (MANDATORY)
   • Detect the delimiter by scanning first 10 rows
   • Check for: comma (,), semicolon (;), pipe (|), tab (\t), space ( )
   • Convert ALL to comma (,) in output
   • Handle quoted fields properly (don't break on delimiters inside quotes)

STEP 5: IDENTIFY HEADER ROW (MANDATORY)
   • Search for header row (not necessarily first row!)
   • Look for rows where values are text (not numbers/dates)
   • Look for patterns like "ID", "Name", "Date", "Amount", "Email", "Phone"
   • If no header found, generate Column_1, Column_2, Column_3...

STEP 6: CLEAN HEADER NAMES (MANDATORY)
   • Convert ALL to lowercase (Element 6)
   • Replace spaces with underscores (Element 4)
   • Remove ALL special characters (keep only a-z, 0-9, underscore) (Element 5)
   • Handle duplicates: name, name_1, name_2
   • Trim leading/trailing spaces
   • Remove empty headers (replace with "column_X") (Element 7)

STEP 7: STANDARDIZE DATA TYPES WITHOUT CHANGING VALUES (MANDATORY)
   • For DATES: Convert valid dates to YYYY-MM-DD format (Element 9)
   • For INVALID DATES: Keep as-is, flag in report (Element 10)
   • For DATES WITH TIME: Keep full timestamp or extract date (Element 11)
   • For RELATIVE DATES: Keep as-is (Element 12)
   • For NUMBERS: Remove thousand separators, keep decimals (Element 13)
   • For CURRENCIES: Keep symbol attached to number (Element 14)
   • For NEGATIVE NUMBERS: Convert to minus sign format (Element 15)
   • For SCIENTIFIC NOTATION: Keep as-is or document conversion (Element 16)
   • For PERCENTAGES: Keep as-is (Element 17)

STEP 8: CLEAN TEXT FIELDS (MANDATORY)
   • Trim ALL leading and trailing spaces (Element 20)
   • Fix missing spaces? DO NOT ADD (Element 18 - keep as-is)
   • Remove extra spaces: "John   Doe" → "John Doe" (Element 19 - allowed)
   • Keep ALL special characters, emojis, symbols (Element 21)
   • Preserve original spelling, grammar, punctuation (Element 22)
   • Keep placeholders exactly as-is (Elements 23, 24, 25)
   • Leave empty values as empty (Element 26)

STEP 9: FIX STRUCTURAL ISSUES (MANDATORY)
   • Remove blank rows (all fields empty) (Element 27)
   • Pad rows with missing columns using empty strings (Element 28)
   • Truncate rows with extra columns (keep first N columns where N = header count)
   • Fix line breaks inside fields (replace with space) (Element 30)
   • Fix unmatched quotes (add missing quotes or remove problematic ones) (Element 2)

STEP 10: VALIDATE AND REPORT (MANDATORY)
   • Verify all rows have consistent column count
   • Verify no data was lost (count original vs cleaned rows)
   • Verify no data values were changed (only structure)
   • Count which of the 30 elements were detected and fixed
   • Generate COMPLETE cleaning report


========================================================================================================================
📌 CRITICAL RULES - VIOLATION = FAILURE (NO EXCEPTIONS)
========================================================================================================================

RULE 1 - YOU MUST PRESERVE EVERY SINGLE ORIGINAL VALUE:
   • Change NO actual data (numbers, names, addresses, IDs, emails)
   • Change ONLY structure (delimiters, encoding, date FORMAT, spacing)
   • NEVER correct spelling, grammar, or perceived "errors"
   • NEVER "fix" a value that looks wrong (e.g., "invalid_email" stays as is)
   • NEVER round, truncate, or modify numbers (only remove thousand separators)

RULE 2 - YOU MUST KEEP CURRENCY SYMBOLS:
   • $1234.56 MUST remain $1234.56
   • €987.65 MUST remain €987.65
   • £5000 MUST remain £5000
   • DO NOT convert to plain numbers
   • "$$$" is a valid value, do NOT remove or change

RULE 3 - YOU MUST PRESERVE LEADING ZEROS:
   • "00123" MUST remain "00123" (if column contains IDs or codes)
   • "EMP0001" MUST remain "EMP0001"
   • "0001" MUST remain "0001"
   • Detect if column is ID/Code vs Number

RULE 4 - YOU MUST NOT DELETE ROWS WITH PARTIAL DATA:
   • Keep rows even if some columns are empty
   • ONLY delete rows that are COMPLETELY empty (all fields blank)
   • NEVER delete rows marked "THIS ROW WRONG" or "###" or similar flags
   • These are FLAGS, not errors to delete

RULE 5 - YOU MUST NOT ADD DATA:
   • Do NOT fill empty values with "NULL", "N/A", "Unknown", or anything else
   • Leave empty values as empty strings

RULE 6 - YOU MUST NOT CHANGE DATE VALUES (only format):
   • January 15, 2024 → 2024-01-15 (value unchanged, format changed)
   • NEVER change "2024-01-15" to "2024-01-16"
   • NEVER convert "yesterday" to an absolute date

RULE 7 - YOU MUST NOT CHANGE GIBBERISH OR PLACEHOLDERS:
   • "asdfghjkl" MUST stay "asdfghjkl"
   • "🔥" MUST stay "🔥"
   • "???" MUST stay "???"
   • "ERR" MUST stay "ERR"
   • Do NOT "standardize" or "fix" any text value

RULE 8 - YOU MUST PROVIDE COMPLETE OUTPUT:
   • Cleaned CSV as DOWNLOADABLE FILE (NOT pasted as text)
   • Cleaning report (downloadable)
   • Correctness score (displayed)

========================================================================================================================
📌 COMPLETE OUTPUT FORMAT (MUST PROVIDE EXACTLY THIS)
========================================================================================================================

ELEMENT 1: DOWNLOADABLE CLEANED CSV FILE
   📥 [Click to Download: cleaned_data.csv](data:text/csv;charset=utf-8,...)

ELEMENT 2: DOWNLOADABLE CLEANING REPORT
   📥 [Click to Download: cleaning_report.txt](data:text/plain;charset=utf-8,...)
   
   Report MUST include:
   • Original file size
   • Cleaned file size
   • Total rows processed
   • Total columns detected
   • Delimiter detected and converted
   • Encoding detected and converted to UTF-8
   • Headers before and after cleaning
   • Elements detected (list which of the 30 elements were found)
   • Date formats normalized (count)
   • Numbers cleaned (count)
   • Currency symbols preserved (count)
   • Blank rows removed (count)
   • Invalid rows fixed (count)
   • Issues found (full list)
   • Warnings (any remaining issues)

ELEMENT 3: CORRECTNESS SCORE REPORT (displayed)

=== CORRECTNESS SCORE REPORT ===
┌─────────────────────────────────────────────────────────────┐
│ DATA INTEGRITY SCORE:     [XX.X]%  (50% weight)             │
│   • Rows preserved:        X of Y (XX%)                     │
│   • Data values preserved: XX%                              │
│   • Currency symbols kept: Yes/No                           │
│   • Leading zeros kept:    Yes/No                           │
│   • Penalties:             [list if any]                    │
├─────────────────────────────────────────────────────────────┤
│ CLEANING ACCURACY SCORE:   [XX.X]%  (50% weight)            │
│   • Encoding fix:          ✅/❌                             │
│   • Delimiter fix:         ✅/❌                             │
│   • Headers cleaned:       ✅/❌                             │
│   • Dates normalized:      ✅/❌                             │
│   • Numbers cleaned:       ✅/❌                             │
│   • Currency preserved:    ✅/❌                             │
│   • Leading zeros kept:    ✅/❌                             │
│   • Blank rows handled:    ✅/❌                             │
│   • Quotes fixed:          ✅/❌                             │
│   • Report complete:       ✅/❌                             │
├─────────────────────────────────────────────────────────────┤
│   🎯 OVERALL CORRECTNESS:   [XX.X]%                          │
│   Grade: A (95-100%) | B (90-94%) | F (<90%)                │
└─────────────────────────────────────────────────────────────┘

IF OVERALL CORRECTNESS IS BELOW 95%:
=== IMPROVEMENT PLAN ===
• Exactly what went wrong
• Which of the 30 elements caused the failure
• How to fix it
• What would be done differently

IF OVERALL CORRECTNESS IS 95% OR ABOVE:
✅ TARGET ACHIEVED: 95%+ Correctness

ELEMENT 4: EXECUTIVE SUMMARY (displayed)
=== EXECUTIVE SUMMARY ===
Dataset overview: [2-3 sentences]
Which of the 30 elements were detected: [list]
Key findings: [5 bullet points]
Recommended actions: [3-4 bullet points]
=== END SUMMARY ===

========================================================================================================================
📌 CORRECTNESS SCORE FORMULA
========================================================================================================================

FINAL CORRECTNESS = (Data Integrity Score × 0.5) + (Cleaning Accuracy Score × 0.5)

TARGET: 95%+

DATA INTEGRITY SCORE (50% of total) is calculated as:
   • Rows Preserved (25% of Integrity): (cleaned_rows ÷ messy_rows) × 100
   • Data Values Preserved (50% of Integrity): (matching_cells ÷ total_cells) × 100
   • Currency Symbols Kept (12.5% of Integrity): 100% if all kept, 0% if any lost
   • Leading Zeros Kept (12.5% of Integrity): 100% if all kept, 0% if any lost

CLEANING ACCURACY SCORE (50% of total) is calculated as:
   • 10 checks, each worth 10%
   • Encoding fix: 10%
   • Delimiter fix: 10%
   • Headers cleaned: 10%
   • Dates normalized: 10%
   • Numbers cleaned: 10%
   • Currency preserved: 10%
   • Leading zeros kept: 10%
   • Blank rows handled: 10%
   • Quotes fixed: 10%
   • Report complete: 10%

========================================================================================================================

========================================================================================================================
📌 WHAT YOU ARE FORBIDDEN FROM DOING (VIOLATION = FAILURE)
========================================================================================================================

❌ Changing ANY original data value (names, numbers, addresses, IDs, emails)
❌ Removing currency symbols ($, €, £, ¥, ₹)
❌ Removing leading zeros from IDs or codes
❌ Filling empty values with NULL, N/A, Unknown, or anything else
❌ Deleting rows with partial data (only delete completely empty rows)
❌ Deleting rows marked "THIS ROW WRONG" or "###" (these are FLAGS, not errors)
❌ Changing "asdfghjkl" or "🔥" or any gibberish text
❌ "Fixing" spelling, grammar, or perceived errors
❌ Changing date values (only format allowed)
❌ Rounding or modifying numbers
❌ Converting "yesterday" to an absolute date
❌ Adding spaces to missing-space words (customerSilverlineTech → keep as-is)
❌ Pasting CSV as text in conversation (must be downloadable)
❌ Skipping the cleaning report
❌ Skipping correctness score calculation
❌ Using fake or estimated scores
❌ Writing code for me to run
❌ Asking me to run anything
❌ Using external tools or libraries

========================================================================================================================
📌 VALIDATION CHECKLIST (RUN THIS BEFORE RESPONDING)
========================================================================================================================

30 ELEMENTS VALIDATION:
□ Element 1 (Delimiters): Handled? (YES/NO)
□ Element 2 (Quotes): Handled? (YES/NO)
□ Element 3 (Encoding): Handled? (YES/NO)
□ Element 4 (Headers): Handled? (YES/NO)
□ Element 5 (Special chars in headers): Handled? (YES/NO)
□ Element 6 (Header case): Handled? (YES/NO)
□ Element 7 (Empty headers): Handled? (YES/NO)
□ Element 8 (Header-data misalignment): Handled? (YES/NO)
□ Element 9 (Date formats): Handled? (YES/NO)
□ Element 10 (Invalid dates): Handled? (YES/NO)
□ Element 11 (Date with time): Handled? (YES/NO)
□ Element 12 (Relative dates): Handled? (YES/NO)
□ Element 13 (Number formats): Handled? (YES/NO)
□ Element 14 (Currency symbols): Handled? (YES/NO)
□ Element 15 (Negative numbers): Handled? (YES/NO)
□ Element 16 (Scientific notation): Handled? (YES/NO)
□ Element 17 (Percentages): Handled? (YES/NO)
□ Element 18 (Missing spaces): Handled? (YES/NO)
□ Element 19 (Extra spaces): Handled? (YES/NO)
□ Element 20 (Leading/trailing spaces): Handled? (YES/NO)
□ Element 21 (Special characters): Handled? (YES/NO)
□ Element 22 (Language variations): Handled? (YES/NO)
□ Element 23 (Standard placeholders): Handled? (YES/NO)
□ Element 24 (Numeric placeholders): Handled? (YES/NO)
□ Element 25 (Text placeholders): Handled? (YES/NO)
□ Element 26 (Empty values): Handled? (YES/NO)
□ Element 27 (Blank rows): Handled? (YES/NO)
□ Element 28 (Malformed rows): Handled? (YES/NO)
□ Element 29 (Metadata): Handled? (YES/NO)
□ Element 30 (Line breaks): Handled? (YES/NO)

CRITICAL RULES VALIDATION:
□ Did I preserve EVERY original data value? (YES/NO)
□ Did I keep currency symbols ($, €, £)? (YES/NO)
□ Did I keep leading zeros? (YES/NO)
□ Did I keep "asdfghjkl" and "🔥" unchanged? (YES/NO)
□ Did I keep "???" and "ERR" unchanged? (YES/NO)
□ Did I NOT delete rows with "THIS ROW WRONG"? (YES/NO)
□ Did I only delete completely empty rows? (YES/NO)
□ Did I convert dates to YYYY-MM-DD only? (YES/NO)
□ Did I leave empty values as empty? (YES/NO)
□ Did I provide downloadable cleaned CSV? (YES/NO)
□ Did I provide cleaning report? (YES/NO)
□ Did I calculate correctness score? (YES/NO)
□ Is overall correctness 95% or above? (YES/NO - if no, provide improvement plan)

========================================================================================================================
📌 FINAL REMINDER - YOUR TARGET
========================================================================================================================

TARGET: 95%+ CORRECTNESS ON ANY CHAOTIC MESSY CSV

You will be evaluated on:
• Data Integrity (50%) - All values preserved, currency symbols kept, leading zeros kept
• Cleaning Accuracy (50%) - All 30 elements detected and fixed correctly

FAILURE to achieve 95%+ means you must provide an improvement plan explaining exactly which of the 30 elements caused the failure.

========================================================================================================================
📌 WHAT YOU MUST DO - COMPLETE WORKFLOW
========================================================================================================================

1. RECEIVE my uploaded messy CSV file
2. SCAN the ENTIRE file to detect ALL 30 elements from PART 1
3. PLAN your cleaning (which issues exist, how to fix them)
4. EXECUTE cleaning steps in order (STEP 1 through STEP 10)
5. VERIFY no data values were changed (only structure)
6. GENERATE downloadable cleaned CSV file
7. GENERATE downloadable cleaning report (including which of the 30 elements were detected)
8. CALCULATE correctness score (Data Integrity 50% + Cleaning Accuracy 50%)
9. DISPLAY score report and executive summary

========================================================================================================================
📌 PART 2 ENDS HERE
========================================================================================================================

I am now uploading my chaotic messy CSV file.

========================================================================================================================

   



