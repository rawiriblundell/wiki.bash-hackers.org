# The kill builtin command

## Synopsis

    kill [-s SIGNAL | -n SIGNALNUMBER | -SIGNAL] PID|JOB

    kill -l|-L [SIGNAL...]

## Description

The `kill` command is used to send signals to processes specified by
their `PID` or their `JOB`-specification.

The signal(s) to be specified can have the following formats:

- Numerical: The signal is specified using its constant numeric value.
  Be aware that not all systems have identical numbers for the signals.
- Symbolic (long): The signal is specified using the same name that is
  used for the constant/macro in the C API (`SIG<name>`)
- Symbolic (short): The signal is specified using the name from the C
  API without the `SIG`-prefix (`<name>`)

Without any specified signal, the command sends the `SIGTERM`-signal.

The `kill` command is a Bash builtin command instead of relying on the
external `kill` command of the operating system to

- be able to use shell job specifications instead of Unix process IDs
- be able to send signals ("kill something") also, when your process
  limit is reached

### Options

| Option            | Description                                                                                                                                                                               |
|:------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-s SIGNAL`       | specifies the signal to send                                                                                                                                                              |
| `-n SIGNALNUMBER` | specifies the signal to send                                                                                                                                                              |
| `-SIGNAL`         | specifies the signal to send                                                                                                                                                              |
| `-l [SIGNAL...]`  | Lists supported/known signal numbers and their symbolic name. If `SIGNAL` is given, only list this signal, translated (if a number is given the symbolic name is printed, and vice versa) |
| `-L [SIGNAL...]`  | Same as `-l [SIGNAL]` (compatiblity option)                                                                                                                                               |

### Return status

| Status | Reason                                                                                              |
|:-------|:----------------------------------------------------------------------------------------------------|
| 0      | no error/success                                                                                    |
| !=0    | invalid option                                                                                      |
| !=0    | invalid signal specification                                                                        |
| !=0    | error returned by the system function (e.g. insufficient permissions to send to a specific process) |

## Examples

### List supported signals

    kill -l

### Send KILL to a process ID

    kill -9 12345

    kill -KILL 12345

    kill -SIGKILL 12345

## Portability considerations

- POSIX(R) and ISO C only standardize symbolic signal names (no numbers)
  and a default action

## See also
