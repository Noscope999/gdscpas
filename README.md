# GDSC Pack & Ship Reviewal

The GDSC "Pack and Ship" challenge is a reverse engineering task where we analyze a packed binary to extract a hidden flag. The binary is intentionally obfuscated to make direct analysis difficult, requiring us to unpack and inspect its behavior before bypassing security checks.

# Tools Used

To analyze and modify the binary, the following tools were used :


• strings - Used to extract readable text from a binary file.

• UPX - Executable packer/unpacker used to operate on the packing of files.

• radare2 - Reverse engineering framework used to modifying executables.

# Step 1 - Extracting Strings From Binary

Using the strings command, we obtained all the strings present within the binary file.

```
strings release
```

The obtained strings can be seen [here](https://github.com/Noscope999/gdscpas/blob/main/strings%20release). Although we didn't directly chance upon a password, we can see that the binary file is packed using the UPX executable packer, which gives us some direction for our next step.

# Step 2 - Unpacking The UPX 

UPX (Ultimate Packer for Executables) is a common executable packer used to compress binary files, making them smaller and sometimes harder to analyze, and we can use the upx tool to unpack it. 

```
upx -d release
```

-d stands for decompress here, directing UPX to attempt to restore the original binary file. 

![image](https://github.com/user-attachments/assets/8d885faa-7655-4ff0-a1d0-5788e365d97d)

# Step 3 - Changing File Permissions

After unpacking the binary, we need to ensure we have admin permissions to run and analyze it properly. In Unix-based systems like Linux, this is done using the chmod command.

```
chmod +x release
```

+x adds execute permissions to the file, allowing it to be run as a program.

# Step 4 - Analyzing the Binary with Radare2

We use the below command to open the file in Radare2 :

```
r2 -aa release
```

Where - aa enables automatic analysis.

Now that we've fed Radare2 the file, we can use the afl command to get a list of all detected functions. 

```
afl
```

![image](https://github.com/user-attachments/assets/5a67186a-8d1e-4dca-b500-2fd208240ed3)

Now that we can see the functions present, we identify the main function, and using pdf (print disassembled function) we can see the assembly code of main (shown below).

```
pdf @ main
```

![image](https://github.com/user-attachments/assets/dcbe8978-dc64-4b1b-92a0-50acf77e2415)
![image](https://github.com/user-attachments/assets/d0b80cd4-2314-4fd8-9a2b-5ee2f703bb04)
![image](https://github.com/user-attachments/assets/d7eba6c4-b7e6-4d17-9886-ea1c87eff240)

# Step 5 - Identifying Jump Conditions

Now that we've obtained the main function, we can check where the obfuscation takes place, by checking at which lines (correspondent to the incorrect password line) get jumped from. We can see that the memory addresses of the correspondent lines from which the jump condition takes place are 0x000013b7 and 0x000013d8 respectively.

![image](https://github.com/user-attachments/assets/532d5f32-0b19-439c-839d-45dbdcf6f8aa)

Now that we've identifed the jump conditions, we can go ahead and remove them - allowing the programs execution to redirect us to the flag we require. Firstly, we open the file in writing mode by using : 

```
r2 -w release
```

After doing this, we then go ahead perform :

```
s 0x000013b7
wa nop
```
```
s 0x000013d8
wa nop
```
s being seek, seeking to that memory address - and nop (no operation) essentially overriding that lines execution.

# Step 6 - Running The Modified File

Before finalizing, we should verify that our modifications did not break the binary. We can do this by running :

```
r2 -AA release
pdf @ main
```

This ensures that the NOP instructions are still in place and no unintended modifications occurred. If they look as expected, then we can go ahead and run the binary file -

```
./release
```

By running the above command, we get the following, finally granting the required flag :

![image](https://github.com/user-attachments/assets/04b9ffa5-7b53-42f2-9c6a-751441a4eb99)

Flag Being : `gdsc{unp4ck1n6_b1n4r135_15_n4u6h7y}`
