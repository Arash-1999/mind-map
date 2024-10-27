# Introduction to Logfmt

It is notable for its compact structure of `key=value` pairs and its popularity stems from being both easily readable for humans and effortlessly parseable by machines.

### Basic Structure

In this format, each key/value pair is distinctly separated by a space, with an equal sign (`=`) marking the division between a key and its associated value.

### Nested Structure

To accommodate nested data within Logfmt, a dot notation can be utilized.

```
level=INFO host=Ubuntu msg="Application running smoothly" properties.status=OK
properties.details="No errors detected" properties.performance=Optimal
```

```json
{
  "info": "INFO",
  "msg": "Application running smoothly",
  "properties": {
    "status": "OK",
    "details": "No errors detected",
    "performance": "Optimal"
  }
}
```

**Note:** most Logfmt implementations treat each key/value pair independently, without recognizing any nested structure suggested by the dot notation.

### Good to Follow Notes:

1. don't use spaces in keys. (use snake_case or camelCase)
2. keys should only contain alphanumeric chars.
3. don't use nested structure
4. colorize keys for better readability
