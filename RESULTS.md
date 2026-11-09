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
- Step 0.10, เพิ่ม `QuickPizza` session fixture ที่ใช้ร่วมกันตลอดการรันทดสอบ เพื่อรักษา cookie ของ load balancer
- Step 0.11, fixture `auth` สร้าง user ชื่อสุ่ม, login และทดสอบว่า user ใหม่เริ่มต้นด้วย ratings ว่าง
- Step 0.12, ผลการทดสอบแบบไม่มี session ทั้ง 3 รอบจะบันทึกหลังรันจริง; จากนั้นลบไฟล์ flaky test ออกจากชุดทดสอบถาวร
- Step 0.13, fixture `pizza_id` สร้าง pizza ของ test และ `test_ratings.py` สร้าง rating ใหม่ได้ status 201 พร้อม id, stars และ pizza_id
- Step 0.14, fixture `rating` สร้างและลบข้อมูลหลัง test; ทดสอบอ่าน, เปลี่ยนดาว และลบ rating ได้ครบ โดยผลลัพธ์คือ 200, 200 และ 204 ตามลำดับ และการอ่านหลังลบได้ 404
- Step 0.15, เพิ่ม tests สำหรับ username ซ้ำ, password ผิด และ username ว่าง โดยตรวจทั้ง status code และข้อความ error

- Step 0.16, blocks ของจำนวน tools ที่ถูก exclude:
  - 0 tools: `[]`
  - 1 tool: `["Knife"]` และ `["Scissors"]`
  - 2 tools: `["Knife", "Pizza cutter"]`
  - 3 tools: `["Knife", "Pizza cutter", "Scissors"]` และย้ายไปเป็น test defect แบบ xfail

## รายงาน defect

- Title: การ exclude tools ทั้ง 3 รายการทำให้ API ตอบ Internal Server Error
- Request: `POST /api/pizza` พร้อม `excludedTools` เป็น `Knife`, `Pizza cutter` และ `Scissors`
- Expected result: status 400 Bad Request พร้อม JSON error message
- Actual result: status 500 Internal Server Error พร้อม response body ว่าง
- Severity: Medium เพราะ input ที่ไม่สามารถสร้าง pizza ได้ควรถูกจัดการเป็นข้อผิดพลาดของข้อมูล ไม่ใช่ server error

## ส่วนที่ A

- Step A1, input domain model ของ `POST /api/ratings` แบ่งเป็น 4 characteristics คือจำนวนดาว, ชนิดของดาว, pizza และ token โดย base choice คือ 3 ดาวแบบจำนวนเต็ม, pizza ที่มีอยู่ และ token ของผู้ใช้เอง
- Step A2, base choice coverage ใช้ 9 test cases: `1 + (3-1) + (4-1) + (2-1) + (3-1) = 9` และ README ระบุ test ที่ครอบคลุมทุก block แล้ว
- Step 0.2, status code และ headers: HTTP 200, `Content-Type: application/json` และมี `set-cookie` ของ AWSALB
