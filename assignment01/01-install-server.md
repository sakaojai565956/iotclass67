# Install Server and Docker
>> อธิบายว่า ทำการติดตั้งอย่างไร 
-ติดตั้ง Server ใช้ Ubuntu
ก่อนอื่นเลยเราต้องอัปเดตแพ็กเกจให้เป็นเวอร์ชันล่าสุดก่อน โดยใช้คำสั่งนี้:sudo apt update && sudo apt upgrade -y

-ติดตั้ง SSH
sudo apt install openssh-server -y
-เปิดบริการ SSH ด้วยคำสั่ง:
sudo systemctl enable ssh
sudo systemctl start ssh




## How to install Server
load ISO มาจาก ubantu official website 
เอา version jammy jellyfish

ติดตั้ง rufas ในคอม

เอา rufas มา burn flashdrive ให้เป็น USB BOOT stick

เข้าเข้า bios ของ gateway machine

เอา flashdrive boot มาเสียบที่ machine

กดเลือก try or install 

กดเลือก ติดตั้ ubantu ทับ window ไปเลย

กด next รัวๆ

ต่อ internet โดยการต่อ lan เข้ากับ com เเล้วก็คั้งค่า bridge network 




## How to install Docker
https://docs.docker.com/engine/install/ubuntu/
เข้า web docker official เข้าไปที่ document เกี่ยวกับการติดตั้ง docker 
coppy code ตามเว็บ




