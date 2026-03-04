# NIFLER – High-Performance Numerical Computing for Erlang/OTP

NIFLER is a comprehensive ecosystem of C NIF and Rust NIFs (via Rustler) libraries for high-performance numerical computing, signal processing, machine learning, and scientific applications in Erlang/OTP. Currently, **3 projects are complete** (nifler_core foundation, erlgmp arbitrary-precision arithmetic, erlflint number theory with rigorous ball arithmetic), with **19 more planned** across signal processing, optimization, linear algebra, machine learning, audio-visual, and supplementary domains.

**What is NIFLER?** NIFLER bridges the gap between Erlang's fault tolerance and scalability and the raw performance of numerical C/Rust libraries. It provides arbitrarily-precise arithmetic, linear algebra, signal transforms, and machine learning infrastructure with safe C/Rust integration, automatic resource management, and minimal overhead. Each package (_nifler_core_, _erlgmp_, planned _erlblas_, _erlfftw_, etc.) follows a unified design: opaque Erlang resources, dirty scheduler support for heavy ops, and standardized error handling.

**Why NIFLER?** Traditional approaches (Python ports, Java interop, pure Erlang) sacrifice either performance, safety, or code clarity. NIFLER combines the best:
- **Performance**: Direct C/Rust bindings to mature libraries (GMP, MPFR, OpenBLAS, FFTW)
- **Safety**: Resource-backed values with automatic cleanup, no dangling pointers
- **Erlang integration**: Uses BEAM schedulers properly (dirty CPU for heavy work), fault tolerance preserved
- **Portability**: Serializable numeric values for distributed computation

## Features

- **Multi-Package Ecosystem** – Modular, composable libraries targeting different numerical domains
  - **Phase 0 (Complete):** `nifler_core` (foundation, tensor type system, shared C helpers)
  - **Phase 1 (In Progress):** `erlgmp` (arbitrary-precision arithmetic) ✅, `erlflint` (number theory + Arb rigorous balls) ✅, `erlblas` (linear algebra), `erlfftw` (signal processing), `erlmath` (Cephes special functions)
  - **Phase 2:** `erlvolk` (SIMD), `erldsp` (audio DSP), `erlopt` (optimization + cminpack), `erlode` (ODE/DAE solvers), `erlhighs` (LP/MIP), `erlgsl` (quadrature/interpolation/statistics), `erlsym` (symbolic algebra)
  - **Phase 3:** `erlsndfile` (audio I/O), `erlopus` (codec), `erlrubberband` (time-stretch), `erlffmpeg` (video/audio), `erlopencv` (computer vision)
  - **Phase 4:** `erltorch` (ML inference)
  - **Phase 5:** `erlsparse` (sparse linear algebra), `erlgraph` (graph algorithms), `erlqhull` (computational geometry)
  - **Deferred:** `erltorch_cuda`, `erlonnx`, `erleigen`, `erlceres`, `erlcgal`; database NIFs (`erldatafusion`, `erldelta`, `erliceberg`) excluded

- **Arbitrary-Precision Arithmetic** – GMP-backed integers, rational numbers, and MPFR-backed decimal floats with configurable precision (erlgmp)
  - Integer operations: add, subtract, multiply, divide, power, square root, comparison, sign, absolute value
  - Number-theory: Probabilistic primality testing, next prime, Jacobi/Legendre/Kronecker symbols
  - Combinatorics: Factorial, binomial coefficients, Fibonacci and Lucas numbers
  - Rational arithmetic: add, subtract, multiply, divide, comparison with automatic normalization
  - Floating-point operations: add, multiply, divide, power, square root, comparison (with configurable bit precision)
  - Serialization: binary and string formats for all number types for storage and transmission

- **Linear Algebra** (Planned Phase 1.2) – OpenBLAS and LAPACK integration for matrix operations (erlblas)
  - Matrix-matrix and matrix-vector operations with row-major/column-major layout support
  - Linear solvers: LU, Cholesky, QR factorization
  - Eigenvalue and singular value decomposition

- **Signal Processing** (Planned Phase 1.3) – FFTW bindings for fast Fourier transforms, convolution, and spectral analysis (erlfftw)
  - Forward and inverse FFT, real-valued FFT, multidimensional transforms
  - Direct and fast convolution
  - Built-in window functions for spectral analysis

- **Foundation Layer** – Standardized abstractions reused across all packages (nifler_core)
  - Canonical tensor type system: uniform representation `{Layout, DType, Shape, Binary}` across all packages
  - 8 data types (f32, f64, i32, i64, u8, u16, c64, c128) and 2 layouts (row-major, column-major)
  - Tensor operations: creation, validation, element access, layout conversion
  - Error handling: 14 standardized error constructors (parse_error, division_by_zero, shape_mismatch, etc.)
  - C/Rust helpers: macros, inline functions, atom converters for rapid NIF development

- **Safe C/Rust NIFs** – Resource-backed values with deterministic lifecycle and minimal overhead
  - Opaque Erlang references to C/Rust structures automatically freed on garbage collection
  - Dirty scheduler annotations ensure heavy operations don't block the main BEAM scheduler
  - Consistent error handling via `nifler_error` module across all packages

- **Binary Serialization** – Portable serialization for distributed computation and persistent storage
  - `to_binary/1` and `from_binary/1` for all numeric types
  - Format includes metadata (precision for MPFR, sign conventions for integers)
  - Enables serialization across BEAM nodes and long-term persistence

- **Development Tools** – Scaffolding and utility scripts for building and testing
  - `scaffold_c_nif.sh` / `scaffold_rust_nif.sh` — Create new package skeletons with proper `rebar.config`, `.gitignore`, test stubs
  - `build_all.sh` — Build projects in dependency order (nifler_core first)
  - `test_all.sh` — Run all test suites with color output and summary
  - `setup_system_deps.sh` — Install system library headers and binaries
  - `verify_deps.sh` — Pre-flight check for required packages with actionable error messages

## Use Cases

- **High-Precision Financial Calculations** – Arbitrary-precision arithmetic prevents compound rounding errors in accounting
  - Interest calculations with exact decimal representation (no IEEE 754 loss)
  - Currency conversion, settlement calculations, regulatory compliance (audit trail required)
  - Batch transaction processing where even tiny rounding errors accumulate over millions of records

- **Cryptographic Operations** – Large integer arithmetic powers modern cryptography
  - RSA, ECC key generation and validation
  - Primality testing and prime number generation
  - Implemented: `mpz_powm`, `mpz_powm_ui` (modular exponentiation), `mpz_gcd` (GCD), `mpz_invert` (modular inverse) for key operations

- **Scientific Computing** – Numerical stability is critical for research
  - Statistical aggregates with high precision to prevent divergence from theory
  - Physics simulations requiring exact decimal arithmetic
  - Chemical and biological modeling with arbitrary-precision parameters

- **Machine Learning** – Numerical foundation for inference and training
  - Linear algebra via `erlblas` for matrix operations, tensor decompositions
  - Feature extraction and normalization at scale
  - Eventually GPU-accelerated operations (Phase 5–6)

- **Symbolic Mathematics** – Computer algebra requires exact computation
  - Polynomial evaluation and factorization
  - Rational number arithmetic (integers + fractions)
  - Theorem proving and formal verification

- **Distributed Numerical Computation** – Work distribution with exact portability
  - Generate numeric data on one node, serialize, ship to another, deserialize and continue
  - Cross-platform consensus (no floating-point platform divergence)
  - Real-time signal processing pipelines across cluster

## Quick Start

### Installation

Clone or add NIFLER to your Erlang project:

```bash
cd /workspace/projects/nifler
./scripts/setup_system_deps.sh --all    # Install system libraries
./scripts/build_all.sh                   # Build all NIFLER projects
./scripts/test_all.sh                    # Run all tests
```

Add to your `rebar.config`:

```erlang
{deps, [
    {nifler_core, {git, "file:///workspace/projects/nifler/nifler_core", {branch, "main"}}},
    {erlgmp, {git, "file:///workspace/projects/nifler/erlgmp", {branch, "main"}}}
    % Add additional packages as needed: erlblas, erlfftw, etc.
]}.
```

### Basic Usage – Arbitrary-Precision Arithmetic

```erlang
%% Integer arithmetic (arbitrary precision via GMP)
{ok, A} = erlgmp:mpz_from_string(<<"999999999999999999999999999999">>),
{ok, B} = erlgmp:mpz_from_string(<<"111111111111111111111111111111">>),
{ok, Sum} = erlgmp:mpz_add(A, B),
{ok, SumStr} = erlgmp:mpz_to_string(Sum),
% SumStr = <<"1111111111111111111111111111110">>

%% Decimal arithmetic (arbitrary precision via MPFR)
{ok, X} = erlgmp:mpfr_from_string(<<"0.1">>, 256),
{ok, Y} = erlgmp:mpfr_from_string(<<"0.2">>, 256),
{ok, Z} = erlgmp:mpfr_add(X, Y),
{ok, ZStr} = erlgmp:mpfr_to_string(Z),
% ZStr ≈ "0.30000000..." (exact to 256 bits ≈ 77 decimal digits)
```

### Basic Usage – Tensor Operations

```erlang
%% Create a 2×3 matrix using nifler_core
Bin = <<1.0:64/float, 2.0:64/float, 3.0:64/float,
         4.0:64/float, 5.0:64/float, 6.0:64/float>>,
Tensor = nifler_tensor:new(row_major, f64, {2, 3}, Bin),

%% Validate and convert layouts
ok = nifler_tensor:validate(Tensor),
{col_major, f64, {2, 3}, TransposedBin} = nifler_tensor:convert_layout(Tensor, col_major).
```

## Core Packages

### Phase 0: Foundation — Complete ✅

#### [nifler_core](nifler_core/README.md) — Foundation Library

Foundational abstractions for all NIFLER packages:
- **Canonical Tensor Type System** – Unified 4-tuple `{Layout, DType, Shape, Binary}` for all numerical operations
- **Type Definitions** – Erlang and C definitions for layouts (row/col major), data types (8 numeric types), and shapes
- **Tensor Operations** – Construction, validation, element access, layout conversion
- **C NIF Helpers** – Macros, inline functions, atom converters for rapid C NIF development
- **Error Handling** – Standardized 14 error constructors for consistency across ecosystem

**Status:** ✅ Complete ([API](nifler_core/docs/API_Documentation_nifler_core.md), [Developer Ref](nifler_core/docs/Developer_Reference_nifler_core.md))

### Phase 1: Core Numerics — In Progress (2 of 5 complete)

#### [erlgmp](erlgmp/README.md) — Arbitrary-Precision Arithmetic ✅ Complete

GMP and MPFR bindings for unlimited-precision numerics:
- **Integer Operations (mpz_*)** – 61 functions: arithmetic, division modes, modular exponentiation, GCD/LCM, primality testing, factorization, number theory
- **Rational Operations (mpq_*)** – 18 functions: rational arithmetic with automatic normalization
- **Floating-Point (mpfr_*)** – 90 functions: arithmetic with configurable precision and rounding modes, transcendental functions (sin, cos, exp, log), special functions (gamma, zeta, Airy, beta), constants
- **Serialization** – Binary and string conversion for storage and transmission
- **Dirty Scheduler Support** – Heavy operations avoid blocking main scheduler

**Status:** ✅ Complete, 112 total function signatures ([API](erlgmp/docs/API_Documentation_erlgmp.md), [Developer Ref](erlgmp/docs/Developer_Reference_erlgmp.md))

#### [erlflint](erlflint/README.md) — Number Theory + Rigorous Ball Arithmetic ✅ Complete

FLINT 3 + Arb bindings for exact integer algebra and certified numerical enclosures:
- **Exact Integer Operations (fmpz_*)** – Arithmetic, division, power, GCD, LCM, factorization, primality testing
- **Polynomial Arithmetic (fmpz_poly_*)** – Construction, arithmetic, GCD over Z[x]
- **Rigorous Ball Arithmetic (arb_*)** – Midpoint-radius real balls with guaranteed error bounds, arithmetic, special functions, predicates
- **Complementary to erlgmp** – `erlflint` provides rigorous certification (Arb balls) where `erlgmp` provides speed (floating-point approximation)

**Status:** ✅ Complete, 42 function signatures ([API](erlflint/docs/API_Documentation_erlflint.md), [Developer Ref](erlflint/docs/Developer_Reference_erlflint.md))

#### [erlblas](erlblas/README.md) — Linear Algebra (Planned Phase 1.3)

OpenBLAS + LAPACK bindings:
- **Matrix Operations** — Matrix-matrix multiply, matrix-vector multiply, transpose, level 1-3 BLAS
- **Linear Solvers** — LU, Cholesky, QR factorization
- **Eigenvalue Solvers** — EVD, SVD for spectral analysis

**Status:** 🔄 Planned for Phase 1.3

#### [erlfftw](erlfftw/README.md) — Signal Processing (Planned Phase 1.4)

FFTW3 bindings for fast Fourier transforms:
- **FFT Operations** — Forward/inverse FFT, real-valued FFT, multidimensional transforms
- **Convolution** — Direct and fast convolution
- **Planning** — Cached plans for repeated operations across same shape

**Status:** 🔄 Planned for Phase 1.4

#### [erlmath](erlmath/README.md) — Special Functions (Planned Phase 1.5)

Cephes mathematical library + quasi-random sequences:
- **Special Functions** — Fast approximations (reciprocal, inverse trig, Bessel, erf, gamma)
- **Quasi-Random** – Sobol, Halton sequences for Monte Carlo
- **Trade-off** – Speed optimized; use `erlflint` (Arb) for certified accuracy

**Status:** 🔄 Planned for Phase 1.5

### Phase 2: Signal, Optimization, Scientific — Planned (7 projects)

**Core projects:**
- **erlvolk** – VOLK SIMD kernels for vector operations
- **erldsp** – liquid-dsp + Speex audio DSP
- **erlopt** – NLopt + OSQP + cminpack (nonlinear least squares) optimization
- **erlode** – SUNDIALS ODE/DAE solvers (IDA, CVODE)
- **erlhighs** – HiGHS LP/MIP solver for linear/mixed-integer programming
- **erlgsl** – GSL: quadrature, interpolation, statistics, root finding
- **erlsym** – SymEngine symbolic algebra (updated 0.13.x)

### Phase 3: Audio-Visual — Planned (5 projects)

- **erlsndfile** – libsndfile + libsamplerate audio I/O and resampling
- **erlopus** – Opus codec for high-quality audio compression
- **erlrubberband** – RubberBand time-stretch and pitch-shift
- **erlffmpeg** – FFmpeg libav* video/audio codecs (H.264, H.265, AV1, VP9, etc.)
- **erlopencv** – OpenCV computer vision (detection, tracking, image processing)

### Phase 4: Machine Learning — Planned (1 project)

- **erltorch** – LibTorch CPU inference for deep learning models

### Phase 5: Supplementary — Planned (3 projects, independent build)

- **erlsparse** – SuiteSparse sparse linear algebra (LU, Cholesky, QR for sparse matrices)
- **erlgraph** – igraph graph algorithms (shortest path, centrality, clustering)
- **erlqhull** – Qhull computational geometry (convex hull, Delaunay, Voronoi)

### Deferred — Future (5 projects)

- **erltorch_cuda** – LibTorch CUDA (requires separate NVIDIA Docker)
- **erlonnx** – ONNX Runtime inference (complement to erltorch)
- **erleigen** – Eigen small-matrix operations (header-only C++)
- **erlceres** – Ceres Solver bundle adjustment / SLAM
- **erlcgal** – CGAL computational geometry (large C++ templates)

### Excluded at This Time

**Database/Data NIFs:** erldatafusion, erldelta, erliceberg, erlsqlparser (database-adjacent; Polars NIF exists)
**BEAM-incompatible HPC:** SLATE, ScaLAPACK, PETSc, FEniCS (MPI-parallel; conflicts with BEAM scheduler model)
See [new-plan-nifler.md](new-plan-nifler.md) for detailed rationale.

## Key Patterns

### Pattern 1: Parse-Compute-Convert Workflow

```erlang
%% Read → Compute → Output
{ok, A} = erlgmp:mpz_from_string(<<"10000">>),
{ok, B} = erlgmp:mpz_from_string(<<"5">>),
{ok, Result} = erlgmp:mpz_div(A, B),
{ok, ResultStr} = erlgmp:mpz_to_string(Result).
```

### Pattern 2: Tensor Validation and Processing

```erlang
%% Validate tensor + process
case nifler_tensor:validate(Tensor) of
    ok ->
        Layout = nifler_tensor:layout(Tensor),
        DType = nifler_tensor:dtype(Tensor),
        process_tensor(Layout, DType, Tensor);
    {error, Reason} ->
        handle_error(Reason)
end.
```

### Pattern 3: Error Handling Chain

```erlang
%% Chain operations with error short-circuiting
case erlgmp:mpz_from_string(AStr) of
    {ok, A} ->
        case erlgmp:mpz_from_string(BStr) of
            {ok, B} ->
                erlgmp:mpz_add(A, B);
            Error -> Error
        end;
    Error -> Error
end.
```

## Scripts and Tools

All scripts are located in the [scripts/](scripts/) directory and provide utilities for building, testing, scaffolding, and managing NIFLER projects.

### Build & Test

- **`build_all.sh`** — Build all NIFLER projects in dependency order
- **`test_all.sh`** — Run all test suites (CT, EUnit, property-based)
- **`setup_nifler.sh`** — One-shot bootstrap: installs system packages, LibTorch, verifies environment
- **`setup_system_deps.sh`** — Install/manage system library dependencies (GMP, BLAS, FFTW, etc.)
- **`verify_deps.sh`** — Check installed system dependencies and report gaps

### Project Generation

- **`scaffold_c_nif.sh`** — Generate a new C NIF project (or C++ with `--cpp`)
- **`scaffold_rust_nif.sh`** — Generate a new Rustler NIF project with Cargo setup

For detailed usage, see [scripts documentation](docs/Developer_Reference_nifler.md#scripts-and-tools).

## Documentation

### For Users

- **[API_Documentation_nifler.md](docs/API_Documentation_nifler.md)** – Complete consolidated function reference for all NIFLER packages (one-stop listing)
- **[Examples_&_Use_Cases_nifler.md](docs/Examples_&_Use_Cases_nifler.md)** – Real-world patterns, cross-package examples, integration scenarios

### For Contributors

- **[Developer_Reference_nifler.md](docs/Developer_Reference_nifler.md)** – Architecture overview, design principles, roadmap, testing strategy, scripts documentation, extension guidelines

### Component Documentation

Each package includes its own documentation (4-doc set per [DOCUMENTATION_GUIDE_ITEMS.md](instr/DOCUMENTATION_GUIDE_ITEMS.md)):
- nifler_core: [API](nifler_core/docs/API_Documentation_nifler_core.md), [Developer Ref](nifler_core/docs/Developer_Reference_nifler_core.md), [Examples](nifler_core/docs/Examples_&_Use_Cases_nifler_core.md)
- erlgmp: [API](erlgmp/docs/API_Documentation_erlgmp.md), [Developer Ref](erlgmp/docs/Developer_Reference_erlgmp.md), [Examples](erlgmp/docs/Examples_&_Use_Cases_erlgmp.md)
- erlflint: [API](erlflint/docs/API_Documentation_erlflint.md), [Developer Ref](erlflint/docs/Developer_Reference_erlflint.md), [Examples](erlflint/docs/Examples_&_Use_Cases_erlflint.md)

## API Overview

### nifler_core

**Modules:**
- `nifler_types` — Layout and dtype definitions and validators
- `nifler_tensor` — Tensor construction, validation, and manipulation
- `nifler_error` — Standardized error constructors

**Key Functions:**
```
nifler_tensor:new/4 — Create tensor from layout, dtype, shape, binary
nifler_tensor:validate/1 — Verify tensor structure and size
nifler_tensor:convert_layout/2 — Transform tensor layout
nifler_types:dtype_size/1 — Get byte size of data type
nifler_error:badarg/1 — Create badarg error tuple
```

### erlgmp (Arbitrary-Precision Arithmetic)

**Integer Functions (mpz_*) — 61 functions:**
```
erlgmp:mpz_from_string/1, mpz_to_string/1 — Parse and convert integers
erlgmp:mpz_add/2, mpz_sub/2, mpz_mul/2, mpz_div/2 — Basic arithmetic
erlgmp:mpz_mod/2, mpz_tdiv_qr/2, mpz_fdiv_qr/2, mpz_cdiv_qr/2, mpz_divexact/2 — Division modes
erlgmp:mpz_gcd/2, mpz_lcm/2, mpz_gcdext/2, mpz_invert/2 — GCD/LCM/modular inverse
erlgmp:mpz_powm/3, mpz_powm_ui/3 — Modular exponentiation
erlgmp:mpz_pow/2, mpz_sqrt/1, mpz_root/2, mpz_rootrem/2, mpz_sqrtrem/1 — Powers and roots
erlgmp:mpz_probab_prime_p/2, mpz_nextprime/1 — Primality testing
erlgmp:mpz_fac_ui/1, mpz_bin_uiui/2, mpz_fib_ui/1, mpz_lucnum_ui/1 — Combinatorics
erlgmp:mpz_and/2, mpz_ior/2, mpz_xor/2, mpz_com/1 — Bitwise operations
erlgmp:mpz_cmp/2 — Comparison
```

**Rational Functions (mpq_*) — 18 functions:**
```
erlgmp:mpq_from_string/1, mpq_to_string/1 — Parse and convert rationals
erlgmp:mpq_add/2, mpq_sub/2, mpq_mul/2, mpq_div/2 — Arithmetic
erlgmp:mpq_num/1, mpq_den/1, mpq_inv/1, mpq_cmp/2 — Access and comparison
```

**Floating-Point Functions (mpfr_*) — 90 function names (139 arities):**
```
erlgmp:mpfr_from_string/2, mpfr_to_string/1 — Parse and convert floats (precision in bits)
erlgmp:mpfr_add/2,/3, mpfr_mul/2,/3, mpfr_div/2,/3, mpfr_pow/2,/3 — Arithmetic with rounding
erlgmp:mpfr_exp/1,/2, mpfr_log/1,/2, mpfr_sin/1,/2, mpfr_cos/1,/2 — Transcendental functions
erlgmp:mpfr_gamma/1,/2, mpfr_erf/1,/2, mpfr_j0/1,/2 — Special functions
erlgmp:mpfr_const_pi/1, mpfr_const_euler/1 — Mathematical constants
erlgmp:mpfr_cmp/2, mpfr_nan_p/1, mpfr_inf_p/1 — Comparison and predicates
```

See [API_Documentation_erlgmp.md](erlgmp/docs/API_Documentation_erlgmp.md) for all functions.

## Testing

All packages include comprehensive test suites using Common Test, EUnit, and property-based testing:

```bash
# Test all packages (in dependency order)
./scripts/test_all.sh

# Test individual package
cd nifler_core && rebar3 ct
cd erlgmp && rebar3 ct && rebar3 eunit

# Verbose test output (for debugging)
./scripts/test_all.sh --verbose

# Test specific suite (erlgmp oracle validation)
cd erlgmp && rebar3 ct --suite erlgmp_compare_gmp_official_SUITE
```

**Test Coverage (Current)**
- **nifler_core**: 18 CT tests
  - Type validation (3 tests)
  - Tensor operations (9 tests)
  - Layout conversion (3 tests)
  - Error constructors (3 tests)

- **erlgmp**: 92 total tests (73 unit, 12 integration, 7 property-based)
  - Unit tests: 73 EUnit (all exported functions)
  - Integration tests: 12 CT (workflows, serialization, precision propagation)
  - Property tests: 7 Proper (algebraic invariants)
  - Oracle validation: 10 tests comparing against GMP 6.3.0 and MPFR 4.2.1 reference binaries

- **erlflint**: 40+ CT + EUnit tests
  - Unit tests: fmpz, fmpz_poly, arb constructors and operations
  - Integration tests: exact integer workflows, ball arithmetic with error bounds
  - Property tests: commutativity, GCD closure, arithmetic identities

**CI/CD Checklist**
```bash
./scripts/verify_deps.sh    # Check system deps installed
./scripts/build_all.sh      # Build all projects
cd nifler_core && rebar3 ct && rebar3 eunit   # nifler_core tests
cd erlgmp && rebar3 ct && rebar3 eunit        # erlgmp tests
./scripts/test_all.sh --verbose               # Aggregate report
```

## Comparison with Alternatives

| Aspect | NIFLER | Python NumPy | Elixir Nx | Haskell | C/C++ |
|--------|--------|---|---|---|---|
| Erlang native | ✓ | ✗ | ✓ | ✗ | ✗ |
| Arbitrary precision | ✓ | Limited | Partial | ✓ | ✗ |
| BEAM integration | ✓ | Port-based | ✓ | ✗ | Manual |
| Performance | Fast (C/Rust) | Excellent | Fast | Good | Excellent |
| Learning curve | Moderate | Low | Low | High | High |

## Building from Source

```bash
# Install system dependencies
sudo ./scripts/setup_system_deps.sh --all

# Build all packages
./scripts/build_all.sh

# Verify installation
./scripts/verify_deps.sh

# Run tests
./scripts/test_all.sh
```

For detailed build instructions, see [Developer_Reference_nifler.md](docs/Developer_Reference_nifler.md).

## Build Status & Roadmap

**Phase 0: Foundation — ✅ Complete**
- ✅ nifler_core (shared C headers, Erlang conventions, tensor type system)

**Phase 1: Core Numerics — 🔄 In Progress (2/5 complete)**
- ✅ 1.1 erlgmp – Arbitrary-precision arithmetic (195 functions, 244 arities, 92 tests with GMP/MPFR oracle validation)
  - Integer (mpz): 61 functions with division modes, GCD/LCM, extended GCD, modular inverse, primality, factorization
  - Rational (mpq): 18 functions with auto-normalization
  - Floating-point (mpfr): 90 functions with configurable precision, transcendental, special, constants
- ✅ 1.2 erlflint – Number theory + rigorous ball arithmetic (42 functions, 40+ tests)
  - Exact integers (fmpz): arithmetic, number theory
  - Polynomials (fmpz_poly): algebra over Z[x]
  - Rigorous balls (arb): midpoint-radius arithmetic with certified error bounds
- 🔄 1.3 erlblas – Linear algebra (OpenBLAS/LAPACK) — planned
- 🔄 1.4 erlfftw – Signal processing (FFTW) — planned
- 🔄 1.5 erlmath – Special functions (Cephes) — planned

**Phase 2: Signal, Optimization, Scientific — 🔄 Planned (7 projects)**
- erlvolk, erldsp, erlopt, erlode, erlhighs, erlgsl, erlsym

**Phase 3: Audio-Visual — ⏳ Planned (5 projects)**
- erlsndfile, erlopus, erlrubberband, erlffmpeg, erlopencv

**Phase 4: Machine Learning — ⏳ Planned (1 project)**
- erltorch (LibTorch CPU)

**Phase 5: Supplementary — ⏳ Planned (3 projects, parallel-buildable)**
- erlsparse, erlgraph, erlqhull

**Timeline outlook:**
- Phases 1–2 (Numerics + Signal): 2–4 months
- Phase 3 (Audio-Visual): 4–6 months  
- Phase 4 (ML): 6–8 months
- Phase 5 (Supplementary): parallel to Phases 2–4
- Deferred projects (CUDA, ONNX, symbolic variants) addressed in follow-up planning

For detailed build order, architectural decisions, shared conventions, and per-project scaffolding templates, see [new-plan-nifler.md](new-plan-nifler.md).

## License

MIT License – See [LICENSE](LICENSE) for details.

## Contributing

Contributions welcome! To add a new NIFLER package:

1. **Use scaffolding scripts:** `./scripts/scaffold_c_nif.sh name` or `./scripts/scaffold_rust_nif.sh name`
2. **Follow nifler_core patterns** for error handling, tensor types, and resource management
3. **Write comprehensive tests** (unit, integration, property-based)
4. **Document per DOCUMENTATION_GUIDE_ITEMS** (README + 3 docs)
5. **Ensure scripts/test_all.sh and scripts/build_all.sh work**

See [Developer_Reference_nifler.md](docs/Developer_Reference_nifler.md#extensions-and-roadmap) for extension guidelines.

## Acknowledgments

NIFLER builds on world-class numerical libraries:
- [GNU GMP](https://gmplib.org) – Arbitrary-precision arithmetic
- [GNU MPFR](https://www.mpfr.org) – Decimal floating-point
- [OpenBLAS](https://www.openblas.net) – Linear algebra (upcoming)
- [FFTW](http://www.fftw.org) – Fast Fourier transforms (upcoming)
- [Rust & Rustler](https://www.rust-lang.org) – Safe systems programming

NIFLER is maintained as part of the Erlang numerical computing ecosystem.
