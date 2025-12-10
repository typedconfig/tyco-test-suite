# Tyco Test Suite

**Canonical test suite for all Tyco parser implementations**

This repository contains the official test suite for the Tyco configuration language. All language implementations (Python, JavaScript, Java, C++, etc.) must use these identical test files to ensure consistent parsing behavior across platforms.

## 📁 Directory Structure

```
tyco-test-suite/
├── README.md          # This file
├── inputs/            # .tyco test input files
└── expected/          # .json expected output files
```

## 🎯 Purpose

The test suite serves as:

1. **Executable Specification** - Defines correct parser behavior through concrete examples
2. **Conformance Tests** - Validates that all implementations parse identically
3. **Regression Tests** - Prevents breaking changes across implementations
4. **Documentation** - Demonstrates real-world Tyco syntax and features
5. **Golden Master** - Python implementation's test outputs are the canonical reference

## 📋 Test Files

Each test file covers specific language features:

### Core Language Features

| Input File | Purpose | Key Features Tested |
|------------|---------|---------------------|
| `simple1.tyco` | Minimal working example | Basic structs, instances, references, templates |
| `basic_types.tyco` | Primitive type system | str, int, float, bool validation |
| `number_formats.tyco` | Integer representations | Decimal, hex (0x), octal (0o), binary (0b) |
| `datetime_types.tyco` | Temporal types | date, time, datetime parsing |

### Data Structures

| Input File | Purpose | Key Features Tested |
|------------|---------|---------------------|
| `arrays.tyco` | Array syntax | Inline arrays `[1, 2, 3]`, typed arrays, nested arrays |
| `nullable.tyco` | Optional fields | `?` prefix, `null` values, nullable arrays |
| `defaults.tyco` | Default values | Schema defaults, instance defaults, value inheritance |

### Advanced Features

| Input File | Purpose | Key Features Tested |
|------------|---------|---------------------|
| `references.tyco` | Cross-references | Primary keys (`*`), `Type(key)` syntax, object lookup |
| `templates.tyco` | String templating | `{variable}` expansion, `{obj.field}` nested access |
| `quoted_strings.tyco` | String formats | `"basic"`, `'literal'`, `"""multiline"""`, escape sequences |

### Edge Cases

| Input File | Purpose | Key Features Tested |
|------------|---------|---------------------|
| `edge_cases.tyco` | Boundary conditions | Empty values, special characters, complex nesting |

## 🔄 Usage in Language Implementations

### Option 1: Git Submodule (Recommended)

Add this repository as a git submodule in your implementation:

```bash
cd tyco-python
git submodule add https://github.com/typedconfig/tyco-test-suite tests/shared
git submodule update --init --recursive
```

### Option 2: Symbolic Links

Create symbolic links from your test directory:

```bash
ln -s ../../tyco-test-suite/inputs tyco-python/tyco/tests/inputs
ln -s ../../tyco-test-suite/expected tyco-python/tyco/tests/expected
```

### Option 3: Direct Copy

Copy files directly (requires manual synchronization):

```bash
cp -r tyco-test-suite/inputs/* tyco-python/tyco/tests/inputs/
cp -r tyco-test-suite/expected/* tyco-python/tyco/tests/expected/
```

## 🧪 Running Tests

Each language implementation has its own test runner that consumes these files:

### Python

```bash
cd tyco-python
pytest -v
```

### JavaScript/TypeScript

```bash
cd tyco-js
npm test
```

### Java

```bash
cd tyco-java
mvn test
```

### C++

```bash
cd tyco-cpp/build
cmake ..
make
./tyco-tests
```

## ✏️ Adding New Test Cases

When adding features to the Tyco language or fixing bugs, add corresponding tests:

### 1. Create the Input File

Create a `.tyco` file in `inputs/` that exercises the feature:

```bash
vim inputs/new_feature.tyco
```

Example content:
```tyco
# Test description: what this file validates
str example: value
```

### 2. Generate Expected Output

Use the **Python reference implementation** to generate the canonical output:

```bash
cd tyco-python
python -c "
import tyco
import json
context = tyco.load('../tyco-test-suite/inputs/new_feature.tyco')
print(json.dumps(context.as_json(), indent=2))
" > ../tyco-test-suite/expected/new_feature.json
```

### 3. Verify Consistency

Run tests in **all** implementations to ensure they produce identical output:

```bash
# Python
cd tyco-python && pytest -v

# JavaScript
cd tyco-js && npm test

# Java
cd tyco-java && mvn test

# C++
cd tyco-cpp/build && ./tyco-tests
```

### 4. Commit to Test Suite

```bash
cd tyco-test-suite
git add inputs/new_feature.tyco expected/new_feature.json
git commit -m "Add test for: <feature description>"
git push
```

### 5. Update Submodules

Each implementation repository must update its submodule reference:

```bash
cd tyco-python
git submodule update --remote tests/shared
git add tests/shared
git commit -m "Update test suite with new_feature test"
```

## 📊 Test Coverage Matrix

Track which tests pass across implementations:

| Test File | Python | JavaScript | Java | C++ |
|-----------|--------|------------|------|-----|
| `simple1.tyco` | ✅ | ✅ | ✅ | ✅ |
| `basic_types.tyco` | ✅ | ✅ | ✅ | ✅ |
| `number_formats.tyco` | ✅ | ✅ | ✅ | ✅ |
| `datetime_types.tyco` | ✅ | ✅ | ✅ | ✅ |
| `arrays.tyco` | ✅ | ✅ | ✅ | ✅ |
| `nullable.tyco` | ✅ | ✅ | ✅ | ✅ |
| `references.tyco` | ✅ | ✅ | ✅ | ✅ |
| `templates.tyco` | ✅ | ✅ | ✅ | ✅ |
| `defaults.tyco` | ✅ | ✅ | ✅ | ✅ |
| `quoted_strings.tyco` | ✅ | ✅ | ✅ | ✅ |
| `edge_cases.tyco` | ✅ | ✅ | ✅ | ✅ |

## 🔍 Test File Format

### Input Files (`.tyco`)

- UTF-8 encoded Tyco configuration files
- Should include comments explaining what's being tested
- Focus on one primary feature per file (though files may test related features)
- Use descriptive struct and field names

### Expected Files (`.json`)

- UTF-8 encoded JSON files
- Generated from Python reference implementation's `context.as_json()` method
- Represent the canonical parsed structure
- All implementations must produce byte-identical JSON output (modulo whitespace)

## 📐 JSON Output Format

The expected JSON structure follows this schema:

```json
{
  "global_var": "value",
  "StructName": [
    {
      "field1": "value",
      "field2": 123,
      "nested_object": {
        "field": "value"
      }
    }
  ]
}
```

### Key Rules:

1. **Global attributes** appear as top-level keys
2. **Struct types** with primary keys appear as arrays of instances
3. **Inline structs** (no primary keys) are embedded as objects
4. **References** are resolved to full object representations
5. **Templates** are expanded to their final string values
6. **Dates/Times** are serialized as ISO 8601 strings
7. **Decimals** are converted to floats for JSON compatibility

## 🐛 Debugging Test Failures

When a test fails in your implementation:

### 1. Verify Input Parsing

Check that your lexer correctly tokenizes the input:

```python
# Python example
import tyco
context = tyco.load('inputs/failing_test.tyco')
print(context._structs)  # Check struct definitions
print(context._globals)  # Check global attributes
```

### 2. Compare JSON Output

Generate JSON from your implementation and diff against expected:

```bash
# Generate your implementation's output
your-parser inputs/test.tyco > /tmp/actual.json

# Compare with expected
diff expected/test.json /tmp/actual.json
```

### 3. Check Specific Differences

Use a JSON diff tool for clearer comparison:

```bash
# Python json.tool for pretty printing
python -m json.tool /tmp/actual.json > /tmp/actual-pretty.json
python -m json.tool expected/test.json > /tmp/expected-pretty.json
diff -u /tmp/expected-pretty.json /tmp/actual-pretty.json
```

### 4. Validate Against Python Reference

Run the same input through Python reference implementation:

```python
import tyco
import json

context = tyco.load('inputs/failing_test.tyco')
print(json.dumps(context.as_json(), indent=2))
```

## 🤝 Contributing

### Test Suite Maintenance

- The Python implementation is the **reference implementation**
- All expected `.json` files must be generated from Python's `context.as_json()`
- New tests require approval and must pass in Python first
- Tests should be minimal and focused on specific features

### Reporting Issues

If you find:
- **Inconsistencies** between expected output and spec → file issue in tyco-test-suite
- **Parser bugs** in a specific implementation → file issue in that implementation's repo
- **Spec ambiguities** → file issue in tyco-test-suite with proposed clarification

## 📚 Related Resources

- **Tyco Specification**: https://typedconfig.io/v0.1.0.html
- **Python Reference Implementation**: https://github.com/typedconfig/tyco-python
- **JavaScript Implementation**: https://github.com/typedconfig/tyco-js
- **Java Implementation**: https://github.com/typedconfig/tyco-java
- **C++ Implementation**: https://github.com/typedconfig/tyco-cpp

## 📄 License

MIT License - Same as Tyco language implementations

## 🏷️ Version

This test suite corresponds to **Tyco Specification v0.1.0**

Future versions may add new test files but should not modify existing ones to maintain backward compatibility testing.
