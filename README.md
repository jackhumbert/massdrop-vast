# Massdrop Vast 35-inch Curved Gaming Monitor

[The Vast](https://drop.com/buy/massdrop-vast-curved-gaming-monitor/) is an ultrawide monitor sold by Massdrop/Drop. The EDID (hardware information about the monitor) automatically assigned to the monitor is incorrect - it's not clear whether it's due to the manufacturer, or OS making assumptions about it. Below are instructions for the process I used, which should apply to any monitor.

If you'd like to just fix your Vast, you can use the files in this repository:
* [EDID.reg](/EDID.reg) - Windows Registry file to override the EDID in place for the monitor
* [EDID.bin](/EDID.bin) - Binary file of the EDID (for other operating systems)

## Generic EDID Replacement Instructions

The monitor I'm using has the incorrect "Image Size" values in the "Detailed Timing > Timing" section of the EDID (I'm not sure these names are accurate, but this is what the editing software calls them, so I'll be using them below).

Before doing any of this, I would make sure your manufacturer hasn't already supplied a driver that will update these to be the correct values - there is some risk to messing up your EDID (although there is a checksum, so I'm not sure how messed up it could get).

**I don't know of the risk involved in changing values in your EDID, but I wouldn't be surprised if some damage could occur with the wrong ones, so please proceed with caution, and if you have any questions, please don't be afraid to ask. Please note that all code blocks require customization - nothing here can be copy & pasted. I'm also not an expert in any of this, and have only figured this out today.**

### Obtaining the current EDID

The EDID key is located here (<some_id> and <other_ids> will be specific to your monitor) - you can view this in regedit.exe:

    HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Enum\DISPLAY\<some_id>\<other_ids>\Device Parameters
    
If it's not obvious which id is the monitor you want to modify, you can go to Settings > Display, and selected the monitor you're working with, then click Advanced Display Settings > Display adapter properties for <monitor>. In that new window, go to the Monitor tab > Monitor Type > Properties button, then Details tab > Property: "Device instance path", and you should see something like "DISPLAY\ICB3500\5&44F2E74&0&UID4355", which is the location in the registry that you need.

To get this into a text file, we need to export it, and can do so by right-clicking on the "Device Parameters" folder - save this as "edid.reg" somewhere where you'll be able to open it to edit it. 

We now need to prepare the EDID for editing - if you understand what's going on, feel free to convert this binary however you want (there might be programs that do this as well), but since it's just text, it's pretty easy.

You'll need some sort of text editor to open the edid.reg file - be sure to right-click on the file and click "Edit" instead of double-clicking - opening it should look something like this:

    Windows Registry Editor Version 5.00
    
    [HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Enum\DISPLAY\ICB3500\5&44f2e74&0&UID4355\Device Parameters]
    "EDID"=hex:00,ff,ff,ff,ff,ff,ff,00,24,62,00,35,00,00,00,00,28,1b,01,04,b5,52,\
      23,78,3f,76,90,a8,54,4d,9f,25,0e,50,54,bf,ef,80,d1,c0,81,c0,81,00,81,80,a9,\
      c0,b3,00,b3,28,21,e8,3e,d0,70,a0,d0,a0,29,50,30,20,3a,00,33,5a,31,00,00,1a,\
      e7,7c,70,a0,d0,a0,29,50,30,20,3a,00,33,5a,31,00,00,1a,00,00,00,fd,00,31,7d,\
      a0,a0,41,01,0a,20,20,20,20,20,20,00,00,00,fc,00,4d,61,73,73,64,72,6f,70,5f,\
      56,61,73,74,00,7b

    [HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Enum\DISPLAY\ICB3500\5&44f2e74&0&UID4355\Device Parameters\WDF]

Open up a new text file, and copy in the values after "hex:" all the way to the end of the key (7b in the case above). That should look something like this:

    00,ff,ff,ff,ff,ff,ff,00,24,62,00,35,00,00,00,00,28,1b,01,04,b5,52,\
      23,78,3f,76,90,a8,54,4d,9f,25,0e,50,54,bf,ef,80,d1,c0,81,c0,81,00,81,80,a9,\
      c0,b3,00,b3,28,21,e8,3e,d0,70,a0,d0,a0,29,50,30,20,3a,00,33,5a,31,00,00,1a,\
      e7,7c,70,a0,d0,a0,29,50,30,20,3a,00,33,5a,31,00,00,1a,00,00,00,fd,00,31,7d,\
      a0,a0,41,01,0a,20,20,20,20,20,20,00,00,00,fc,00,4d,61,73,73,64,72,6f,70,5f,\
      56,61,73,74,00,7b
  
We also need to remove the "\" characters (the spaces and newlines are ok, though) - doing so will look like this:

    00,ff,ff,ff,ff,ff,ff,00,24,62,00,35,00,00,00,00,28,1b,01,04,b5,52,
      23,78,3f,76,90,a8,54,4d,9f,25,0e,50,54,bf,ef,80,d1,c0,81,c0,81,00,81,80,a9,
      c0,b3,00,b3,28,21,e8,3e,d0,70,a0,d0,a0,29,50,30,20,3a,00,33,5a,31,00,00,1a,
      e7,7c,70,a0,d0,a0,29,50,30,20,3a,00,33,5a,31,00,00,1a,00,00,00,fd,00,31,7d,
      a0,a0,41,01,0a,20,20,20,20,20,20,00,00,00,fc,00,4d,61,73,73,64,72,6f,70,5f,
      56,61,73,74,00,7b
  
We can now save this as "edid.txt" or something similar, and it's ready to be imported into the editor.

### Modifying the EDID

The program I used is called EEditGold, is free, and can be downloaded here:

[https://ez.analog.com/video/w/documents/750/advantiv-edid-editor](https://ez.analog.com/video/w/documents/750/advantiv-edid-editor)

Once you have that installed, open the program, select File > Import > Text, then click "Select" and navigate to the location of the "edid.txt" file we just created. Open this, and click "Import".

From here on, it will really depend on the current status of the EDID file. The two spots I needed to edit were under EDID > VESA > Db1 and Db2 (navigated to on the left side). In both of these sections, the Detailed Timing > Timing > "Image Size (mm)" were incorrect. I measured both the width and height of my monitor in millimeters, and entered the values here (in both Db1 and Db2). For what it's worth, my EDID > VESA > Basic entry show the Screen Size > Image Size values correctly (note that these are in centimeters), but these must not be used/reported. I also updated the "Product Name" that was under Db4, because mine was generic and unhelpful. I wouldn't go around changing too many values not outlined here, or at the very least, just change one at a time, and be prepared for things to break.

Once you're happy with your changes, hit File > Export > Text, and click "Select" - name it "edit-fixed.txt" and hit "Save" in the file selector, then "Save" in the next window.

Now, with your edid.reg file still open, save it as "edit-fixed.reg" before you start making changes to avoid editing your original edid.reg file (in case something goes wrong). Open your edid-fixed.txt, and copy that entire file into edit-fixed.reg, after "hex:", completely replacing what was there previously. It should look something like this:

    Windows Registry Editor Version 5.00

    [HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Enum\DISPLAY\ICB3500\5&44f2e74&0&UID4355\Device Parameters]
        "EDID"=hex:00,FF,FF,FF,FF,FF,FF,00,24,62,00,35,00,00,00,00,28,1B,01,04,B5,52,23,78,3F,76,90,A8,54,4D,9F,25,0E,50,54,BF,EF,80,D1,C0,81,C0,81,00,81,80,A9,C0,B3,00,B3,28,21,E8,3E,D0,70,A0,D0,A0,29,50,30,20,3A,00,33,5A,31,00,00,1A,E7,7C,70,A0,D0,A0,29,50,30,20,3A,00,33,5A,31,00,00,1A,00,00,00,FD,00,31,7D,A0,A0,41,01,0A,20,20,20,20,20,20,00,00,00,FC,00,4D,61,73,73,64,72,6F,70,5F,56,61,73,74,00,7B

    [HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Enum\DISPLAY\ICB3500\5&44f2e74&0&UID4355\Device Parameters\WDF]
    
Now, we need to modify the url of the key, adding a "\EDID_OVERRIDE", and renaming the key from "EDID" to "0" - this will allow us to run/merge in the .reg file. We'll also remove the WDF reference at the end of the file. Doing so should look like this:

    Windows Registry Editor Version 5.00

    [HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Enum\DISPLAY\ICB3500\5&44f2e74&0&UID4355\Device Parameters\EDID_OVERRIDE]
    "0"=hex:00,FF,FF,FF,FF,FF,FF,00,24,62,00,35,00,00,00,00,28,1B,01,04,B5,52,23,78,3F,76,90,A8,54,4D,9F,25,0E,50,54,BF,EF,80,D1,C0,81,C0,81,00,81,80,A9,C0,B3,00,B3,28,21,E8,3E,D0,70,A0,D0,A0,29,50,30,20,3A,00,33,5A,31,00,00,1A,E7,7C,70,A0,D0,A0,29,50,30,20,3A,00,33,5A,31,00,00,1A,00,00,00,FD,00,31,7D,A0,A0,41,01,0A,20,20,20,20,20,20,00,00,00,FC,00,4D,61,73,73,64,72,6F,70,5F,56,61,73,74,00,7B

You can now save your edid-fixed.reg file.

### Finishing things up and applying the change

Before you run/merge the edid-fixed.reg file, be sure your edid.reg file still exists and has your old EDID information. You may need this if something goes wonky. In the case that it does, be prepared to have another monitor to use to restore your EDID. To do that, merge your original edid.reg file (just to be safe), and delete the "0" key from that folder in your registry location. Then, restarting your computer should get things back to normal. Please note that I haven't had this happen to me, so these restoration directions are purely speculative.

If you're comfortable, right-click > "Merge" on your new edid-fixed.reg file, and restart your computer. You might also be able to press Ctrl+Win+Shift+B, to restart your graphics driver, but I wasn't able to test this before making the change.

Documents should now be correctly sized at 100%/Actual Size on the monitor you configured (as long as it's your main monitor, maybe? I haven't tested this extensively yet).
