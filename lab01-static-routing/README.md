# Lab 01: Static Routing

## เป้าหมาย

ตั้งค่า static route ให้ PC ในสองเครือข่ายติดต่อกันได้ ผ่าน Router 2 ตัว

## ผังเครือข่าย

![topology](topology.png)

## ตาราง IP

| อุปกรณ์ | Interface | IP | Mask | Gateway |
|---------|-----------|----|------|---------|
| PC0 | NIC | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| R1 | G0/0 | 192.168.1.1 | 255.255.255.0 | - |
| R1 | G0/1 | 10.0.0.1 | 255.255.255.252 | - |
| R2 | G0/1 | 10.0.0.2 | 255.255.255.252 | - |
| R2 | G0/0 | 192.168.2.1 | 255.255.255.0 | - |
| PC1 | NIC | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |

## ขั้นตอนที่ทำ

1. ต่อสายตามผัง แล้วตั้ง IP ที่ทุก interface และเปิด interface ด้วย `no shutdown`
2. ตั้ง static route ที่ R1 และ R2 ให้ชี้ไปหาเครือข่ายฝั่งตรงข้าม
3. ทดสอบ ping จาก PC0 ไป PC1 และตรวจ routing table

## คำสั่งสำคัญ

ตั้ง IP ที่ R1

```
R1> enable
R1# configure terminal
R1(config)# interface g0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# interface g0/1
R1(config-if)# ip address 10.0.0.1 255.255.255.252
R1(config-if)# no shutdown
R1(config-if)# exit
```

ตั้ง static route ที่ R1 (ไปเครือข่าย 192.168.2.0 ผ่าน R2)

```
R1(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

ตั้ง IP และ static route ที่ R2

```
R2(config)# interface g0/1
R2(config-if)# ip address 10.0.0.2 255.255.255.252
R2(config-if)# no shutdown
R2(config-if)# exit
R2(config)# interface g0/0
R2(config-if)# ip address 192.168.2.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit
R2(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.1
```

บันทึกค่า

```
R1# copy running-config startup-config
```

คำสั่งตรวจสอบ

```
R1# show ip route
R1# show ip interface brief
R1# ping 10.0.0.2
C:\> ping 192.168.2.10
C:\> tracert 192.168.2.10
```

ใน `show ip route` เส้นทางที่ตั้งเองจะขึ้นเป็น `S` ส่วนเครือข่ายที่ต่ออยู่ตรงจะขึ้นเป็น `C`

## ปัญหาที่เจอ

(ถ้ามี ให้เขียนเพิ่ม เช่น ping ไม่ผ่านเพราะลืมตั้ง gateway ที่ PC หรือลืม `no shutdown`)
