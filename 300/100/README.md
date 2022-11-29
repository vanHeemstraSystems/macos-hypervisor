# 100 - MacOS Virtual Machine on MacOS
 
Based on "Create macOS or Linux virtual machines" at https://developer.apple.com/videos/play/wwdc2022/10002/ 

Documentation at https://developer.apple.com/documentation/virtualization
 
 We'll see how we can set up a virtual Mac and install macOS into it.
 And finally, we'll do a second deep dive, this time into Linux.
 We'll see how to run full Linux distributions and some cool new features.
 Let’s get started with the overview.
 We'll first look into the stack that enables virtualization.
 It all starts with hardware.
 Apple silicon has special hardware that enables the virtualization of CPUs and memory.
 This means you can run multiple operating systems on top of a single SoC.
 Next, we need software to take advantage of this hardware.
 And this is built right into the macOS kernel.
 You no longer need to write kernel extensions, or KEXTs.
 It's all built in.
 To use those capabilities from your application, you can use Hypervisor framework.
 Hypervisor framework is a low-level API that lets you virtualize CPUs and memory.
 But, because it's a low-level framework, you need to write every single detail of the virtual environment.
 Oftentimes, we want to run full operating systems.
 For this, there is a higher-level API, which is Virtualization framework.
 Virtualization framework enables the creation of virtual machines running macOS on Apple silicon or Linux on both Apple silicon and Intel.
 Today, our session will focus on Virtualization framework.
 When using Virtualization framework, we'll deal with two kinds of objects.
 The first kind are configuration objects.
 They define all the properties of our virtual machines.
 The second kind are virtual machine objects.
 Those objects abstract virtual machines and how to interact with them.
 We'll start with looking at the configuration.
 The configuration defines the hardware.
 Creating a configuration is like configuring a Mac on the Apple Store.
 We define how many CPUs we want, how much memory, what kind of devices.
 We can start from a simple configuration.
 We can add a display, and we get to see the content.
 We can add a keyboard, and we can type.
 We can add a trackpad, and we can interact with the UI.
 Configuring a virtual machine is just like that.
 But since we are dealing with virtual machines, we'll do this in code.
 Let’s see how we can write the configuration in Swift.
 Defining the hardware is very simple.
 We start with an object of type VZVirtualMachineConfiguration.
 This is the root object of all configurations.
 Next, we define how many CPUs our machine should have.
 Here we give four CPUs.
 Then, we set how much memory we want.
 In this case, we give four gigabytes of memory.
 Finally, we define the devices our machine will have.
 In this example, we set a single storage device, the disk to boot from, and a pointing device, like a mouse.
 There are many devices available.
 The ones you set up depend on the problem you want to solve.
 Now we've seen the configuration.
 It starts with VZVirtualMachineConfiguration, on which we add the CPUs, the memory, and the devices.
 Next, we'll look into the virtual machine objects.
After we have configured our Mac, we get it by the mail.
 It's time to unbox it and start it.
 But since we are dealing with virtual machine, we need to do that in code.
 Let’s see how we can do it in Swift.
 First, we'll create an instance of VZVirtualMachine from our configuration.
 A VZVirtualMachine abstracts an instance of the virtual hardware.
 Now that we have the virtual machine, we can operate on it.
 For example, in this case, we call start() to start it.
 We'll often want to interact with our virtual machines.
 For this, we have other objects to help us.
 For example, if we want to show our virtual display, we can use an object of type VZVirtualMachineView.
 We start by creating a view.
 Then we set our virtual machine as the virtualMachine property on the view, and it's ready.
 Now we can use this VZVirtualMachineView object like any NSView.
 We can integrate it in our app to see the content of the virtual machine.
 
### To wrap up, we've seen the configuration.

 The configuration starts with VZVirtualMachineConfiguration, from which we define the CPUs, memory, and our devices.
 From the configuration, we will create a virtual machine, and we will use virtual machine objects.
 We've seen VZVirtualMachine to abstract the VM itself, VZVirtualMachineView to display content, and there are other objects that can help us use the VM.
 We have seen that the configuration gives a lot of flexibility in how we define virtual machines.
 Unfortunately, there are too many features to cover in one session.
 In this session, we will look into some of the core capabilities.
 For everything else, we have documentation, and I invite you to check it out.
 In the overview, we just saw how to build virtual machines.
 Now it is time to look into how we can run a full operating system in them.
 And we will start with macOS.
 Virtualization framework supports macOS on Apple silicon.
 When we built Vvirtualization framework on Apple silicon, we've developed macOS and Virtualization framework together.
 What this gives us is incredible efficiency when running macOS inside virtual machines.
 Here is what we are going to see: First, we will look into what we need to turn a virtual machine into a virtual Mac.
 Then we'll look into the steps to install macOS on our virtual Mac.
 Next, we'll see some of the special devices we have for macOS.
 And finally, we will look into a very important use case, which is sharing files between the host system and the virtual Mac.
Let’s start with the configuration.
 We have seen before how to build a generic virtual machine.
 Now we want to add the special properties that will make a virtual machine a Mac.
 So how do we make a virtual Mac? First, we will define a special platform.
 A platform is an object that holds all the properties of a particular type of virtual machine.
 There are three properties that are unique to the virtual Mac hardware.
 First, we have the hardware model.
 The hardware model specifies which version of the virtual Mac we want.
 Second, there is the auxiliary storage.
 The auxiliary storage is a form of non-volatile memory used by the system.
 And third, there is the machine identifier.
 The machine identifier is a unique number representing the machine, just like a physical Mac has a unique serial number.
 Once we have the platform, we have all the pieces to describe the hardware, but we need one more piece, which is a way to boot macOS.
 For this, we will use a special boot loader, the macOS boot loader.
 Let’s see how to do all of this in Swift.
 We start from the same base as before.
 This code is what we have seen in the overview.
 Then we create a VZMacPlatformConfiguration.
 This is our platform object for virtual Macs.
 We need a hardware model for this Mac.
 Here we use one we previously saved.
 In virtual machines, the auxiliary storage is backed by a file on the local filesystem.
 Here, we initialize our auxiliary storage from a file URL.
 For the unique identifier, we initialize a VZMacMachineIdentifier from one we previously saved.
 For a new install, we can also create a new identifier.
 We have set all three properties.
 Our platform is ready.
 All we have to do is set it on the configuration object.
 This gives us the hardware.
 Next we need a way to boot it.
To do that, we set up the boot loader with VZMacBootLoader.
 Now our machine is ready to boot.
 What we have done so far is define the virtual Mac and how to start it.
 But we still need to get software on it, which brings us to the installation.
 Installing macOS is done in three steps.
 First, we need to download a restore image with the version of macOS we want to install.
 Then we need to create a configuration that is compatible with that version of macOS.
 And finally, we’ll install our restore image in the compatible virtual machine.
 So first, we need to download a restore image.
 You can download restore images from the developer website, but Virtualization can also help us.
 You can call VZMacOSRestoreImage.
latestSupported to get a restore image object for the latest stable version of macOS.
 This object has a URL property that we can use to download the file.
 Then we want to create a virtual machine that is compatible with the version of macOS we downloaded.
 Virtualization can also help us here.
 We can ask the restore image object for the configuration requirements.
 If the restore image can be run on the current system, we get an object listing the requirements.
 From the requirements, we can obtain the hardware model needed needed to run this version of macOS.
 We have seen previously how to restore a hardware model.
 This is how we obtain a new one.
The requirements also contain two useful properties.
 The object can tell us how many CPUs and how much memory is required to run this version of macOS.
Finally, we are ready to start installation.
 We start by creating a new virtual machine from our configuration.
 Then we create an installer.
 The installer takes two arguments, the compatible virtual machine we created and the path to the restore image we downloaded.
 Now we can just call install(), and voilà, we are ready to run macOS.
Now that we can set up a virtual Mac and install macOS, let’s see some of the special devices for the Mac.
 A first cool capability is GPU acceleration.
 We have built a graphic device that exposes the GPU capabilities to the virtual Mac.
 This means you can run Metal in the virtual machine, and get great graphics performance in macOS.
 Let’s see how to set it up.
We start by creating the graphics device configuration.
 Here, we will use the VZMacGraphicsDeviceConfiguration.
 Next, we want to give it a display.
 We set up the display by defining its size and pixel density.
 Now our device configuration is ready.
 As usual, we set it on the main configuration object.
 We set it as the graphics device for our virtual machine.
Next, we have a new device for interacting with the Mac.
 In macOS Ventura, we are adding the Mac trackpad support to the virtual Mac.
 With the new trackpad, it is possible to use gestures like rotation, pinch to zoom, and so on.
 This new device uses new drivers in macOS.
 So to use it, you will need macOS 13 both on the host system and in the virtual machine.
 Let’s see how to set it up.
 It’s very easy.
 We create a new object of type VZMacTrackpadConfiguration.
 Then we set it as the pointing device on the virtual machine.
 Now when we’ll use the view with our virtual Mac, we can use gestures.
 Finally, let’s look into a common use case for many of us, sharing files between the host system and the virtual machine.
 In macOS 12, we introduced the Virtio file-system device to share files on Linux.
 In macOS Ventura, we are adding support for macOS.
 You can now pick folders that you want to share with the virtual machine.
 Any change you make from the host system is instantly reflected within the virtual machine and vice versa.
 Let’s see how to set it up.
 First, we create a VZShareDirectory with a directory we want to share.
 Then we create a share object.
 Here we'll use VZSingleDirectoryShare to share a single directory.
 You can also share multiple directories by using VZMultipleDirectoryShare.
 Now that we have the share, we need to create a device.
 But we will start we something special.
 File system devices are identified by a tag.
 In macOS Ventura, we have added a special tag to tell the virtual machine to automount this device.
 Here, we take this special tag, macOSGuestAutomountTag.
 Then we create the device and use our special tag.
 We set the share from the single directory we configured.
 And finally, we add the device to the configuration as usual.
 Finally, let’s look at everything together in a demo.
 We start from a basic configuration.
 We have a VZVirtualMachineConfiguration with just CPU, memory, a keyboard, and a disk.
 We want a virtual Mac.
 To do that, we need to start by setting up the platform.
 We'll use createMacPlatform that is defined above to do that.
 The second piece of a virtual Mac is the boot loader.
 We need a boot loader that knows how to boot macOS.
 To get that, we set the platform's boot loader to VZMacOSBootLoader().
 Next, we want to set up the devices.
 We want accelerated graphics.
 To get it, we will set up a VZMacGraphicsConfiguration.
 We create the object, define the display size and pixel density, and we add it to the configuration.
 Next, we want to use the new trackpad.
 All we need to do is set the pointing device to VZMacTrackpadConfiguration.
 That's it.
 Now, we could start the VM, but let's add the cherry on top.
 We have seen how we can share directories.
 Let's do it here.
 We start by creating the filesystem device configuration.
 Here, notice we use the special tag to automount it into macOS.
 Then we define our share.
 Here we use a single directory share from a path on the file system.
 Here, we will share this project we are editing right now.
We add the device to our configuration, and we are done.
Everything is ready.
 We launch our app.
 Since we configured the Mac graphics device, the VZVirtualMachineView can show the content.
 This is what you see here in the window.
 And here it is.
 We have configured macOS from scratch.
 We can see the shared directory and the project we were editing right now.
