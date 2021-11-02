# How to restrict process CPU usage using nice, cpulimit, and cgroups

## How to restrict process CPU usage using nice, cpulimit, and cgroups

The Linux kernel is an incredible circus performer, carefully juggling many processes and their resource needs to keep your server humming along. The kernel is also all about equity: when there is competition for resources, the kernel tries to distribute those resources fairly.

However, what if you've got an important process that needs priority? What about a low-priority process? Or what about limiting resources for a group of a processes?

**The kernel can't determine what CPU processes are important without your help**.

Most processes are started at the same priority level and the Linux kernel schedules time for each task evenly on the processor. Have a CPU intensive process that can be run at a lower priority? Then you need to tell the scheduler about it!

There are at least three ways in which you can control how much CPU time a process gets:

Use the

* **`nice`**command to manually lower the task's priority.
* Use the**`cpulimit`**command to repeatedly pause the process so that it doesn’t exceed a certain limit.
* Use Linux’s built-in **control groups**, a mechanism which tells the scheduler to limit the amount of resources available to the process.

Let's look at how these work and the pros and cons of each.

## Simulating high CPU usage

Before looking at these three techniques, we need to find a tool that will simulate high CPU usage on a system. We will be using CentOS as our base system, and to artificially load the processor we can use the prime number generator from the [Mathomatic toolkit](http://www.mathomatic.org/).

There isn’t a prebuilt package for CentOS so you will need to build it yourself. Download the source code from [http://mathomatic.orgserve.de/mathomatic-16.0.5.tar.bz2](http://mathomatic.orgserve.de/mathomatic-16.0.5.tar.bz2) and then unpack the archive file. Change directory into`mathomatic-16.0.5/primes`. Run`make`and`sudo make install`to build and install the binaries. You will now have the`matho-primes`binary in`/usr/local/bin`.

Run the command like this:

```text
/usr/local/bin/matho-primes 0 9999999999 > /dev/null &
```

This will generate a list of prime numbers from zero to nine billion nine hundred ninety-nine million nine hundred ninety-nine thousand nine hundred ninety-nine. Since we don’t really want to keep the list, the output is redirected to`/dev/null`.

Now run top and you will see that the matho-primes process is using all the available CPU.

![](../.gitbook/assets/import.png)  
Exit top \(press the q key\) and kill the matho-primes process \(fg to bring the process to the foreground and press CTRL+C\).

## nice

The`nice`command tweaks the priority level of a process so that it runs less frequently.**This is useful when you need to run a CPU intensive task as a background or batch job**. The niceness level ranges from -20 \(most favorable scheduling\) to 19 \(least favorable\). Processes on Linux are started with a niceness of 0 by default. The nice command \(without any additional parameters\) will start a process with a niceness of 10. At that level the scheduler will see it as a lower priority task and give it less CPU resources.

Start two`matho-primes`tasks, one with nice and one without:

```text
nice matho-primes 0 9999999999 > /dev/null &
matho-primes 0 9999999999 > /dev/null &
```

Now run`top:`

![](../.gitbook/assets/import2.png)

Observe that the process started without`nice`\(at niceness level 0\) gets more processor time, whereas the process with a niceness level of 10 gets less.

What this means in real terms is that if you want to run a CPU intensive task you can start it using nice and the scheduler will always ensure that other tasks have priority over it. This means that the server \(or desktop\) will remain responsive even when under heavy load.

Nice has an associated command called`renice`. It changes the niceness level of an already running process. To use it, find out the PID of process hogging all the CPU time \(using ps\) and then run`renice`:

```text
renice +10 1234
```

Where 1234 is the PID.

Don’t forget to kill the`matho-primes`processes once you have finished experimenting with the`nice`and`renice`commands.

## cpulimit

The`cpulimit`tool curbs the CPU usage of a process by pausing the process at different intervals to keep it under the defined ceiling. It does this by sending SIGSTOP and SIGCONT signals to the process. It does not change the`nice`value of the process, instead it monitors and controls the real-world CPU usage.

**`cpulimit`is useful when you want to ensure that a process doesn't use more than a certain portion of the CPU.**The disadvantage over`nice`is that the process can't use all of the available CPU time when the system is idle.

To install it on CentOS type:

```text
wget -O cpulimit.zip https://github.com/opsengine/cpulimit/archive/master.zip
```

```text
unzip cpulimit.zip
cd cpulimit-master
make
sudo cp src/cpulimit /usr/bin
```

The commands above will download the source code from GitHub, unpack the archive file, build the binary, and copy it to`/usr/bin`.

`cpulimit`is used in a similar way to`nice`, however you need to explicitly define the maximum CPU limit for the process using the ‘-l’ parameter. For example:

```text
cpulimit -l 50 matho-primes 0 9999999999 > /dev/null &
```

![top](https://dl.dropboxusercontent.com/u/468982/blog/cpu_usage_blog/image03.jpg)

Note how the`matho-primes`process is now only using 50% of the available CPU time. On my example system the rest of the time is spent in idle.

You can also limit a currently running process by specifying its PID using the ‘-p’ parameter. For example

```text
cpulimit -l 50 -p 1234
```

Where 1234 is the PID of the process.

## cgroups

Control groups \(cgroups\) are a Linux kernel feature that allows you to specify how the kernel should allocate specific resources to a group of processes. With cgroups you can specify how much CPU time, system memory, network bandwidth, or combinations of these resources can be used by the processes residing in a certain group.

**The advantage of control groups over`nice`or`cpulimit`is that the limits are applied to a set of processes, rather than to just one.**Also,`nice`or`cpulimit`only limit the CPU usage of a process, whereas cgroups can limit other process resources.

By judiciously using cgroups the resources of entire subsystems of a server can be controlled. For example in CoreOS, the minimal Linux distribution designed for massive server deployments, the upgrade processes are controlled by a cgroup. This means the downloading and installing of system updates doesn’t affect system performance.

To demonstrate cgroups, we will create two groups with different CPU resources allocated to each group. The groups will be called ‘cpulimited’ and ‘lesscpulimited’.

The groups are created with the`cgcreate`command like this:

```text
sudo cgcreate -g cpu:/cpulimited
sudo cgcreate -g cpu:/lesscpulimited
```

The “-g cpu” part of the command tell cgroups that the groups can place limits on the amount of CPU resources given to the processes in the group. Other contollers include`cpuset`,`memory`, and`blkio`. The cpuset controller is related to the cpu controller in that it allows the processes in a group to be bound to a specific CPU, or set of cores in a CPU.

The cpu controller has a property known as cpu.shares. It is used by the kernel to determine the share of CPU resources available to each process across the cgroups. The default value is 1024. By leaving one group \(lesscpulimited\) at the default of 1024 and setting the other \(cpulimited\) to 512, we are telling the kernel to split the CPU resources using a 2:1 ratio.

To set the cpu.shares to 512 in the cpulimited group, type:

```text
sudo cgset -r cpu.shares=512 cpulimited
```

To start a task in a particular cgroup you can use the cgexec command. To test the two cgroups, start matho-primes in the cpulimited group, like this:

```text
sudo cgexec -g cpu:cpulimited /usr/local/bin/matho-primes 0 9999999999 > /dev/null &
```

If you run`top`you will see that the process is taking all of the available CPU time.

![top](https://dl.dropboxusercontent.com/u/468982/blog/cpu_usage_blog/image01.jpg)

This is because when a single process is running, it uses as much CPU as necessary, regardless of which cgroup it is placed in. The CPU limitation only comes into effect when two or more processes compete for CPU resources.

Now start a second matho-primes process, this time in the lesscpulimited group:

```text
sudo cgexec -g cpu:lesscpulimited /usr/local/bin/matho-primes 0 9999999999 > /dev/null &
```

The top command shows us that the process in the cgroup with the greater cpu.shares value is getting more CPU time.

![top](https://dl.dropboxusercontent.com/u/468982/blog/cpu_usage_blog/image02.jpg)

Now start another matho-primes process in the cpulimited group:

```text
sudo cgexec -g cpu:cpulimited /usr/local/bin/matho-primes 0 9999999999 > /dev/null &
```

![https://dl.dropboxusercontent.com/u/468982/blog/cpu&amp;lt;em&amp;gt;usage&amp;lt;/em&amp;gt;blog/image04.jpg](https://dl.dropboxusercontent.com/u/468982/blog/cpu_usage_blog/image04.jpg)

Observe how the CPU is still being proportioned in a 2:1 ratio. Now the two`matho-primes`tasks in the cpulimited group are sharing the CPU equally, while the process in the other group still gets more processor time.

You can [read the full control groups documentation from Red Hat](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Resource_Management_and_Linux_Containers_Guide/chap-Introduction_to_Control_Groups.html) \(which applies equally to CentOS 7\).

