# ผลการทดลอง API Lab

## ส่วนที่ 0

- Step 0.1, จำนวนชื่อ pizza ทั้งหมด: 18
- Step 0.3, status code ของคำขอที่ไม่มี token: 401 Unauthorized
- Step 0.3, เมื่อใช้ token ใน `auth.txt` จะอ่าน tools ได้สำเร็จ: Knife, Pizza cutter และ Scissors
- Step 0.4, ชื่อ pizza ที่ได้: The Fresh Bolognese
- Step 0.4, คำขอ POST พร้อม `veg.json` ได้ status 200 และส่วนผสมทุกตัวมี `vegetarian: true`
- Step 0.5, การทดสอบผ่าน Python shell: ตรวจสอบ names, tools และ vegetarian pizza สำเร็จ
- Step 0.2, status code และ headers: HTTP 200, `Content-Type: application/json` และมี `set-cookie` ของ AWSALB
