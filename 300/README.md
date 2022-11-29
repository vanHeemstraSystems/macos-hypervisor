# 300 - Building Our Application

## 100 - MacOS Virtual Machine on MacOS
 
See [README.md](./100/README.md)
 
## 200 - Linux Virtual Machine on MacOS
 
See [README.md](./200/README.md) 
 
 Finally, we will turn our eyes onto Linux.
 Virtualization framework has supported Linux since the very beginning in macOS Big Sur.
 In macOS Ventura, we have added some pretty cool new features, and we want to share some of them with you.
First, we will see how we can install full Linux distributions, completely unmodified, in virtual machines.
 Then we will look at a new device we are adding to show UI from Linux.
 And finally, we will look at how we can take advantage of Rosetta 2 to run Linux binaries in virtual machines.
 Let’s start with installation.
 If we wanted to install Linux on a physical machine, we'd start by downloading an ISO file with the installer.
 Then we'd erase a flash drive with the ISO.
 And finally, we'd plug the drive in the computer and boot from it.
 When dealing with virtual machines, we will go through the same flow.
 But instead of using a physical USB drive, we will use a virtual one.
 Let’s see how it works.
 We start by creating an URL from the path to the ISO file we downloaded.
 Then we create a disk image attachment from the file.
 A disk image attachment represents a piece of storage that we can attach to a device.
 Next, we configure a virtual storage device.
 In this case, we want USB storage, so we use VZUSBMassStorageDeviceConfiguration.
 Finally, as always, we add our device in the main configuration.
 Here, the USB device appears next to another storage device, the main disk on which we will install Linux.
 Now we have a USB drive, but we need a way to boot from it.
In macOS Ventura, we have added support for EFI.
 EFI is an industry standard for booting both ARM and Intel hardware.
 We are bringing the same support to virtual machines.
 EFI has a boot discovery mechanism.
 What this will allow is discovering the installer on our USB drive.
 EFI looks at each drive for one that can be booted.
 It will find the installer and start from there.
 The installer itself will tell EFI what drive to use next.
 After the installation, EFI can then start the Linux distribution.
 Let’s see how to set up EFI in code.
 First, we create a boot loader of type VZEFIBootLoader.
 EFI requires non-volatile memory to store information between boots.
 This is called the EFI variable store.
 With virtual machines, we can back such storage by a file on the filesystem.
 Here, we create a new variable store from scratch.
 Now EFI is ready.
 We just need to set it as the boot loader on the configuration.
 Next, we will look into a new capability for Linux VMs, graphics.
 In macOS Ventura, we have added support for Virtio GPU 2D.
 Virtio GPU 2D is a paravirtualized device that allows Linux to provide surfaces to the host macOS.
 Linux renders the content, gives the rendered frame to Virtualization framework, which can then display it.
 You can now show this content in your app with VZVirtualMachineView just like on macOS.
 Let’s see how to set it up.
Setting up the device is similar to what we did for macOS.
 We start by creating a VZVirtioGraphicsDeviceConfiguration.
 We need to define the size of our virtual display.
 In Virtio terminology, a virtual display is a "scanout.
" So we create one scanout with the size of the display.
 Finally, we set the new device as the graphics device of our configuration.
 Now our VM is ready to display content with VZVirtualMachineView.
 Next, let’s see everything together in a demo.
 We start from where we left off.
 Let's delete the code that is specific to the Mac.
 Then let's change the disk we are booting from.
 We'll swap the path from our Mac drive to our Linux drive.
 Next, we need a boot loader.
 We set up EFI with VZEFIBootLoader.
We first create the EFI boot loader object.
 Then we load the variable store from its file.
 And finally, we set EFI as the boot loader on our configuration.
 Now we can boot, but it'd be nice to show the UI.
 Let's add Virtio GPU to our configuration.
 We simply create a graphics device of type VZVirtioGraphicsDeviceConfiguration.
 Then we define a scanout with the size of the virtual display.
 And we set the Virtio GPU as a graphics device on our configuration.
 The last touch is getting the mouse to work.
 We just use a virtual USB screen coordinate pointer device, and we'll have a mouse in Linux.
 That's it.
 We can run the project.
 EFI looks at the disk and finds it bootable.
 Then Linux shows the content of the UI through the Virtio GPU device.
 And we can use the mouse to interact with Linux.
 Last but not least, we'll see how we can take advantage of the Rosetta 2 technology inside Linux.
For many of us, we love developing services on our Mac, but once our work is ready, the binaries we create may need to run on x86 servers.
 x86 instruction emulation has been great for this, but we can do better.
 In macOS Ventura, we are bringing the power of Rosetta 2 to Linux binaries.
What Rosetta 2 does is translate the Linux x86-64 binaries inside your virtual machine.
 This means you can run your favorite ARM Linux distribution, and its x86-64 apps can run with Rosetta.
 And it's fast.
 It's the same technology we have been using on the Mac, which means we have incredible performance.
 Let’s see how to use it.
 First, we need to give Linux access to Rosetta.
 To do this, we use the same file sharing technology we have seen on macOS.
 Instead of sharing a folder, we use a special kind of object, a VZLinuxRosettaDirectoryShare.
 Then we create a sharing device and set up Rosetta directory share.
 Finally, we set up our device on the configuration as usual.
 Now our virtual machine is ready to use Rosetta.
 Next, let’s see how Linux can take advantage of it.
In Linux, we start by mounting the shared directory in the file system.
 What we see from Linux is a Rosetta binary that can translate applications.
 Then we can use update-binfmts to tell the system to use Rosetta to handle any x86-64 binary.
 Don’t worry about remembering this command.
 It's all in the documentation.
 Now Linux is ready.
 Every x86-64 binary launched will be translated by Rosetta.
Before we end our Linux section, let’s see everything together.
 Here, we have a full Linux distribution installed from scratch.
 We can show its UI with Virtio GPU 2D.
 From within the VM, we run a PHP server with Rosetta.
 And we can just connect to it from macOS host.
We've seen that creating virtual machines has never been simpler.
 With Virtualization framework, you can get virtual machines running with just a few lines of code.
 We have also seen that virtual machines are ridiculously fast on macOS.
 To learn more about Virtualization, I invite you to check out the code samples and documentation.
 And on behalf of the team, we cannot wait to see what you will do next with this technology.
