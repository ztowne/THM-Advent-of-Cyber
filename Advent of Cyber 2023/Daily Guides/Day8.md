# Day 8 
## Disk Forensics

Today, we will be using FTK Imager to do some disk forensics on a USB drive. Our learning objectives are:

- Analyze digital artifacts and evidence
- Recover deleted digital artifacts and evidence
- Verify the integrity of a drive/image used as evidence

## FTK Imager

FTK Imager is a forensics tool that allows specialists to acquire computer data and perform analysis without affecting the orignal evidence. 

First, we will select the USB drive in FTK Imager by navigating to ```File > Add Evidence Item``` and selecting ```Physical Drive``` from to pop-up windiow. We can then see the Evidence Tree pane is populated with our drive and its contents:

![Alt text](/Advent%20of%20Cyber%202023/Resources/FTKimager.png)

### Previewing Modes

There are 3 different previewing modes in FTK Imager - automatic, text, or hex. No matter what mode we are in, we can always use ```CTRL + F``` to search through the preview of the file.

### Recovering Deleted Files and Folders

To view and recover deleted files, we can right click and select ```Export Files``` on any files marked with an X.

![Alt text](/Advent%20of%20Cyber%202023/Resources/deletedfilesftk.png)

### Verifying Drive/Image Intergrity

To verify the integrity of a drive, we can grab the MD5 and SHA1 hashes by going to ```File > Verify Drive/Image```

## Practical Exercise with FTK Imager

We need to use what we learned today to answer the following questions:

1. What is the malware C2 server?

A: We can find the malware C2 server in .txt file called ```secretchat.txt``` located in the ```DO_NOT_OPEN``` folder we recovered. The server is located at ```mcgreedysecretc2.thm```.

2. What is the file inside the deleted zip archive?

A: In the same folder as the previous question that we recovered, we can find a ZIP file with a .exe inside of it named ```JuicyTomaTOY.exe```.

3. What flag is hidden in one of the deleted PNG files?

A: Opening up the ```portrait.png``` file in HEX mode, we can search using ```CTRL + F``` to search for ```THM{``` inside the contents of that file. Here we will find the flag ```THM{byt3-L3vel_@n4Lys15}```.

4. What is the SHA1 hash of the physical drive and forensic image?

A: We can simply open up the navigate to ```File > Verify the Integrity of Disk/Image``` to recieve the SHA1 hash of ```39f2dea6ffb43bf80d80f19d122076b3682773c2```

## Conclusion

Today we dove a little into digital forensics and got some very interesting hands on experience with ```FTK Imager```.