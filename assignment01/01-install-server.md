# Install Server and Docker
>> อธิบายว่า ทำการติดตั้งอย่างไร 

ใช้ Windows Server เป็นเครื่อง Server ของเราเมื่อติดตั้ง Windows Server เสร็จเรียบร้อยแล้ว ขั้นตอนต่อไปคือการติดตั้ง Docker ผ่าน Command Prompt หรือ PowerShell บน Windows Server ก่อนที่จะเริ่มติดตั้ง Docker เราต้องแก้ไขปัญหาเรื่อง Network ของเครื่อง Server ก่อน
กลุ่มของเราแก้ปัญหาโดยใช้สาย LAN เชื่อมต่อเครื่อง Server กับเครื่องคอมพิวเตอร์ที่เชื่อมต่ออินเทอร์เน็ตอยู่แล้ว จากนั้นทำการตั้งค่า Network Bridge บน Windows โดยการแชร์การเชื่อมต่ออินเทอร์เน็ตจากเครื่องคอมพิวเตอร์ไปยังเครื่อง Server เพื่อให้ทั้งสองเครื่องอยู่ในวง LAN เดียวกันและสามารถเข้าถึงอินเทอร์เน็ตได้
เมื่อNetwork เชื่อมต่อเรียบร้อยแล้ว เราจึงสามารถติดตั้ง Docker Desktop บน Windows Server โดยดาวน์โหลดจากเว็บไซต์ Docker และทำตามขั้นตอนการติดตั้ง จากนั้นทำการทดสอบ Docker ด้วยการรันคำสั่ง docker run hello-world เพื่อเช็คว่าการติดตั้งสำเร็จและทำงานได้ถูกต้อง



## How to install Server

1.)เตรียมอุปกรณ์
เชื่อมต่อ Dell Server กับจอภาพ, เมาส์, คีย์บอร์ด และสาย LAN จากนั้นใช้แฟลชไดร์ฟที่บรรจุไฟล์ติดตั้ง Windows Server (สามารถดาวน์โหลดได้จาก Microsoft)

2.)บูตจากแฟลชไดร์ฟ
เปิด Dell Server แล้วกด F2 เพื่อเข้าสู่เมนูบูต จากนั้นเลือกบูตจากแฟลชไดร์ฟ

3.)เริ่มการติดตั้ง
ทำตามขั้นตอนการติดตั้ง โดยเลือกภาษา, เขตเวลา, และสร้างบัญชี Administrator ด้วย Username และ Password

4.)ตรวจสอบการเชื่อมต่อเครือข่าย
หลังการติดตั้งเสร็จสิ้น ใช้คำสั่ง ipconfig เพื่อตรวจสอบ IP Address หรือปรับแต่งการตั้งค่าเครือข่ายตามความต้องการ

5.)เปิดการเข้าถึงจากระยะไกล
เปิดใช้งาน Remote Desktop หรือ ติดตั้ง OpenSSH Server เพื่อให้คอมพิวเตอร์เครื่องอื่นสามารถเข้ามาจัดการผ่านเครือข่ายได้ง่ายขึ้น

6.)ตรวจสอบการทำงาน
เมื่อเสร็จสิ้นการตั้งค่าทั้งหมดแล้ว ให้รีสตาร์ทเครื่องและทดสอบว่า Windows Server ทำงานได้ตามปกติและสามารถเชื่อมต่อผ่านเครือข่ายได้




## How to install Docker
การติดตั้ง Docker บน Ubuntu โดย
>ลบแพ็กเกจที่เกี่ยวข้องกับ Docker เดิมออก
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

>ติดตั้ง GPG Key ของ Docker

--apt-get update เพื่ออัปเดตรายการแพ็กเกจ
sudo apt-get update

--ca-certificates และ curl คือเครื่องมือที่ช่วยในการดาวน์โหลดข้อมูลและตรวจสอบการรับรองความปลอดภัย
sudo apt-get install ca-certificates curl

--สร้างไดเรกทอรีใหม่ที่ /etc/apt/keyrings เพื่อเก็บ GPG Key
sudo install -m 0755 -d /etc/apt/keyrings

--ดาวน์โหลดและเก็บ Docker GPG Key ซึ่งใช้สำหรับยืนยันความปลอดภัยในการดาวน์โหลดแพ็กเกจ Docker
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

>>เพิ่ม Docker repository ลงใน Apt sources

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

(คำสั่งนี้จะเพิ่มที่อยู่ของ repository อย่างเป็นทางการของ Docker ลงในระบบ ทำให้ apt สามารถดึงแพ็กเกจ Docker จากที่นี่ได้โดยตรง จากนั้นรัน apt-get update เพื่ออัปเดตรายการแพ็กเกจจากแหล่งใหม่)

>>ติดตั้ง Docker Engine และเครื่องมือที่เกี่ยวข้อง

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin


--ติดตั้ง Docker Community Edition (CE) ซึ่งเป็น Docker เวอร์ชันฟรี
--docker-ce-cli คือ CLI สำหรับจัดการ Docker
--containerd.io คือ container runtime ที่ใช้โดย Docker
--docker-buildx-plugin และ docker-compose-plugin คือปลั๊กอินสำหรับการสร้างและจัดการ containers แบบซับซ้อน

>>ทดสอบการติดตั้ง Docker

sudo docker run hello-world
คำสั่งนี้จะดาวน์โหลดและรันคอนเทนเนอร์ทดสอบชื่อ "hello-world" เพื่อตรวจสอบว่าการติดตั้ง Docker สำเร็จ

>>ตั้งค่าให้ผู้ใช้สามารถใช้ Docker โดยไม่ต้องใช้ sudo

--ร้างกลุ่มชื่อ docker เพื่อให้สามารถรันคำสั่ง Docker ได้โดยไม่ต้องใช้ sudo
sudo groupadd docker

--เพิ่มผู้ใช้ปัจจุบันเข้าไปในกลุ่ม docker
sudo usermod -aG docker $USER

--ใช้ newgrp docker เพื่อให้การเปลี่ยนกลุ่มมีผลทันที โดยไม่ต้องล็อกเอาต์แล้วล็อกอินใหม่
newgrp docker


>> ทดสอบ Docker 

docker run hello-world
--รันคำสั่งนี้อีกครั้งโดยไม่ใช้ sudo เพื่อตรวจสอบว่าผู้ใช้สามารถใช้ Docker ได้