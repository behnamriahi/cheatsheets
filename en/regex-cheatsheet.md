````markdown
# Regular Expressions Cheatsheet

## Basic Metacharacters
```
.           # Any character except newline
^           # Start of string/line
$           # End of string/line
*           # 0 or more repetitions
+           # 1 or more repetitions
?           # 0 or 1 repetition
{n}         # Exactly n repetitions
{n,}        # n or more repetitions
{n,m}       # Between n and m repetitions
\           # Escape special character
|           # OR operator
()          # Capture group
[]          # Character class
```

## Character Classes
```
[abc]       # Match a, b, or c
[^abc]      # Any character except a, b, or c
[a-z]       # Lowercase letters
[A-Z]       # Uppercase letters
[0-9]       # Digits
[a-zA-Z]    # Any letter
[a-zA-Z0-9] # Alphanumeric characters
```

## Predefined Character Classes
```
\d          # Digit [0-9]
\D          # Non-digit [^0-9]
\w          # Word character [a-zA-Z0-9_]
\W          # Non-word character [^a-zA-Z0-9_]
\s          # Whitespace [ \t\n\r\f\v]
\S          # Non-whitespace [^ \t\n\r\f\v]
\b          # Word boundary
\B          # Non-word boundary
```

## Anchors
```
^           # Start of string/line
$           # End of string/line
\A          # Start of string (not line)
\Z          # End of string (not line)
\b          # Word boundary
\B          # Non-word boundary
```

## Quantifiers
```
*           # 0 or more (greedy)
+           # 1 or more (greedy)
?           # 0 or 1 (greedy)
*?          # 0 or more (lazy)
+?          # 1 or more (lazy)
??          # 0 or 1 (lazy)
{n}         # Exactly n times
{n,}        # n or more times
{n,m}       # Between n and m times
{n,m}?      # Between n and m times (lazy)
```

## Groups
```
(...)       # Capture group
(?:...)     # Non-capturing group
(?P<name>...) # Named capture group (Python)
(?<name>...)  # Named capture group (other languages)
\1          # Backreference to group 1
\2          # Backreference to group 2
(?P=name)   # Named group backreference (Python)
```

## Lookahead and Lookbehind
```
(?=...)     # Positive lookahead
(?!...)     # Negative lookahead
(?<=...)    # Positive lookbehind
(?<!...)    # Negative lookbehind
```

## Flags/Modifiers
```
i           # Case-insensitive
m           # Multiline mode (^ and $ match line boundaries)
s           # Dot matches newline
x           # Verbose mode (ignore whitespace and comments)
g           # Global (find all matches)
```

## Common Patterns

### Email
```regex
^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$
```

### URL
```regex
https?://[^\s/$.?#].[^\s]*
```

### IP Address (IPv4)
```regex
^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$
```

### Phone Number (US)
```regex
^\(?([0-9]{3})\)?[-. ]?([0-9]{3})[-. ]?([0-9]{4})$
```

### Date (YYYY-MM-DD)
```regex
^\d{4}-\d{2}-\d{2}$
```

### Time (HH:MM)
```regex
^([0-1]?[0-9]|2[0-3]):[0-5][0-9]$
```

### Hex Color
```regex
^#?([a-fA-F0-9]{6}|[a-fA-F0-9]{3})$
```

### Username (alphanumeric, underscore, hyphen, 3-16 characters)
```regex
^[a-zA-Z0-9_-]{3,16}$
```

### Password (minimum 8 characters, at least one letter and one number)
```regex
^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$
```

### Credit Card (basic format)
```regex
^\d{4}[-\s]?\d{4}[-\s]?\d{4}[-\s]?\d{4}$
```

### HTML Tag
```regex
<([a-z]+)([^<]+)*(?:>(.*)<\/\1>|\s+\/>)
```

### Whitespace
```regex
\s+         # One or more whitespace
^\s+|\s+$   # Leading and trailing whitespace
```

## Examples by Use Case

### Validation
```regex
# Integer
^-?\d+$

# Positive integer
^\d+$

# Negative integer
^-\d+$

# Decimal number
^-?\d*\.?\d+$

# Alphanumeric
^[a-zA-Z0-9]+$
```

### Extraction
```regex
# Extract domain from URL
https?://([^/]+)

# Extract filename from path
[^/\\]+$

# Extract file extension
\.([^.]+)$

# Extract all words
\b\w+\b
```

### Replacement
```regex
# Remove all spaces
\s+ → (empty string)

# Remove HTML tags
<[^>]+> → (empty string)

# Convert snake_case to camelCase
_([a-z]) → \U$1 (uppercase captured letter)
```

## Language-Specific Examples

### Python
```python
import re

# Matching
re.match(r'\d+', '123abc')          # Match at beginning
re.search(r'\d+', 'abc123')         # Search anywhere
re.findall(r'\d+', 'a1b2c3')        # Find all matches
re.finditer(r'\d+', 'a1b2c3')       # Iterator of matches

# Replacement
re.sub(r'\d+', 'X', 'a1b2c3')       # Replace all
re.subn(r'\d+', 'X', 'a1b2c3')      # Replace and count

# Split
re.split(r'\s+', 'a b  c')          # Split on whitespace

# Compile
pattern = re.compile(r'\d+')
pattern.findall('a1b2c3')
```

### JavaScript
```javascript
// Matching
/\d+/.test('123abc')                // true/false
'123abc'.match(/\d+/)               # Match result

// Replacement
'a1b2c3'.replace(/\d+/g, 'X')       // Replace all

// Split
'a b  c'.split(/\s+/)               // Split on whitespace

// Flags
/pattern/gi                         // Global, case-insensitive
```

### Bash (grep)
```bash
grep -E '\d+' file.txt              # Extended regex
grep -P '\d+' file.txt              # Perl regex
grep -o '\d+' file.txt              # Only matching part
grep -v 'pattern' file.txt          # Invert match
```

### sed
```bash
sed 's/pattern/replacement/' file   # Replace first occurrence
sed 's/pattern/replacement/g' file  # Replace all occurrences
sed -E 's/[0-9]+/X/g' file         # Extended regex
```

## Tips and Tricks

### Greedy vs Lazy
```regex
.*          # Greedy: matches as much as possible
.*?         # Lazy: matches as little as possible

# Example: <div>text</div><div>more</div>
<div>.*</div>   # Matches entire string
<div>.*?</div>  # Matches first <div>text</div>
```

### Word Boundaries
```regex
\bcat\b     # Matches "cat" but not "category" or "scatter"
\Bcat\B     # Matches "cat" in "scatter" but not standalone "cat"
```

### Case Sensitivity
```regex
(?i)pattern # Case-insensitive in many flavors
[Pp]attern  # Match Pattern or pattern
```

### Comments (verbose mode)
```regex
(?x)        # Enable verbose mode
\d{3}       # Three digits
[-.]?       # Optional separator
\d{3}       # Three more digits
```

## Escaping Special Characters
Characters that need escaping: `. ^ $ * + ? { } [ ] \ | ( )`

```regex
\.          # Literal dot
\^          # Literal caret
\$          # Literal dollar
\*          # Literal asterisk
\+          # Literal plus
\?          # Literal question mark
\\          # Literal backslash
\(          # Literal parenthesis
\[          # Literal bracket
```

## Common Mistakes
```regex
# Wrong: . matches any character including special ones
filename.txt

# Correct: escape the dot
filename\.txt

# Wrong: + needs something to repeat
+pattern

# Correct:
pattern+

# Wrong: forgetting to escape special characters in character class
[test.]

# Correct:
[test\.]
```

````
