# Installing an Arch Linux virtual machine on Windows Hyper-V

## 1. Creating the Internal Network Switch

If you need the VM to directly connect to your network, use an External Switch instead. An Internal Switch will create a
subnet inside the host computer that VMs can connect to. You can give these VMs access to the internet by using Windows
Internet Connection Sharing. 

To set up an Internal Switch with Internet Connection Sharing, go through the following steps:

- Inside the Hyper-V Manager (`virtmgmt.msc`), open the Virtual Switch Manager. Create and name your Internal Switch.

<img src="images/virtual-switch-manager.png" alt="Virtual Switch Manager" width="600" />

- Open the Network Connections Control Panel (`ncpa.cpl`). Select a network adapter that has internet access, that you
  would like to connect the Internal Switch to.

<img src="images/network-adapters-control-panel.png" alt="Network Adapters Control Panel" width="900" />

- Open the Sharing tab in the adapter's properties panel, check the "Allow other network users to connect through this
  computer's Internet connection" box. Select the Internal Switch as the "Home networking connection."

<img src="images/network-adapters-sharing.png" alt="Network Adapters Sharing" width="300" />

## 2. Creating the Virtual Machine in Hyper-V Manager

Open the Hyper-V Manager (`virtmgmt.msc`), and select New > Virtual Machine... to open the New Virtual Machine Wizard.
Go through the wizard as you normally would but take note of the following settings:

- When specifying the Hyper-V Generation, use Generation 2 to create a UEFI virtual machine.

<img src="images/vm-wizard-generation.png" alt="New Virtual Machine Wizard Generation" width="600" />

- Select the Internal Switch you created when configuring networking.

<img src="images/vm-wizard-networking.png" alt="New Virtual Machine Wizard Networking" width="600" />

- Select "Install an operating system from a bootable image file" and set it to your Arch Linux installation image.

<img src="images/vm-wizard-installation.png" alt="New Virtual Machine Wizard Installation" width="600" />

## 3. Disable Secure Boot in the VM settings

Secure Boot requires that the bootloader be signed with Microsoft's key. In order to boot into the Arch Linux
installation image you need to disable Secure Boot. In your virtual machine's settings go to the Security tab and
uncheck "Enable Secure Boot"

<img src="images/vm-settings-secure-boot.png" alt="Virtual Machine Security Settings" width="600" />

## 4. Disable Hyper-V Alt + Tab keyboard shortcut capture (Optional)

By default, Hyper-V will capture keyboard shortcuts like Alt + Tab when the Virtual Machine Connection window is
focused. You can configure this behavior in the Hyper-V settings.

<img src="images/hyperv-settings-keyboard.png" alt="Hyper-V Keyboard Settings" width="600" />

## 5. Install Arch Linux on the Virtual Machine

