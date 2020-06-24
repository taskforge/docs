---
title: "Getting Started: CLI client"
linkTitle: "Getting Started: CLI client"
description: >
    Start here if you prefer to use command line clients
---

Welcome to the Taskforge CLI getting started guide. This is everything you need to
know to use Taskforge from a command line environment.

## Installing

First, let's install `task` the CLI client. There are two ways to install the
CLI client at this time. First you can navigate to the [Releases
page](https://github.com/taskforge/cli/releases) and download the appropriate
binaries for your platform. Alternatively you can use our automated installer
like so:

```
curl -o install-taskforge.sh https://raw.githubusercontent.com/taskforge/cli/master/scripts/install.sh
bash ./install-taskforge.sh
```

The install script by default will store the binary in `$HOME/.local/bin` if you
wish to use a different installation directory you can set `INSTALL_DIR` before
running the script like so.

```
# Uses sudo since we're installing into a privileged location on most systems.
INSTALL_DIR=/usr/local/bin sudo bash ./install-taskforge.sh
```

## Using Taskforge

### Your first task

Now that Taskforge is installed we can start using it. Out of the box taskforge
will use a SQLite database to store and retrieve tasks. Lets add a task now:

```
task add complete the Taskforge tutorial
```

To see what tasks are in our list we can use `task list`. Let's run it now:

```
task list
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| ID                                   | CREATED DATE         | TITLE                           | PRIORITY | CONTEXT | COMPLETED DATE |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| 6afa0f00-9c4d-44fa-a73b-9686be0617b1 | 2020/06/24 21:09 UTC | complete the Taskforge tutorial |        1 | default | None           |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
```

### What's next?

If we want to see what our current task is you can use `task next` or 
`task current`:

```
task next
6afa0f00-9c4d-44fa-a73b-9686be0617b1: complete the Taskforge tutorial
```

Taskforge defines the 'current' task as the highest priority task. If all tasks
are of equal priority then the 'current' task is the one with the oldest created
date. To demonstrate let's add a few more tasks: 

```
task add another default priority task
task add --priority 2 a high priority task
```

This introduces a new flag `--priority`. You can set many fields on a task via
flags to the add command. See the [Task add command
documentation](/docs/cli/task-add/) for more
information.

Now our `task list` should look like this:

```
task list
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| ID                                   | CREATED DATE         | TITLE                           | PRIORITY | CONTEXT | COMPLETED DATE |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| e33cd594-b8e8-423a-9ba7-95cca1aa66bc | 2020/06/24 21:10 UTC | a high priority task            |        2 | default | None           |
| 6afa0f00-9c4d-44fa-a73b-9686be0617b1 | 2020/06/24 21:09 UTC | complete the Taskforge tutorial |        1 | default | None           |
| 7230c2f3-dc06-4149-933a-ef665960c740 | 2020/06/24 21:10 UTC | another default priority task   |        1 | default | None           |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
```

If we run `task next` now we'll see that the 'a high priority task' is the
current task:

```
task next
e33cd594-b8e8-423a-9ba7-95cca1aa66bc: a high priority task
```

This is because priority, in the Taskforge world, is the #1 indicator of what
you should be working on. Then you should be working on whatever has been
waiting the longest. You can read the [Workflow](/docs/concepts/workflow) for
more information about the default strategy and how to change it.

### Completing tasks

You can complete tasks with `task done` or `task complete`. Let's complete
our high priority task:

```   
task next
e33cd594-b8e8-423a-9ba7-95cca1aa66bc: a high priority task
task complete e33cd594-b8e8-423a-9ba7-95cca1aa66bc
```

Every task has a unique ID. Most commands will show you this ID for easy use
with other commands like done which take a Task ID as an argument. 

### Viewing incomplete tasks

Now that we've completed this task we'll see that the current task has changed:

```
task next
6afa0f00-9c4d-44fa-a73b-9686be0617b1: complete the Taskforge tutorial
```

However if we run `task list` we will still see the completed task:

```
task list
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------------+
| ID                                   | CREATED DATE         | TITLE                           | PRIORITY | CONTEXT | COMPLETED DATE       |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------------+
| e33cd594-b8e8-423a-9ba7-95cca1aa66bc | 2020/06/24 21:10 UTC | a high priority task            |        2 | default | 2020/06/24 21:13 UTC |
| 6afa0f00-9c4d-44fa-a73b-9686be0617b1 | 2020/06/24 21:09 UTC | complete the Taskforge tutorial |        1 | default | None                 |
| 7230c2f3-dc06-4149-933a-ef665960c740 | 2020/06/24 21:10 UTC | another default priority task   |        1 | default | None                 |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------------+
```

As your task list grows finding tasks that need to be done using `task list`
can be overwhelming. Luckily, Taskforge has a [Query Language](/docs/query-language/) we can use to
search tasks. See the linked documentation for full instructions, but for our
purposes we simply need to run the following:

```
task query completed = false
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| ID                                   | CREATED DATE         | TITLE                           | PRIORITY | CONTEXT | COMPLETED DATE |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| 6afa0f00-9c4d-44fa-a73b-9686be0617b1 | 2020/06/24 21:09 UTC | complete the Taskforge tutorial |        1 | default | None           |
| 7230c2f3-dc06-4149-933a-ef665960c740 | 2020/06/24 21:10 UTC | another default priority task   |        1 | default | None           |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
```


This shows us all tasks which are incomplete. This is such a common query that
there is a shortcut command for displaying this information `task todo`:

```
task todo
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| ID                                   | CREATED DATE         | TITLE                           | PRIORITY | CONTEXT | COMPLETED DATE |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| 6afa0f00-9c4d-44fa-a73b-9686be0617b1 | 2020/06/24 21:09 UTC | complete the Taskforge tutorial |        1 | default | None           |
| 7230c2f3-dc06-4149-933a-ef665960c740 | 2020/06/24 21:10 UTC | another default priority task   |        1 | default | None           |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
```

### Re-ordering tasks

Sometimes a task which you added for later will become the top priority. Such is
the shifting world of todo lists. To accommodate this Taskforge has the `task
workon` command. To demonstrate let's make `another default priority task the
top priority`. To do this let's find its ID with `task todo`:

```
task todo
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| ID                                   | CREATED DATE         | TITLE                           | PRIORITY | CONTEXT | COMPLETED DATE |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| 6afa0f00-9c4d-44fa-a73b-9686be0617b1 | 2020/06/24 21:09 UTC | complete the Taskforge tutorial |        1 | default | None           |
| 7230c2f3-dc06-4149-933a-ef665960c740 | 2020/06/24 21:10 UTC | another default priority task   |        1 | default | None           |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+

```

Then run the `task workon` command providing the ID of the task we want to
re-prioritize:

```
task workon 7230c2f3-dc06-4149-933a-ef665960c740
```


`task next` should now show `another default priority task` as the
current task:

```
task next
7230c2f3-dc06-4149-933a-ef665960c740: another default priority task
```

It accomplishes this by determining the priority of the current task and adding
`1` to it. If we run `task todo` we can see this:

```
task todo
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| ID                                   | CREATED DATE         | TITLE                           | PRIORITY | CONTEXT | COMPLETED DATE |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+
| 7230c2f3-dc06-4149-933a-ef665960c740 | 2020/06/24 21:10 UTC | another default priority task   |        2 | default | None           |
| 6afa0f00-9c4d-44fa-a73b-9686be0617b1 | 2020/06/24 21:09 UTC | complete the Taskforge tutorial |        1 | default | None           |
+--------------------------------------+----------------------+---------------------------------+----------+---------+----------------+

```

Let's go ahead and complete this task now. A shortcut that we did not mention
earlier is that if `task done` is given no arguments it will complete the
current task:

```
task done
task next
6afa0f00-9c4d-44fa-a73b-9686be0617b1: complete the Taskforge tutorial
```

This is a useful shortcut since most often you'll be completing the current task
as you work through your task list.

Further Reading
---------------

You can safely run `task done` now since you've completed the getting started
guide for Taskforge. You can look at some of our [Advanced
Guides](/docs/advanced-usage/) if you want to get more out of Taskforge or you
can read up on how to best find tasks using the [Taskforge Query
Language](/docs/query-language/)
