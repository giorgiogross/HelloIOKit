# HelloIOKit
Sample driver for OS X using Apple's I/O Kit

You can find the original tutorial here:
https://developer.apple.com/library/content/documentation/Darwin/Conceptual/KEXTConcept/KEXTConceptIOKit/iokit_tutorial.html#//apple_ref/doc/uid/20002366

Other useful sources:
https://developer.apple.com/library/content/referencelibrary/GettingStarted/GS_HardwareDrivers/_index.html
https://developer.apple.com/library/content/documentation/DeviceDrivers/Conceptual/WritingDeviceDriver/DebuggingDrivers/DebuggingDrivers.html


## Notes I want to point out
Your info.plist file needs to have an unique identifyer for CFBundleIdentifier which is the reverse DNS of your company, so e.g. com.mycompany.driver.MyDriver
Personalities describe the type of devices the driver can support. For this driver, include
* CFBundleIdentifier property as just as above
* IOClass as com_MyCompany_driver_MyDriver which matches the CFBundleIdentifier but has _ instead of .
* IOKitDebug If you specify a nonzero value for this property, your driver provides useful debugging information when it matches and loads. When you build your driver for public release, you should specify 0 as the value for this property or remove it entirely.
* IOProviderClass indicates the class of the provider objects that your driver can match on. Normally a device driver matches on the nub that controls the port that your device is connected to. For example, if your driver connects to a PCI bus, you should specify IOPCIDevice as your driver's provider class. In this tutorial, you are creating a virtual driver with no device, so it matches on IOResources.
* IOMatchCathegory allows other drivers to match on the same device as your driver, as long as the drivers’ values for this property differ. As this driver matches on IOResources, a special provider class that provides system-wide resources, it needs to include this property to allow other drivers to match on IOResources as well. When you develop your driver, you should not include this property unless your driver matches on a device that another driver may match on, such as a serial port with multiple devices attached to it.<br><br>

Because kexts are linked at load time, a kext must list its libraries in its information property list with the OSBundleLibraries property. To add all library declarations to the info.plist file you can
* Build the project
* <code>kextlib -xml MyDriver.kext</code>
* Copy the output to the dict of your info.plist file

To make this driver run/loadable you will need to disable the System Integity Protection on your mac. To do so boot in recovery mode and execute <code>csrutil disable</code>. To reanable it again run <code>csrutil enable</code>. (Also useful for this topic: https://apple.stackexchange.com/questions/208478/how-do-i-disable-system-integrity-protection-sip-aka-rootless-on-os-x-10-11)
Your driver needs the root:wheel permission set. To achieve this simply copy the driver to your tmp folder with <code>sudo cp -R MyDriver.kext /tmp</code>.
To see information about your driver use <code>kexturil -n -t /tmp/MyDriver.kext</code>.
To load or unload your driver use <code>kextload</code> or <code>kextunload</code>, respectively, and pass your driver as Argument. Monitoring the logs can be done by executing <code>dmesg</code>.
