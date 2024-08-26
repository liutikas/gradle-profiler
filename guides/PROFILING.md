# Guide To Profiling Gradle Builds Using Gradle Profiler

## Prerequisites

1. Run profiling on a machine that is plugged into power and has good cooling. Gradle builds can be CPU intensive and
cause thermal or power-usage throttling. 
2. Minimize background activity on the machine you are using for profiling. E.g. using Chrome in the background can
affect available memory on your machine and skew your results.
3. Ensure ample amount of disk space on the machine. Limited disk space can cause performance thrashing skewing your
results.
4. Use the same machine if you want to compare profiled builds. Reducing the number of variables can make comparisons
more representative.

## Writing A Scenario

1. Focus on a minimal reproduction of the thing you want to measure. The smaller the reproduction reduces the amount of
noise in your measurements, and you also allows you to iterate faster. For example, if you want to profile configuration
time, run with [Gradle `--dry-run` flag](https://docs.gradle.org/current/userguide/command_line_interface.html#sec:command_line_execution_options)
which will skip executing tasks. Similarly, if you only want to measure the performance of a single task, set up
your scenario to only rerun that task with [Gradle `--rerun` flag](https://docs.gradle.org/current/userguide/command_line_interface.html#sec:builtin_task_options).
2. Understand and invalidate the relevant caches between iterations. For example, if you want to measure configuration
phase performance, you should use `clear-configuration-cache-state-before` mutation. Similarly, measuring the
performance of `JavaCompile` tasks, you might want to use `apply-abi-change-to`.
3. Recreate relevant environment for the thing you want to measure. For example, if you use ephemeral build environment
in CI and want to profile some aspect of it, you will want to use `--no-daemon`.

## Running Profiler

```bash
YOURKIT_HOME=/path/to/yourkit/   JAVA_HOME=/path/to/jdk ./build/install/gradle-profiler/bin/gradle-profiler \
    --profile yourkit \
    --no-daemon \
    --project-dir path/to/project/ \
    --scenario-file metalava.scenarios
```
