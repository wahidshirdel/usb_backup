#script for Linux that will detect a specific USB device when it's inserted and then copy a file to it.

#This script uses udev rules to detect the USB insertion and a simple Bash script to handle the file copying.

#1  Create a udev rule: This will detect when the specific USB device is inserted. You need to identify your USB device's vendor ID and product ID.

	#First, plug in your USB device and run the following command to get its vendor ID and product ID:

	$lsusb

	#Look for your device in the output, which will look something like this:

	"Bus 001 Device 004: ID 1234:5678 Your_USB_Device_Name"

	#In this example, 1234 is the vendor ID and 5678 is the product ID.

#2	Create the udev rule file

    #Create a file named 99-usbcopy.rules in the /etc/udev/rules.d/ directory:

	$sudo nano '/etc/udev/rules.d/99-usbcopy.rules'

	#Add the following line to this file, replacing 1234 and 5678 with your USB device's vendor ID and product ID:

	ACTION=="add", SUBSYSTEM=="usb", ATTR{idVendor}=="1234", ATTR{idProduct}=="5678", RUN+="/usr/local/bin/usbcopy.sh"

#3	Create the usbcopy.sh script:

	#Create a script named 'usbcopy.sh' in the '/usr/local/bin/' directory:

	$sudo nano /usr/local/bin/usbcopy.sh

	#Add the following content to this script:

	

-----------------------------------------------------------------------------------------------------------------

#!/bin/bash



# Wait for the device to be fully registered

sleep 5



# Define the source file and the destination mount point

SOURCE_FILE="/path/to/your/file"

MOUNT_POINT="/mnt/usb"



# Create the mount point directory if it doesn't exist

mkdir -p $MOUNT_POINT



# Find the device name

DEV_NAME=$(lsblk -o NAME,VENDOR,MODEL | grep -B 1 'Your_USB_Device_Name' | head -n 1 | awk '{print $1}')



if [ -n "$DEV_NAME" ]; then

    # Mount the USB device

    mount /dev/$DEV_NAME $MOUNT_POINT



    # Copy the file

    cp $SOURCE_FILE $MOUNT_POINT



    # Unmount the USB device

    umount $MOUNT_POINT



    # Optionally, remove the mount point directory

    rmdir $MOUNT_POINT

else

    echo "USB device not found"

fi

-----------------------------------------------------------------------------------------------------------------

	#Replace '/path/to/your/file' with the path to the file you want to copy and "Your_USB_Device_Name" with the name of your USB device as shown by 'lsblk'.

#4	Make the script executable:

	$sudo chmod +x /usr/local/bin/usbcopy.sh

#5	#Reload udev rules:

	$sudo udevadm control --reload-rules

################################the example that i use is like below ###########################################################

-----------------------------------------------------------------------------------------------------------------

#!/bin/bash



# Wait for the device to be fully registered

sleep 5



# Define the source file and the destination mount point

SOURCE_FILE="/home/wahid/projectNotes"

MOUNT_POINT="/mnt/usbinserted"

log="/home/wahid/Desktop/loggerfile.txt"



#create a log file

touch $log

echo "my message">$log



# Create the mount point directory if it doesn't exist

mkdir -p $MOUNT_POINT

echo "file $MOUNT_POINT make ">>$log 



# Find the device name

DEV_NAME=$(lsblk -o NAME,VENDOR,MODEL | grep 'sdb' | tail -n 1)

echo "we are here and dev name is $DEV_NAME is ready ">>$log



if [ -n "$DEV_NAME" ]; then

    # Mount the USB device

	echo "successfully interd ">>$log

    mount /dev/$DEV_NAME $MOUNT_POINT



    # Copy the file

    cp $SOURCE_FILE $MOUNT_POINT

	

	echo "this file is writeed">>$MOUNT_POINT/test.txt



    # Unmount the USB device

    umount $MOUNT_POINT



    # Optionally, remove the mount point directory

    rmdir $MOUNT_POINT

else

    echo "USB device not found">>log

fi

-----------------------------------------------------------------------------------------------------------------

