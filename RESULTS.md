# ผลการทดลอง API Lab

## ส่วนที่ 0

- Step 0.1, จำนวนชื่อ pizza ทั้งหมด: 18
- Step 0.3, status code ของคำขอที่ไม่มี token: 401 Unauthorized
- Step 0.3, เมื่อใช้ token ใน `auth.txt` จะอ่าน tools ได้สำเร็จ: Knife, Pizza cutter และ Scissors
- Step 0.4, ชื่อ pizza ที่ได้: The Fresh Bolognese
- Step 0.4, คำขอ POST พร้อม `veg.json` ได้ status 200 และส่วนผสมทุกตัวมี `vegetarian: true`
- Step 0.5, การทดสอบผ่าน Python shell: ตรวจสอบ names, tools และ vegetarian pizza สำเร็จ
- Step 0.5, Python `requests` ให้ผลเหมือน curl: names และ tools อ่านได้, vegetarian pizza ตอบ status 200 และมีชื่อ pizza
- Step 0.6, `test_names.py` ผ่าน: status 200 และมีชื่อ Margherita ในรายการ
- Step 0.7, `test_tools.py` ผ่าน: status 200 และ response ตรงกับ tools ทั้ง 3 รายการ
- Step 0.8, `test_pizza.py` ตรวจสอบ vegetarian ingredients และกรณีไม่มี token ที่ต้องได้ 401
- Step 0.9, ทดลองสร้าง user ชื่อสุ่ม, login ด้วย session เดิม และอ่าน ratings ของ user ใหม่ได้เป็นรายการว่าง
- Step 0.2, status code และ headers: HTTP 200, `Content-Type: application/json` และมี `set-cookie` ของ AWSALB
