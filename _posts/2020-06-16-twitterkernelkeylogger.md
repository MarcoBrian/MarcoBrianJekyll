---
title: "Twitter Bot - Kernel Keylogger"
date: 2020-06-16
tags: [spyware, Computer Security, Linux, Keylogger]
excerpt: "A spyware (keylogger) that runs on Linux operating system kernel and communicates using Twitter as a covert channel"
toc: true
toc_label: "Contents"
toc_sticky: false
---

# Introduction

[Github repository](https://github.com/MarcoBrian/TwitterBotKernelKeylogger)

Okay, I know thats a bad name for a project but I have something interesting to share with you today. I'm very excited to announce that i've have created a twitter bot which posts memes automatically. 

Okay what? Yep you heard that right. memes. 

Alright you might think “whats so cool about this i can do this stuff in 10 mins.. boo 😒👎”

What's different here is that these memes contains all your <b> credit card number, ccv, email, passwords , </b> etc. Interested? Continue reading if you want to know how this was done. <b> ALERT </b> : a lot of technical stuff ahead, Im assuming you know at least some basic understanding of operating systems, some programming and know what an API is before moving on. But at the same time i am trying to explain implentation concepts from a high level or else it might be a long read.

A bit of backstory: 

This project started because i was taking a computer security course at UC Davis and we were free to do anything for the final project. So well i thought why not make a malware while we get the permission to do it. But of course we are just gonna use it for educational purposes only ;) wink wink. ( jk but seriously though using tech for malpractice aint cool )

Over the past years studying CS i’ve always been building things for the good/benefit of others. But it was really interesting that now i get to put my shoes in the mind of an attacker and to wreak as much havoc as I can. 

## The Kernel Keylogger



So lets start with what we built. At the very core we made a kernel based keylogger which attacks mainly Linux OS. Our keylogger runs in the kernel level and not in user space, reason being is that it’s much more difficult to detect the keylogger that is running right inside the operating system. Anti virus software mainly runs on user space and these software will have no idea of our program running in the kernel.



The way we get it to run in the kernel is that we implemented the keylogger as a kernel module. 



There was a lot of research done during this step, this was probably the most difficult part of the project. There was a lot of trial and error and trying out multiple methods because there was just so many ways you can implement the keylogger. There are multiple different kernel functions you can hijack but some are platform dependent and even CPU dependent. But finally we found a way to collect keystrokes by registering a custom callback function into the keyboard event handler. 



The custom callback function allows us to read through scan codes (these are low level codes to determine which key was pressed) when a key was pressed. Once we had this figured out we are basically done with the keylogging aspect. For every key that was pressed we registered the scan codes,convert them into the corresponding char values and place them into a buffer which will eventually be placed onto the proc filesystem so that we can access them on user space as a text file.






## Text Analysis



So we will leave the keylogger running in the background while the person using the computer casually browses the internet. Maybe buying something online, logging into their emails, opening social media accounts, etc. Whatever activity they are doing on the keyboard, we record them. But now comes the time we extract those information. Most of the time the user will be typing on some nonsense, we dont really want to know what the person watches in his free time you know (unless you’re into that kinda stuff but we can talk about it some other time ok?). 



For now we want his passwords, his emails , his credit card number and sensitive personal information. So we created an additional program that will be able to detect these patterns from the keylogger text file and output them into a new text file which contains only the sensitive information. These are the information which we will eventually retrieve.







## Twitter Bot , Encryption & Stenography



Our plan is to use twitter as a covert channel between the victim and the attacker. Using social media like twitter is a great way for the attacker to remain anonymous. We encrypted the keylog text files using AES-128 bit encryption and then afterwards using Stenography to hide the encrypted text into photos that are obtained from a public API. We used the LSB Stenography method to hide the encrypted text into the image. 



Afterwards this image will be posted a random tweet on a twitter bot profile. As long as the attacker knows the profile he will be able to download this image and get back the text with the correct decryption key.





## Phishing Application

So in order to have this keylogger installed we actually need superuser access (installing a kernel module is a sudo operation) which means we need to know the password for the computer. So some of you be raising some eye brows alread. we need password -> to install keylogger -> to get password -> ????  How do we even do that. We see some cyclic dependency here and it’s not a good thing. The answer to that is that we need to trick the user somehow to give in to their password. 

So we have to create a phishing application, this phishing part could really be anything really. This is the time to be creative, the better the GUI the better. We can implement a food tracker app, contacts app, calculator app, whatever kind of app. The idea here is to make the user download this application for the purposes stated (ex for calorie tracking or calculator) and make the application actually functional and make them comfortable with the UX. At the right timing say when user makes changes to the settings in the app or even during installation we can eventually make a pop up dialog box which says:

“Would you allow ‘app-name’ to make changes ?” 

“Enter password to confirm:”

Some might fall for it and eventually enter the password. If the user enters the password then using the phising application we record that password and in the background run a bash script (hidden in the phishing application) which downloads the actual keylogger malware from a remote server. Once the files are downloaded (but not yet installed), using our newly discovered password from the phishing application we can gain access to sudo and finally install the keylogger and run the additional scripts.

In our implementation we just downloaded the files from our github repo but a real life attacker would try to set up an anonymous server and download the files from there to keep a low profile. Also we didnt really implement a GUI because of time constraints and lets be honest i am not a GUI master. 

## Conclusion

Thats all the main parts to this malware and I hope you enjoyed reading. But most importantly hoped that you learned something new today. I would like to end this article with a cliche quote from the book Art Of War-Sun Tzu but it makes a lot of sense in the context of computer security.

<b> “If you know the enemy and know yourself, you need not fear the result of a hundred battles. If you know yourself but not the enemy, for every victory gained you will also suffer a defeat. If you know neither the enemy nor yourself, you will succumb in every battle.” </b>

### Contributors 

- [Hiroya Gojo](https://github.com/HiDoYa)
- [Marco Brian](https://github.com/MarcoBrian)
- [Nan Chen](https://github.com/qianlan97)
- [Shuyao Li](https://github.com/DrunkLea)
