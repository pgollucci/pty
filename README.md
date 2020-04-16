# PTY Stuff

pty is a portable pseudoterminal interface written in C but works well with
code written other programming languages.  It has the basic functionality of
TCL's `expect` but without the TCL dependency.

See the pty-driver.pl script for commentary on how pty works for coprocessing
(driving applications with other applications with an intermediary terminal).

```
    % pty -h
    Usage: pty [ -d driver -einvVh -t timeout ] -- program [ arg ... ]
    Options
    -------
      -d (cmd) Driver/coprocess for passed program
      -e       Noecho for slave pty's line discipline
      -i       Ignore EOF on stdin
      -n       Non interactive mode
      -v       Verbose
      -t (sec) Delay initial read from stdin for a clean pty setup (default=1)
      -h       This help menu
      -V       Version
```

`pty -d $driver` is a cross-language portable `TCL expect`-like interface
for wiring a coprocess's stdin/stdout directly to a pseudoterminal.  Because
stderr is unaltered by the `$driver`, it is connected to the master terminal
controlling the `pty` process. We can hence use the driver as a **copilot** for
terminal sessions.

## Automated population of "common" credentials

If you are dealing with orchestration engines like `ansible` that manage a
secure encrypted-at-rest Vault, and need to supply the password in a simple,
automated fashion without writing it to disk, you can use `pty` to work through
the password delivery to a contolling terminal.  Have a peek at `bin/pty*` to
see how I accomplish this for system login prompts, as well as password-
protected ssh private keys.

`C`-wise, it's just a little hacking beyond what you see in Richard Steven's
_Advanced Programming in the Unix Environment_. `Perl5`-wise, there's a lot
of stuff going on, but the only module dependency is Term::ReadKey.
`Python3-wise` it just depends on the setproctitle module.

## INSTRUCTIONS:

To build the app, edit the Makefile with the right Make.def.* include for
your OS. Make.def.44 is a popular choice but doesn't work on all platforms.
It may require some customization to get your build working, but I've tested
on linux and OSX. Should also work without modification on FreeBSD.

Then build pty with
```
    % make
```
and if you get a working pty executable, run
```
    % make install
```
to install the pty executable and associated scripts to your ~/bin dir.
Do not `make install` as root, it will prevent you from doing so.


# RELEASE NOTES:

## Changes with v1.1.0:

- pty-pw-driver.pl split into pty_driver.pm and pty-driver.pl

- API perldoc documentation for pty-driver.pl


## Changes with v1.0.1:

- pty-pw-driver.pl supports non-terminal intput on STDERR.

- pty-pw-driver.pl can be pulled in as a perl4-ish lib using do() or require().


## Changes with v1.0.0:

- pty dups STDIN instead of STDOUT to STDERR for drivers invoked with -d option.