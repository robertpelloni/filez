# tests/ — Unit & Integration Tests

> Parent: [../AGENTS.md](../AGENTS.md)

## Overview

GTest-based test suite with 59 tests. Unit tests for providers, integration tests for workflows, fuzz tests for robustness.

## Structure

```
tests/
├── CMakeLists.txt      # GTest setup, test discovery
├── test_scanner.cpp    # Scanner provider tests
├── test_hasher.cpp     # Hasher provider tests
├── test_database.cpp   # DB operations, repositories
├── test_integration.cpp # End-to-end workflows
├── test_rule_engine.cpp # Template expansion, rule matching
├── test_export.cpp     # Export format tests
├── test_linter.cpp     # Filesystem linter tests
└── fuzz/               # Fuzz tests (optional build)
    ├── fuzz_scanner.cpp
    ├── fuzz_rule_engine.cpp
    └── fuzz_metadata.cpp
```

## Running Tests

```powershell
.\build\tests\fo_tests.exe                   # All tests
.\build\tests\fo_tests.exe --gtest_filter=ScannerTest.*  # Suite
.\build\tests\fo_tests.exe --gtest_filter=*Duplicates*   # Pattern
```

## Adding a Test

1. Add to existing `test_*.cpp` or create new file
2. Use fixtures for setup/teardown:
```cpp
class MyTest : public ::testing::Test {
protected:
    void SetUp() override {
        temp_dir_ = fs::temp_directory_path() / ("test_" + timestamp());
        fs::create_directories(temp_dir_);
    }
    void TearDown() override {
        fs::remove_all(temp_dir_);
    }
    fs::path temp_dir_;
};

TEST_F(MyTest, SomeFeature) {
    // Test code using temp_dir_
    EXPECT_TRUE(some_condition);
}
```
3. If new file, add to `CMakeLists.txt`

## Test Patterns

- **Provider Tests**: Verify registry registration, basic functionality
- **Integration Tests**: Full workflows (scan → duplicates → export)
- **Platform Guards**: `#ifdef _WIN32` for Windows-specific tests
- **Temp Files**: Always create in `temp_directory_path()`, clean up in TearDown

## Fuzz Testing

Enable with `-DFO_BUILD_FUZZ_TESTS=ON` (requires Clang + sanitizers).

```bash
./build/tests/fuzz_scanner corpus/
```
