---
title: "Adding tests"
teaching: 10
exercises: 30
---

:::::: questions

## Questions

  - What are unit tests?
  - How are tests organized in Julia?

::::::

:::::: objectives

## Objectives

  - Learn to create unit tests and test sets using the `Test` standard library

::::::

## Unit tests

Now that Melissa has released her first package she fears that future changes
will impact the existing functionality of her package.  This can be prevented by
adding tests to her package.

Looking at the structure of other packages Melissa figured out that tests
usually go in a separate `test` folder next to the `src` folder.  This should
contain a `runtests.jl` file.

The standard library `Test` provides the functionality for writing tests:
namely, the macros `@test` and `@testset`.

`@test` can be used to test a single equality, such as

```julia
using Test
@test 1 + 1 == 2
```

```output
Test Passed
```

Several tests can be grouped in a test set with a descriptive name

```julia
using Test
@testset "Test arithmetic equalities" begin
    @test 1 + 1 == 2
end
```

```output
Test.DefaultTestSet("Test arithmetic equalities", Any[], 1, false, false, true, 1.731669987513481e9, 1.731669987543832e9, false)
```

Melissa sees that she can run her package tests using the pkg mode of the REPL:

```julia
(MelissasModule) pkg> test
```

```error
ERROR: Package MelissasModule did not provide a `test/runtests.jl` file

```

Remember from earlier that the conventional project structure included a
`test/runtests.jl` file? This file is the entry point for package tests.

### Test environment

Melissa needed to add `Test` to her package in order to run the code above, but
actually `Test` is not needed for her package other than testing. Melissa needs
to generate a testing environment with its own dependencies:

```julia
(MelissasModule) pkg> activate projects/MelissasModule/test
```

```output
  Activating new project at `~/projects/MelissasModule/test`

```

```julia
(test) pkg> add Test
```

```output
  Resolving package versions...
    Updating `~/projects/MelissasModule/test/Project.toml`
  [8dfed614] + Test v1.11.0
    Updating `~/projects/MelissasModule/test/Manifest.toml`
  [2a0f44e3] + Base64 v1.11.0
  [b77e0a4c] + InteractiveUtils v1.11.0
  [56ddb016] + Logging v1.11.0
  [d6f4376e] + Markdown v1.11.0
  [9a3f8284] + Random v1.11.0
  [ea8e919c] + SHA v0.7.0
  [9e88b42a] + Serialization v1.11.0
  [8dfed614] + Test v1.11.0

```

Now Melissas project structure looks like this:

```
MelissasModule/
├── Project.toml
├── src
│   └── MelissasModule.jl
└── test
    └── Project.toml
```

And the `test/Project.toml` has:

```
[deps]
Test = "8dfed614-e22c-5e08-85e1-65c5234f0b40"

```

:::::: callout

## Using just one `Project.toml`

Alternatively, Melissa could include the `Test` package as an "extra" in her
top-level `Project.toml` and require that dependency for the `test` target.

Check out the
[Project.toml](https://github.com/JuliaLang/Example.jl/blob/master/Project.toml)
from the Example.jl project for an example of this.

::::::

:::::: challenge

## Challenge

Create a test for MelissasModule in `test/runtests.jl`

Create a test that ensures that `shoot_distance` returns a value that is
between `target - ε` and `target + ε`.

:::::: solution

## Solution

```julia
using MelissasModule
using Test

@testset "Test hitting target" begin
    imprecise_trebuchet = Trebuchet(500.0, 0.25pi)
    environment = Environment(5, 100)
    precise_trebuchet = aim(imprecise_trebuchet, environment)
    final_distance = shoot_distance(precise_trebuchet, environment)
    @test 100 - 0.1 <= final_distance <= 100 + 0.1
    # default ε is 0.1
    # could also use:
    # @test abs(final_distance - 100) <= 0.1
end
```

::::::

::::::

Now Melissa can run this test from pkg mode:

```julia
(MelissasModule) pkg> test
```

```output
     Testing MelissasModule
      Status `/tmp/jl_TTQJwg/Project.toml`
  [34b7a6fa] MelissasModule v0.1.0 `~/projects/MelissasModule`
  [8dfed614] Test v1.11.0
      Status `/tmp/jl_TTQJwg/Manifest.toml`
  [...]
  ⋮
  # Skipping output here setting up the temporary testing environment
  ⋮
     Testing Running tests...
Test Summary:       | Pass  Total   Time
Test hitting target |    1      1  19.7s
     Testing MelissasModule tests passed

```

:::::: keypoints

  - Tests are important

::::::

