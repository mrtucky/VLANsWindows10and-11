# VLANsWindows10and-11
How to access different vLANs in Windows 10 and 11

Ok, I shall start with few sample powershell administrative level commands. Before running those commands- one has to enable HyperV which is Windows 10 Pro and Windows 11 Pro buildin type 1 hypervisor. Those who are using Oracle Virtualbox, good news is that Hyper V and VirtualBox can work on the same computer. In short, they are compatible with each other. Virtual Network Adapters based on vLANs will be accessable in VirtualBox. No need to install intel's PROset drivers or Realtek's Diagnostic Utilitiy to access vLANS. Anyway, Intel has stopped providing PROset Driver for intel based cards for Windows 11.


==============================command=====================================
Get-NetAdapter   #returns list of network adapters
==========================================================================


==============================command- Create a vSwitch=====================================
=============================================================================================
New-VMSwitch -name slice3Switch -NetAdapterName Ethernet -AllowManagementOS $true  #this will create a new vswitch named vlansswitch and adding physical nic Lan_connection to it and managed by OS 
============================================================================================




=========================removing virtual adapter created by the above command=====================
# Hyper-V automatically creates a virtual Network Adapter without a VLAN tag to keep the host online - Remove it, except you are using a Untagged/Tagged combination. The trouble with this virtual Network Adapter is that it has the same name as external virtual switch. So, I remove it and then add a virtual adapter as untagVLAN or something that tells me that this virtual adapter will deal with untagged traffic which you set on the managed switch.

=============================================================
Remove-VMNetworkAdapter -ManagementOS -Name slice3Switch  #this will remove vNIC which was created automatically by the above command
==============================================================

====================================================================
to add an untagged virtual network adapter- the command will be

Add-VMNetworkAdapter -ManagementOS -Name "untagVLAN" -SwitchName "slice3Switch" #no need to assign any vlan id or untagged label
============================================================================

==============================================
After that command untagVLAN virtual adapter will handle all you untagged traffic
===============================================


now we create our own virtual adapters based on vlans and ADD THEM TO OUR NEWLY CREATED vSWITCH named slice3Switch as below
==============================================================================================================

Add-VMNetworkAdapter -ManagementOS -Name "VLAN88" -SwitchName "slice3Switch" -Passthru | Set-VMNetworkAdapterVlan -Access -VlanId 88
Add-VMNetworkAdapter -ManagementOS -Name "VLAN999" -SwitchName "slice3Switch" -Passthru | Set-VMNetworkAdapterVlan -Access -VlanId 999

Add-VMNetworkAdapter -ManagementOS -Name "VLAN333" -SwitchName "slice3Switch" -Passthru | Set-VMNetworkAdapterVlan -Access -VlanId 333
Add-VMNetworkAdapter -ManagementOS -Name "VLAN103" -SwitchName "slice3Switch" -Passthru | Set-VMNetworkAdapterVlan -Access -VlanId 103

Add-VMNetworkAdapter -ManagementOS -Name "VLAN10" -SwitchName "slice3Switch" -Passthru | Set-VMNetworkAdapterVlan -Access -VlanId 10
Add-VMNetworkAdapter -ManagementOS -Name "VLAN30" -SwitchName "slice3Switch" -Passthru | Set-VMNetworkAdapterVlan -Access -VlanId 30

Add-VMNetworkAdapter -ManagementOS -Name "VLAN888" -SwitchName "slice3Switch" -Passthru | Set-VMNetworkAdapterVlan -Access -VlanId 888

Those commands work on Windows 10 Pro and Windows 11 Pro with hyper V installed and Window machines are connected to managed switch properly configured tagged vlans and untagged traffic.

It is not a full description but I am sure one can start using internet resources and those commands to have a full fledged vLAN sollution on windows pro 10 and 11 like PROXMOX.
