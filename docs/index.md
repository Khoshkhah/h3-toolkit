# H3-Toolkit Documentation

Welcome to the H3-Toolkit documentation.

## Contents

- [Concepts](concepts.md) - Core concepts: faces, boundary tracing, and hierarchy
- [API Reference](api_reference.md) - Python and C++ API documentation

## Quick Links

- [GitHub Repository](https://github.com/your-org/h3-toolkit)
- [H3 Documentation](https://h3geo.org/docs/)

## Performance

H3-Toolkit offers both pure Python and C++ backends:

| Backend | Time (43M cells) | Rate | Speedup |
|---------|------------------|------|---------|
| **C++ Bindings** | 10.6s | 4.0M cells/s | **11x** |
| Pure Python | 118.3s | 363K cells/s | 1x |

The library automatically uses C++ when available.
