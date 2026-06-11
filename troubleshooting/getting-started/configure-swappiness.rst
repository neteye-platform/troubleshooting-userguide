.. _ts-configure-swappiness:

Configure swappiness
---------------------------

In this section you will learn how to configure Swap usage on NetEye.

.. _ts-configure-swappiness-single:

What is Swappiness
~~~~~~~~~~~~~~~~~~~~~~~~~

Swappiness is a kernel parameter that controls how the system handles the management of virtual memory,
particularly when it comes to swapping data between RAM (Random Access Memory) and the swap space
(a designated area on the hard drive used as an extension of physical memory).

When a system's physical RAM becomes fully utilized, the kernel must decide which data to keep in RAM
and which data to move to the swap space to free up space for new data.
Swappiness determines the balance between preserving physical memory and making efficient use of the swap space.

The swappiness value, ranging from 0 to 200, controls the degree to which the system favors reclaiming memory from the anonymous memory pool, or the page cache memory pool.

* A swappiness value of 1: The kernel avoids swapping data out of RAM as much as possible. It prefers to keep data in RAM and only start swapping when the RAM is almost completely full.

* A swappiness value of 200: The kernel aggressively moves data to the swap space. It prioritizes freeing up RAM space, even if it's not being fully utilized.

* Setting the `vm.swappiness` to `0` aggressively avoids swapping anonymous memory out to a disk, this increases the risk of processes being killed by the oom_killer function when under memory or I/O intensive workloads.

The default swappiness value is set to a moderate value of 60.

Setting Swappiness value
~~~~~~~~~~~~~~~~~~~~~~~~~

To check actual swappiness value set for every |cgroup_link| on your system use:

    .. |cgroup_link| raw:: html

       <a href="https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/setting-limits-for-applications_managing-monitoring-and-updating-the-kernel#understanding-control-groups_setting-limits-for-applications" target="_blank">cgroup</a>


    .. code-block:: shell

       grep . /sys/fs/cgroup/memory/*/memory.swappiness

To change the value for every cgroup create or edit file `/etc/sysctl.d/zzz-swappiness.conf`

    .. code-block:: shell

       neteye# cat /etc/sysctl.d/zzz-swappiness.conf

       vm.swappiness = 1
       vm.force_cgroup_v2_swappiness = 1


To immediately apply this change on your system run:

    .. code-block:: shell

       sysctl -p /etc/sysctl.d/zzz-swappiness.conf

The sysctl values are loaded early in boot via initramfs, so finally, rebuild initramfs to override any previous persistent sysctl settings in the initial ramdisk image.

     .. code-block:: shell

        dracut -f -v
