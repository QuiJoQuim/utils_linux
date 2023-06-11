# Màquines Virtuals

En principi per tot plegat usarem el Sistema de gesrions de Màquines Virtuals que ve de Sèriue  mb el ubuntu >20.04: virt-manager

## Per assignar una IP fixa:

A la màquina Host ubiquem quin és el sistema de DHCP usat (habitual el **default**)

```
$ virsh net-list
 Name      State    Autostart   Persistent
--------------------------------------------
 default   active   yes         yes
```

I mirem quina configuració de DHCP està usant per default:
```
$ virsh net-dumpxml default
<network connections='1'>
  <name>default</name>
  <uuid>c8b0e0b8-f1c5-47dd-9074-673c45185279</uuid>
  <forward mode='nat'>
    <nat>
      <port start='1024' end='65535'/>
    </nat>
  </forward>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='52:54:00:39:88:99'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.254'/>
    </dhcp>
  </ip>
</network>
```

Ara jo ho veiem:
* cal posar la ip estàtica corresponent dins del tag dhcp.
* Cal reduir el rang per deixar uns nombres sense assignació dinàmica

```
$ virsh net-edit default
```

I obre el nano per editar, modifiquem el tag dhcp per fer el que hem dit:
```
<network connections='1'>
  <name>default</name>
  <uuid>c8b0e0b8-f1c5-47dd-9074-673c45185279</uuid>
  <forward mode='nat'>
    <nat>
      <port start='1024' end='65535'/>
    </nat>
  </forward>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='52:54:00:39:88:99'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.50' end='192.168.122.254'/>
      <host mac='52:54:00:07:07:3f' name='SrvDocker1' ip='192.168.122.5'/>
    </dhcp>
  </ip>
</network>
```

És a dir identifiquem la màquina per nom i per mac (info de la màquina al vir-manager)

Bé ara només queda reiniciar per a que tot vagi comn hga d'anar:

```
$ virsh net-destroy default
$ virsh net-start default
$ virsh shutdown SrvDocker1
$ virsh start SrvDocker1
```

Et Voilá!


