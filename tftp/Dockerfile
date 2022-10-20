FROM fedora:36 as fedora_build
RUN dnf install -y syslinux shim-x64 grub2-efi-x64

FROM alpine:3.16.2
RUN apk update && apk add tftp-hpa
RUN mkdir /var/tftpboot/f36
RUN mkdir /var/tftpboot/uefi
COPY --from=fedora_build /usr/share/syslinux/pxelinux.0 /var/tftpboot/
COPY --from=fedora_build /usr/share/syslinux/menu.c32 /var/tftpboot/
COPY --from=fedora_build /usr/share/syslinux/vesamenu.c32 /var/tftpboot/
COPY --from=fedora_build /usr/share/syslinux/ldlinux.c32 /var/tftpboot/
COPY --from=fedora_build /usr/share/syslinux/libcom32.c32 /var/tftpboot/
COPY --from=fedora_build /usr/share/syslinux/libutil.c32 /var/tftpboot/
COPY --from=fedora_build /boot/efi/EFI/fedora/shimx64.efi /var/tftpboot/uefi/
COPY --from=fedora_build /boot/efi/EFI/fedora/grubx64.efi /var/tftpboot/uefi/
ADD ["default","/var/tftpboot/pxelinux.cfg"]
ADD ["grub.cfg","/var/tftpboot/uefi/grub.cfg"]
RUN wget https://download.fedoraproject.org/pub/fedora/linux/releases/36/Server/x86_64/os/images/pxeboot/vmlinuz -O /var/tftpboot/f36/vmlinuz
RUN wget https://download.fedoraproject.org/pub/fedora/linux/releases/36/Server/x86_64/os/images/pxeboot/initrd.img -O /var/tftpboot/f36/initrd.img
RUN chmod -R 755 /var/tftpboot/
CMD in.tftpd -Llsvvv /var/tftpboot/
