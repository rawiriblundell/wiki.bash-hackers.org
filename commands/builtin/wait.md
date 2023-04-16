# The wait builtin command

## Synopsis

    wait [-f] [-n] [-p VARNAME] [ID...]

## Description

The `wait` builtin command is used to wait for job completion and return
exit status.

- if `ID` is a job specification, it waits for all processes in the
  pipeline of this job
- waits for a specific job (asynchronous command) and report its exit
  status if one or more `ID` is given
- waits for all running jobs (asynchronous commands)
- waits for "the next" job (`-n` option)
- waits for termination instead of status change (`-f` option)

`ID` may be an operating system process identifier or a shell job
specification.

### Options

| Option       | Description                                                                                                                     |
|:-------------|:--------------------------------------------------------------------------------------------------------------------------------|
| `-n`         | Waits for "the next" child to exit (as opposed to "all children" without this option). Accepts a list of IDs (jobs)             |
| `-f`         | Waits for the termination of the given `ID` (instead of waiting for a status change only)                                       |
| `-p VARNAME` | When waiting for a list (-n) or all jobs, writes the job ID to the job that was actually terminated into the variable `VARNAME` |

### Return status

The return status is the return status of the job waited for, or

| Status | Reason                                          |
|:-------|:------------------------------------------------|
| 0      | waited for all jobs in shell's job list         |
| 1      | the given `ID` is not a valid job or process ID |

## Examples

## Portability considerations

## See also
