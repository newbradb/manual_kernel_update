# **Kernel update**

### **Клонирование и запуск**

Делаем `fork` данного [репозитория](https://github.com/dmitry-lyutenko/manual_kernel_update)

Клонируем к себе на рабочую машину : 

```console
$ git clone https://github.com/newbradb/manual_kernel_update.git
Cloning into 'manual_kernel_update'...
remote: Enumerating objects: 34, done.
remote: Total 34 (delta 0), reused 0 (delta 0), pack-reused 34
Unpacking objects: 100% (34/34), done
```

Запустим виртуальную машину и залогинимся:

```console
$ vagrant up
Bringing machine 'kernel-update' up with 'virtualbox' provider...
....
==> kernel-update: Setting hostname...

$ vagrant ssh
[vagrant@kernel-update ~]$ uname -r
3.10.0-1127.el7.x86_64
```

Теперь приступим к обновлению ядра.

### **kernel update**


Подключаем репозиторий, откуда возьмем необходимую версию ядра.
```console
$ sudo yum install -y http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm

...

Running transaction
  Installing : elrepo-release-7.0-3.el7.elrepo.noarch                                                                                                                                                   1/1 
  Verifying  : elrepo-release-7.0-3.el7.elrepo.noarch                                                                                                                                                   1/1 

Installed:
  elrepo-release.noarch 0:7.0-3.el7.elrepo
```

Ставим последнее ядро:

```console
$ sudo yum --enablerepo elrepo-kernel install kernel-ml -y
...

Running transaction
  Installing : kernel-ml-5.12.0-1.el7.elrepo.x86_64                                                                                                                                                     1/1 
  Verifying  : kernel-ml-5.12.0-1.el7.elrepo.x86_64                                                                                                                                                     1/1 

Installed:
  kernel-ml.x86_64 0:5.12.0-1.el7.elrepo                                                                                                                                                                    

Complete!
```

Обновляем конфигурацию загрузчика:

```console
[vagrant@kernel-update ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-5.12.0-1.el7.elrepo.x86_64
Found initrd image: /boot/initramfs-5.12.0-1.el7.elrepo.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-1127.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1127.el7.x86_64.img
done
```

Выбираем загрузку с новым ядром по-умолчанию и перезагружаем машину:

```console
[vagrant@kernel-update ~]$ sudo grub2-set-default 0
[vagrant@kernel-update ~]$ sudo reboot
Connection to 127.0.0.1 closed by remote host.
Connection to 127.0.0.1 closed.

```

Ядро обновилось :

```console
[vagrant@kernel-update ~]$ uname -r
5.12.0-1.el7.elrepo.x86_64
```
# **PACKER** 

Пробуем запустить билд :

```console
$ packer build centos.json
Error: Failed to prepare build: "centos-7.7"

1 error occurred:
	* Deprecated configuration key: 'iso_checksum_type'. Please call `packer fix`
against your template to update your template to be compatible with the current
version of Packer. Visit https://www.packer.io/docs/commands/fix/ for more
detail.

```

Запускаем ```packer fix centos.json``` видим что строки 'iso_checksum_type' нету в выводе.   
Удаляем ее из centos.json. Запускаем билд :

```console
$ packer build centos.json  
centos-7.7: output will be in this color.

==> centos-7.7: Cannot find "Default Guest Additions ISO" in vboxmanage output (or it is empty)
==> centos-7.7: Retrieving Guest additions checksums
