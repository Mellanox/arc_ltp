# Linux Test Project for ARC Linux

For details about LTP see README.ltp and http://ltp.sourceforge.net/
For details about ARC processors see http://www.synopsys.com/dw/ipdir.php?ds=arc_770d

Not all tests from LTP suite are applicable to ARC Linux which is an embedded
system based on uClibc and BusyBox. So some of the tests need to be disabled in
order for LTP to build and run succesfully.


## Build instructions

```
git clone git://github.com/anthony-kolesov/arc_ltp.git
cd arc_ltp
./configure --host=arc-linux-uclibc CC=arc-linux-uclibc-gcc --enable-arc-support CFLAGS="-Dlinux -DUCLINUX"
make
sudo make install
```

## Modifications to original LTP

### Tests that cannot be built

These tests cannot be build by ARC Linux uClibc Toolchain (as of version 4.4). To disable all of
them at once provide --enable-arc-support flag to ./configure command.

* controllers/cpuset
* security/tomoyo
* syscalls/clock_nanosleep (also must be disable in ./runtest/syscalls)


### Tests with changed parameters

These tests can be run on ARC Linux but require are different from default
parameters because they either take too much time to execute or use values that
are too big for an embedded system.

* math/float_bessel - with default parameters take too much time to finish
* math/float_exp_log - with default parameters take too much time to finish
* math/float_iperb - with default parameters take too much time to finish
* math/float_power - with default parameters take too much time to finish
* math/float_trigo - with default parameters take too much time to finish
* syscalls/fork13 - change value of -i from 1000000 to 10000 so it will take
  bearable amount of time to complete	
* sched/hackbench01 - reduce number of iterations to reduce run time. Reduce
  number of processes because ARC doesn't support more than 4096 processes at
  one time.
* sched/hackbench02 - ARC Linux has problems if amount of threads in one
  process is more that 380.


### Fatal tests

These tests cause system to crash or hung so they must be disabled.

* syscalls/sendfile04 - it is required to find why this tests causes system to hang
* syscalls/sendfile04_64 - it is required to find why this tests causes system to hang.


### Not applicable tests

These tests are not applicable to ARC Linux but are reported as failed, so they
should be disabled.

* fs/rwtest01 - this test requires BASH which isn't available
* fs/rwtest02 - this test requires BASH which isn't available
* fs/rwtest03 - this test requires BASH which isn't available
* fs/rwtest04 - this test requires BASH which isn't available
* fs/rwtest05 - this test requires BASH which isn't available
* fs/iogen01 - this test requires BASH which isn't available
* fs_bind/BindMounts - this test requires BASH which isn't available
* io/aio01 - asynchronous IO isn't supported by uClibc
* io/aio02 - asynchronous IO isn't supported by uClibc


## Notes

* sched/pth_str02 - This test will fail with current paramter "-n 1000" until
  STAR 9000579074 will be fixed
* fs/fs_racer* - These tests are written for bash which isn't available however
  they doesn't use syntax unsupported by BusyBox so they run nicely if `bash`
  is replaced with `sh`.

