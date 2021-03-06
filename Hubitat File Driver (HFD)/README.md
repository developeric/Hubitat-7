# Hubitat<small><sup>&reg;</sup></small> File Driver (HFD) [BETA]

## Introduction

This solution was developed to allow you to read/write/append files at the brand new (BETA) and powerfull local storage in the Hubitat Elevation<small><sup>&reg;</sup></small>  hub.

This solution intended to be a "provisory" one, while Hubitat moves ahead with the BETA and awards us with a full API to access files from apps and drivers.

**NOTE:** This solution itself is a BETA version - your input is expected and very welcome!


## HFD - "The Good, the Bad and the Ugly"

### Components

This solution has two components:

- A custom driver [Hubitat File Driver (HFD)](https://github.com/MAFFPT/Hubitat/blob/master/Hubitat%20File%20Driver%20(HFD)/driver/Hubitat%20File%20Driver%20(HFD).groovy)

- A model Rule Machine rule [HFD model rule](https://github.com/MAFFPT/Hubitat/tree/master/Hubitat%20File%20Driver%20(HFD)/rule)

### Installation

- **The custom driver above must be installed in your HE hub and used to create Virtual Devices (VD), one for each particular file you want to access**

  ### What? A Virtual Device for EACH file that I want to access?

  I am afraid this is **"The Ugly"** part: this is the way it works ... at least for now.

  I am aware that creating a VD for each file may be cumbersome. However, so far, it is the only way I have found to implement this solution. The problem that caused this is the fact that I could not find a way of referencing a file name as a variable, since a file reference is implemented as variable itself. Maybe there is, but unfortunatelly I could not find it.
  
  And there is more ...
  
  And **"The Ugly"** part goes on: this device must be created as a **child device** of the your app or driver, otherwise it will not be possible to use it.

- **A Rule Machine rule**

  This is, for sure, **"The Bad"** part: also specific for each file!
  
  And, please, do not shoot me! I have tried my best !!!
  
  So, you will need to create a rule for each file you want to access, using the model Rule Machine provided. I know ... this is really **"The Bad"**, don't you agree?
  
  When creating the rule you will need to replace the items indicated by the "clue" colored lines found all around the model rule image by the appropriate ones. The box at the upper right corner of the image tells you what you need to replace to do the necessary adjustments.
  
  **TIP**
  
  If you want to access more than one file, create the first rule, clone it and make the adjustments on the cloned rule. I am trying to help ... so, again, please do not shoot me! 
  
- **Suggested naming convention for drivers and rules**

  First of all, I must stress that this is only a suggested naming convention. You, obviously, can use the naming convention that best suits your needs or preferences.
  
  - Driver: **File Driver [** *filename* **]**
  
    Example: for the file **myLogFile**, the suggested driver name would be **File Driver [myLogFile]**
    
  - Rule: **File Driver [** *filename* **]**
  
    Example: for the same file, the suggested driver name would be **File Driver [myLogFile]**



## Using HFD

Finally, **"The Good"** part!

### Reading from a file

Call the **read** method of the specific VD created for the file
  
>
> *myLogFileDevice*.read ()
>
  
  where:
     
  - ***myLogFileDevice*** is the object that references the VD you have created 
  
Then, read the **fileContents** attribute of the device
  
>
> *myStringVariable* = *myLogFileDevice*.currentValue ("fileContents", true)
>
  
  where:
    
   - ***myStringVariable*** is the String variable to receive the contents of the fil
   - ***myLogFileDevice*** is the object that references the VD you have created
   - **"fileContents"** is the attribute where you will find the file contents (this attribute name is fixed)
   - **true** argument is to force the reading of the attribute skipping the cache, reading the last information from the database
     
**NOTE** 
   
 It has been observed some delay between the execution of the **read** command and the availability of the file contents at the **"fileContents"** attribute.
   
 To avoid data inconsistency, it has been added a delay (pause) at the end of the execution of the **write** and **append** commands to give time to the HE to update the attribute. This delay, expressed in miliseconds is stored, for now, in a internal driver constant:
   
 >
 > @Field static Long _afterCommandDelay = 250
 >
    
 If this value is deemed sufficient, it will stay that way. However, in case if you need to increase this value to fit your particular HE hub enviroment, please report it to me. If necessary, I can create a driver's preference variable to store that value and made easier to change it.

### Writing to a file

Call the **write** method of the specific VD created for the file
  
>
> *myLogFileDevice*.write (*content to be written*)
>
  
  where:
     
  - ***myLogFileDevice*** is the object that references the VD you have created
  - ***content to be written*** is - guess what ... - the string to be written to the file! It can be a literal string, a variable, a string value returned from a function, method, etc.
  
**NOTE**

  When ask for a write operation, two things can happen:
  
  - If the file does not exist yet, it will be created and the ***"content to be written"*** will be stored in it
  - If the file already exists, its contents will be **overwritten**, and the ***"content to be written"*** will be stored in it
  
  So, **be careful** when requesting a **write** operation!

### Appending to a file

Call the **append** method of the specific VD created for the file
  
>
> *myLogFileDevice*.append (*content to be appended*)
>
  
  where:
     
  - ***myLogFileDevice*** is the object that references the VD you have created
  - ***content to be appended*** is the string literal or string variable to be appended to the end of the file

## Final words

I hope that everything works as expected ... 
