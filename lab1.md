# Arquitectura de Laboratorio.
##Para este laboratorio se deben tomar en cuanta las siguientes consideraciones:
##1 .- Arquitectura:
   * 1 Fedora 24 KVM Nested.
   * 1 VM appliance RHEV4.
   * 1 VM Hipervisor for RHEV 4.
  
##2.- Descargar RHEV - Manager Appliance desde el portal de Red Hat.
##3.- Descargar RHEV - Hypervisor para RHEV 4.0.

###Nota:

Los archivos .ova que contienen la descripción del hardware de la  VM es sinónimo de los archivos XML en KVM/RHEV. La imagen por default esta diseñada para trabajar bajo un export domain de RHEV. Para usarlo en un ambiente sin RHEV usaremos imagenes qcow2.

Por lo tanto se necesita para llevar a cabo el siguiente procedimiento para generar un archivo XML con la descripción de los componentes de la VM.

##Extrayendo la imagen .ova

    [root@chakanais rhev4]# tar -xvf rhevm-appliance-20160731.0-1.x86_64.rhevm.ova
    images/
    images/fb3c633e-58e8-47c9-984b-ce752ef7479e/
    images/fb3c633e-58e8-47c9-984b-ce752ef7479e/93401448-d96d-4e74-ab28-4a9d31d869de.meta
    images/fb3c633e-58e8-47c9-984b-ce752ef7479e/93401448-d96d-4e74-ab28-4a9d31d869de
    master/
    master/vms/
    master/vms/847571ea-2086-4012-a3a2-8016e7c62e4d/
    master/vms/847571ea-2086-4012-a3a2-8016e7c62e4d/847571ea-2086-4012-a3a2-8016e7c62e4d.ovf

## Una vez que tenemos la imagen procederemos a validar sus características

    [root@chakanais fb3c633e-58e8-47c9-984b-ce752ef7479e]# qemu-img info 93401448-d96d-4e74-ab28-4a9d31d869de
    image: 93401448-d96d-4e74-ab28-4a9d31d869de
    file format: qcow2
    virtual size: 50G (53687091200 bytes)
    disk size: 2.9G
    cluster_size: 65536
    Format specific information:
        compat: 0.10
        refcount bits: 16
        
## Creamos el disco en formato qcow2.

    [root@chakanais fb3c633e-58e8-47c9-984b-ce752ef7479e]# qemu-img creat -f qcow2 RHEV-4.0.qcow2

## Extendemos FS.

    [root@chakanais fb3c633e-58e8-47c9-984b-ce752ef7479e]# virt-resize --expand /dev/sda1 93401448-d96d-4e74-ab28-4a9d31d869de /var/lib/libvirt/images/RHEV-4.0.qcow2 
    [   0.0] Examining 93401448-d96d-4e74-ab28-4a9d31d869de
    **********
    Summary of changes:
    /dev/sda1: This partition will be resized from 6.0G to 100.0G.  The 
    filesystem xfs on /dev/sda1 will be expanded using the 'xfs_growfs' method.
        **********
    [   6.8] Setting up initial partition table on /var/lib/libvirt/images/RHEV-4.0.qcow2
    [   6.9] Copying /dev/sda1
     100% ⟦▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒⟧ 00:00
    [  15.1] Expanding /dev/sda1 using the 'xfs_growfs' method
    Resize operation completed with no errors.  Before deleting the old disk, 
    carefully check that the resized disk boots and works correctly.
    
## Asignamos Password

    [root@chakanais fb3c633e-58e8-47c9-984b-ce752ef7479e]# virt-customize -a /var/lib/libvirt/images/RHEV-4.0.qcow2 --root-password password:redhat
    [   0.0] Examining the guest ...
    [  12.8] Setting a random seed
    [  12.8] Setting passwords
    [  14.7] Finishing off
    
## Eliminamos Cloud-Init

    [root@chakanais fb3c633e-58e8-47c9-984b-ce752ef7479e]# virt-customize -a /var/lib/libvirt/images/RHEV-4.0.qcow2 --run-command 'yum remove cloud-init* -y' 
    [   0.0] Examining the guest ...
    [  15.3] Setting a random seed
    [  15.3] Running: yum remove cloud-init* -y
    [  19.9] Finishing off

## Ahora podemos crear nuestra vm importando el disco mediante virt-manager.

