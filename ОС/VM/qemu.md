 qemu - инструмент для создания виртуальных машин [[ОС]] используя [[Виртуализация]]
KVM - модуль ядра Linux позволяющий использовать **аппаратную виртуализацию** 
QEMU — интерфейс и эмулятор оборудования  
KVM — ускоритель виртуализации

----

### создание VM
1. создать виртуальный диск
```bash
qemu-img create -f hardOS.qcow2 20G
```
2. запуск ISO образа
```bash
qemu-system-x86_64 \
  -enable-kvm \
  -m 4096 \
  -cpu host \
  -smp 2 \
  -drive file=ubuntu_vm.qcow2,format=qcow2 \
  -cdrom ISO_BOOT_DISK.iso \
  -boot d \
  -vga virtio \
  -net nic -net user \
  -name "UbuntuServer"
```
- `-enable-kvm` - запуск kvm
- `-m` - RAM память 4096M -> 4ГБ
- `-cpu host` - использование cpu хоста
- `-smp 2` - использовать 2 ядра
- `-drive file=DISK, format=qcow2`
- `-cdroom ISO` - загрузочный образ
- `-boot d` - запуститься с загрузочного образа
- `-vga virtio` - драйвер видео
- `-net nic -net user` - простая сетевая конфигурация [[NAT]]
3. запуск системы
```bash
qemu-system-x86_64 \
  -enable-kvm \
  -m 4096 \
  -cpu host \
  -smp 2 \
  -drive file=ubuntu_vm.qcow2,format=qcow2 \
  -boot c \
  -vga virtio \
  -net nic -net user \
  -name "UbuntuServer"
```
-> или если нужен реальный ip для vm можно использовать
`-netdev bridge,id=br0,br=br0 -device virtio-net-pci,netdev=br0`

---

#### GUI
`sudo virt-manager`
если лень писать команды можно создать через GUI

---

```bash
#!/bin/bash
ISO="ubuntu-22.04-live-server-amd64.iso"
DISK="ubuntu_vm.qcow2"

qemu-img create -f qcow2 $DISK 20G

qemu-system-x86_64 \
  -enable-kvm -m 4096 -cpu host -smp 2 \
  -drive file=$DISK,format=qcow2 \
  -cdrom $ISO -boot d \
  -net nic -net user \
  -name "DevOpsLab"
```
-> минимум для DevOps лабы

---

### ssh
если надо подключится через хост к вм по ssh
`-net nic,model=virtio -net user,hostfwd=tcp::2222-:22`
а подключение:
`ssh -p user:localhost`

---

### какая ВМ нужна для кластера k8s
самый минимум:
- 1 worker(control panel)
- 2 nodes(ноды)

| Роль VM     | vCPU | RAM    | Диск  | Пример имени             |
| ----------- | ---- | ------ | ----- | ------------------------ |
| Master node | 2    | 2–4 ГБ | 20 ГБ | `k8s-master`             |
| Worker node | 2    | 2 ГБ   | 20 ГБ | `k8s-node1`, `k8s-node2` |
### какую ОС использовать
- **Ubuntu Server 22.04 LTS** ✅ (самый популярный вариант)
- **Debian 12**
- **Rocky Linux / CentOS Stream 9**
- **Fedora Server**

---

при тестировании ВМ я столкнулся с ошибкой `qemu-system-x86_64: Virtio VGA not available`

Эта ошибка говорит о том, что QEMU не может найти или загрузить **видеодрайвер Virtio VGA**.  
Он зависит от:
- правильного **типа виртуализации (machine type)**,
- **видеоадаптера**, который поддерживается в сборке QEMU,
- и **версии QEMU** (некоторые сборки не включают `virtio-vga`).

```bash
sudo virsh net-define /usr/share/libvirt/networks/default.xml
sudo virsh net-autostart default
sudo virsh net-start default
```
запуск:
```bash
qemu-system-x86_64 \
  -enable-kvm \
  -m 2048 \
  -cpu host \
  -smp 2 \
  -drive file=debian12.qcow2,format=qcow2 \
  -cdrom debian-12-netinst.iso \
  -boot d \
  -vga std \
  -display vnc=:1 \
  -nic bridge,br=virbr0,model=virtio
```

----

