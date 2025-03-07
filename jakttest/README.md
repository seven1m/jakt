# Jakttest

Jakttest is the test driver we use to test the Jakt compiler with code snippets.
It's also written in Jakt.

## Build dependencies

To build Jakttest you should use the `ninja` build system which will use the provided `build.ninja` file.
It needs the same `clang++` that is required to build any other Jakt file, and `python` 3.6+ to run the
patch script that patches the generated C++ from Jakt code to accept the external C++ code.

As a list:

- `ninja` (or `ninja-build` on some package managers)
- `clang++`

## How to run it?

If you've never used Jakttest before or you transitioned from a commit older
than [`c232f03`](https://github.com/SerenityOS/jakt/commit/c232f03570247d947a1bfb6373e6d41b3b9219a7),
you'll have to first run the following to generate the binary:

```shell
ninja -C jakttest
```

Once done, Jakttest will try to update automatically, switching the running
binary on the fly if the executable has been updated, so you always run the
tests with the correct Jakttest.

To run the tests, just use the binary:

```shell
./jakttest/build/jakttest
```

## On customizing behavior

Jakttest has some options you can pass to it to customize certain behaviors
(number of jobs, temporary directory to use...) that are set to reasonable
defaults, but you can always customize it. To see them all, check the output
of:

```shell
./jakttest/build/jakttest -h
```

## Test format

Most tests you'll se have a little comment at the top, with **three slashes**:
```jakt
/// Expect:
/// - <tag>: "<escaped output>"
```

There are currently three available tags:
- `output`: Expects the test to compile, succeed execution and output to
  stdandard output.
- `stderr`: Expects the test to compile, but execution fails and output is
  written to stdandard error.
- `error`: Expects the test to be rejected by the Jakt compiler, where the given
  output must appear somewhere in its error output.

## On adding selfhost-only tests

Since CI runs both selfhost and rust-based tests, and because rust-based
development is being discontinued, if you just add a test with the regular
`Expect` format, it will probably get picked by the rust test driver, failing in
the process. For this, add `selfhost-only` after `Expect:`:
```jakt
/// Expect: selfhost-only
/// - ...
```
This will tell the rust test driver to skip the test, while Jakttest will run it
normally.
