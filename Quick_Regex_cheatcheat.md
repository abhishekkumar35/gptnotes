Below is a concise regex cheatsheet that you can read in about 5 minutes. It covers the essentials, provides quick examples, and explains each key component.

---

## Regex Basics

- **Literals:**  
  Match exact characters.  
  **Example:**  
  - `cat` matches "cat" exactly.

- **Metacharacters:**  
  Special symbols with predefined meanings:  
  - `.` (dot): Matches any character except newline.  
  - `*`: Matches 0 or more of the preceding element.  
  - `+`: Matches 1 or more of the preceding element.  
  - `?`: Matches 0 or 1 of the preceding element (or makes quantifiers non-greedy).  
  - `^`: Asserts the start of a string.  
  - `$`: Asserts the end of a string.

- **Escaping:**  
  Use `\` to treat metacharacters as literals.  
  **Example:**  
  - `\.` matches a literal dot.

- **Character Classes:**  
  Define a set of characters to match.  
  - `[abc]`: Matches a, b, or c.  
  - `[a-z]`: Matches any lowercase letter.  
  - `[A-Z]`: Matches any uppercase letter.  
  - `[0-9]`: Matches any digit.

- **Predefined Classes:**  
  - `\d`: Any digit (equivalent to `[0-9]`).  
  - `\D`: Any non-digit.  
  - `\w`: Any word character (letters, digits, underscore).  
  - `\W`: Any non-word character.  
  - `\s`: Any whitespace (spaces, tabs, etc.).  
  - `\S`: Any non-whitespace.

- **Anchors:**  
  Ensure matches occur at specific positions.  
  - `^`: Start of string.  
  - `$`: End of string.  
  - `\b`: Word boundary.

- **Quantifiers:**  
  Specify how many times a character or group should appear.  
  - `*`: 0 or more times.  
  - `+`: 1 or more times.  
  - `?`: 0 or 1 time.  
  - `{n}`: Exactly n times.  
  - `{n,}`: At least n times.  
  - `{n,m}`: Between n and m times.

- **Grouping & Alternation:**  
  - `( )`: Group expressions or capture matches.  
  - `|`: Logical OR for alternation.  
  **Example:**  
  - `(cat|dog)` matches either "cat" or "dog".

---

## Practical Examples

### 1. Validate Words (Letters Only)
**Regex:** `^[A-Za-z]+$`  
**Explanation:**  
- `^` and `$` anchor the pattern to the start and end of the string.
- `[A-Za-z]+` requires one or more uppercase or lowercase letters.

---

### 2. Validate Numbers (Digits Only)
**Regex:** `^\d+$`  
**Explanation:**  
- `\d+` matches one or more digits.
- Anchors ensure the entire string is composed only of digits.

---

### 3. Validate Phone Numbers (US Format Example)
**Regex:** `^\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}$`  
**Explanation:**  
- `\(?\d{3}\)?` matches an optional opening parenthesis, exactly 3 digits, and an optional closing parenthesis.
- `[-.\s]?` allows an optional separator (dash, dot, or whitespace).
- `\d{3}` matches the next three digits.
- `\d{4}` matches the last four digits.

---

### 4. Validate Email Addresses (Basic Pattern)
**Regex:** `^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$`  
**Explanation:**  
- `[A-Za-z0-9._%+-]+` matches the email username part (one or more valid characters).  
- `@` is the literal separator between the username and domain.  
- `[A-Za-z0-9.-]+` matches the domain name.  
- `\.[A-Za-z]{2,}` ensures a dot followed by at least 2 letters for the top-level domain.

---

### 5. Additional Quick Tips

- **Whitespace Matching:**  
  - Use `\s` to match any whitespace (space, tab, newline).

- **Any Character:**  
  - `.` matches any character except newline. Use `[\s\S]` if you need to include newlines.

- **Repetition:**  
  - `a*` matches zero or more "a"s.  
  - `a+` matches one or more "a"s.

---

This cheat sheet covers the essentials of regex and provides examples that you can experiment with to validate words, numbers, phone numbers, and email addresses. It should serve as a quick reference guide to get you started with regular expressions!
