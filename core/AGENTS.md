# core/ — fo_core Static Library

> Parent: [../AGENTS.md](../AGENTS.md)

## Overview

Core engine library with interfaces, providers, database layer, and business logic. All CLI/GUI functionality delegates here.

## Structure

```
core/
├── include/fo/core/    # Public headers (interfaces, types)
│   ├── interfaces.hpp  # IFileScanner, IHasher, IMetadataProvider, IOCRProvider
│   ├── registry.hpp    # Registry<T> singleton for provider registration
│   ├── engine.hpp      # Engine class (scan, find_duplicates, extract_metadata)
│   ├── database.hpp    # DatabaseManager, migrations
│   └── *.hpp           # Repositories, exporters, rule engine
└── src/                # Implementations
    ├── scanner_*.cpp   # StdFs, Win32, Dirent scanners
    ├── hasher_*.cpp    # Fast64, SHA256, XXHash, BLAKE3
    ├── metadata_*.cpp  # TinyEXIF, Exiv2
    └── *.cpp           # Engine, DB, export, rules
```

## Adding a New Provider

1. Create interface method in `include/fo/core/interfaces.hpp` (if new type)
2. Implement in `src/provider_impl.cpp`
3. Register via Registry pattern:
```cpp
static auto reg = []() {
    Registry<IHasher>::instance().add("myhasher", []() {
        return std::make_unique<MyHasherImpl>();
    });
    return true;
}();
```
4. Add feature guard if optional: `#ifdef FO_HAVE_MYFEATURE`
5. Add test in `../tests/test_hasher.cpp` (or appropriate test file)

## Key Interfaces

| Interface | Purpose | Implementations |
|-----------|---------|-----------------|
| `IFileScanner` | Directory traversal | StdFs, Win32, Dirent |
| `IHasher` | File hashing | Fast64, SHA256, XXHash, BLAKE3 |
| `IMetadataProvider` | EXIF extraction | TinyEXIF, Exiv2 |
| `IOCRProvider` | Text extraction | Tesseract |
| `IPerceptualHasher` | Image similarity | dHash, pHash, aHash |
| `IClassifier` | AI tagging | OnnxRuntime |

## Database Schema

Tables: `files`, `file_hashes`, `file_dates`, `duplicate_groups`, `duplicate_members`, `tags`, `file_tags`, `operation_log`

Migrations in `database.cpp` — auto-applied on first run.

## Feature Guards

| Define | Dependency | Provider |
|--------|------------|----------|
| `FO_HAVE_EXIV2` | vcpkg exiv2 | Exiv2MetadataProvider |
| `FO_HAVE_BLAKE3` | vcpkg blake3 | Blake3Hasher |
| `FO_HAVE_TESSERACT` | vcpkg tesseract | TesseractOCRProvider |
| `FO_HAVE_OPENCV` | vcpkg opencv | OpencvPerceptualHasher |
| `FO_HAVE_ONNX` | vcpkg onnxruntime | OnnxRuntimeClassifier |
