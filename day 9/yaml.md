# YAML
YAML (YAML Ain't Markup Language) is a human-readable data serialization standard. It's widely used for configuration files, including GitHub Actions workflows.

### Basic YAML syntax rules

![rule](image/rule.png)

- list/arrays
```
  # Method 1: Dash notation
fruits:
  - apple
  - banana
  - orange

# Method 2: Inline notation
fruits: [apple, banana, orange]

# Complex list items
employees:
  - name: John
    position: Developer
    skills:
      - Java
      - Python
  - name: Jane
    position: Designer
    skills:
      - Figma
      - Photoshop
```
![rule2](image/rule2.png)

- YAML data types
```
# Strings
name: John
quoted_string: "Hello World"
single_quoted: 'Hello World'

# Numbers
integer: 42
float: 3.14
exponential: 1.2e+10

# Booleans
active: true
disabled: false
enabled: yes  # Also valid
disabled: no  # Also valid

# Null values
empty_value: null
tilde_value: ~
nothing:      # Also null

# Dates
date: 2023-12-25
datetime: 2023-12-25T10:30:00Z
```

