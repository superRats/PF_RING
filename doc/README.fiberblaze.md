# Silicom/Fiberblaze support in PF_RING

## Prerequisites
We expect you to have installed the Fiberblaze drivers and loaded them. 
Typical commands are:

```
echo 34359738368 > /proc/sys/kernel/shmmax
echo 34359738368 > /proc/sys/kernel/shmall
echo 2048 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages
export PATH=/opt/fiberblaze/bin:$PATH
echo "/opt/fiberblaze/lib" > /etc/ld.so.conf.d/fiberblaze.conf; ldconfig
cd /opt/fiberblaze/driver; make; ./load_driver.sh hugepages='2G'
cd /opt/fiberblaze/bin; ./configurecard --device fbcard0 --configuration ../fbcard.cfg
```

# Receive Traffic
The naming convention for RX is fbcard:CARD_ID:GROUP_NAME:GROUP_RING_ID where CARD_ID is the id of the card we want to open, GROUP_NAME is the name of the group specified in fbcard.cfg used by configurecard, and RING_ID (in case of traffic hashing, i.e. Fiberblaze's RSS) if the id of the PRBs.

Example if you have set in fbcard.cfg

```
prbGroup "b"
{
    noPrbs 8
    hash HashPacket
    filter "hash"
}
```

the device names are
```
fbcard:0:b:0
fbcard:0:b:1
...
fbcard:0:b:7
```

Example (receive packets from card 0, group "b, ring 0): 
```
pfcount -i fbcard:0:b:0
```

# Transmit Traffic
The naming convention for TX is fbcard:CARD_ID:PORT_ID. If you have a 4 port NIC, the PORT_ID will be 0 to 3.

Example (send packets from port 1 of cardId 0):
```
pfsend -i fbcard:0:1
```
