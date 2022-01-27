# CTF Level 2
The 2nd level of the CTF can be unlocked by running the oracle executable with the flag we received from level 1. Additionally, the executable tells us that we _-h_ is a flag that can be used to show a hint, which is "combine the parts".

The command unlocks 1 new file.
By using the _file_ utility we can see that the new file is an ELF executable.

If we try running the ELF, a hexadecimal number is printed to the standard output. When using the command _echo $?_ a 0 is printed, meaning the file is exiting with no errors.

Additionally, adding a command-line parameter does not change the functionality, and different numbers are printed, suggesting a use of the libc function _rand_.

And as expected, the output of the _ltrace_ utility shows that the libc functions: _time_, _srand_, _rand_ & _puts_ are all used.

The _strings_ & _strace_ utilities offer no new useful information.

The disassembly of the executable shows that the random number generated by _rand_ is manipulated and then used as an offset to an address whose content will be passed on to _puts_.

Since rand creates a random number each iteration we'll have to analyse the executable dynamically.

We'll set a breakpoint at the call to _puts_.
In the current iteration, the value of rdi is 0x4006d9.

_readelf --sections --wide_ shows us that the .rodata section starts at address 0x4006c0 and the next section starts at 0x4006f4, meaning that the value that rdi points to is in the .rodata section.

We can dump the contents of the section using objdump. In the output are values, some of which we can recognize from running the executable.

With the hint we received at the start, we can infer that the values in the .rodat sections can be combined into a flag.

...And tadah! The combined numbers are accepted as the flag: 034fc4f6a536f2bf74f8d6d3816cdf88.