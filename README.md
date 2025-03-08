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

The obtained strings can be seen [here](url). Although we didn't directly chance upon a password, we can see that the binary file is packed using the UPX executable packer.

