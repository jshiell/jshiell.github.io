---
layout: post
title: Practising Safe Bash
---
If you're developing on Linux or OS X you're likely to run into bash (or sh) scripting at some point. And in general, people run into a task, solve it as fast as possible and move on, leaving behind a mess of flakey scripts that don't cope with errors.

Bash provides a couple of tools to make your scripts more robust, and they're very simple to implement (at least for new scripts).

## Error Handling via Options

The default behaviour when a command in a bash script fails is to continue merrily on. This leads to a couple of nasty outcomes: either people expect success, and scripts fail later when a pre-requisite hasn't produced the outcome they expect, or you end up with boilerplate code checking the exit status ($?).

You can change this default behaviour using the *set* command. There are a variety of options, all viewable via the *help set* command, but there's a few which are particularly useful.

### errexit

Setting the *errexit* option will ensure the script exits immediately when a command returns a non-zero error code.

You can activate it using:
```
set -o errexit
```

After this point, any command that fails will immediately stop the script. So you can now run your commands without checking the exit status.

However, sometimes you do care - for instance, you want to check if a *grep* found any matches. For this, you can use an *if* statement:
```
if ! grep a-file some-content; then
    echo 'No content found'
fi
```

If you don't care about the return code, you can use true to ensure the return code remains 0:
```
docker kill acontainer || true
```

You can also use this if you'd like to record the command's output:
```
RUNNING_CONTAINERS=$(docker ps | grep a-container-name || true)
if [[ -n "$RUNNING_CONTAINERS" ]]; then
    # do something
fi
```

### pipefail

Setting *pipefail* will ensure that the return code of a pipe is non-zero if any part of the pipe failed.

You can activate it using:
```
set -o pipefail
```

After this point, the status of a pipe is equal to the status of the last element to exit with a non-zero status, or 0 if all succeeded.

For instance, the following will leave $? with the status of 1, as *anotherword* isn't matched:
```
echo aword | grep anotherword | grep aword
```

When combined with *errexit*, this can ensure that your pipes execute has planned, rather than losing error statuses that occur during the pipeline.

### nounset

Using *nounset* ensures that our script will break if we reference an undefined variable.

You can activate it using:
```
set -o nounset
```

Now the script will break if we try to access any variable that hasn't been defined.

Sometimes you'll need to query the presence of a variable. In this case you can use variable defaults to ensure the variable can be checked without invoking the wrath of *nounset*:
```
AVAR=${AVAR:-}
if [[ -n "$AVAR" ]]; then
    echo "AVAR = $AVAR"
fi
```

## Cleanup via traps

In some scripts you'll need to perform some cleanup before the script exits. You could accomplish this by delaying exiting until the end of the script. Or, you could use traps.
```
trap <function> <signals>
```

In most cases, the signal *EXIT* (a pseudo signal covering the script exiting) should suffice, although you can trap the standard signals as well. If trapping other signals, [you should consider propagating them](http://www.cons.org/cracauer/sigint.html), and [be aware of the effects of dealing with multiple signals](http://stackoverflow.com/questions/8122779/is-it-necessary-to-specify-traps-other-than-exit).

For instance, if you want to remove a temporary file you could use:
```
touch /tmp/afile
trap "{ rm -f /tmp/afile >/dev/null 2>&1 }" EXIT
```
Or, alternately:
```
function cleanup {
    rm -f /tmp/afile >/dev/null 2>&1
}
touch /tmp/afile
trap cleanup EXIT
```

The exit code of the script will not be effected by the trap unless you specify an exit with the trap function.