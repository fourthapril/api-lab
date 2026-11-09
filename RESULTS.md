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
- Step A3, boundary test ของ update ใช้ค่า 0, 1, 5 และ 6 โดย 1 และ 5 ต้องได้ 200 ส่วน 0 และ 6 ต้องได้ 400 และค่าดาวเดิมต้องไม่เปลี่ยนเมื่อ update ถูกปฏิเสธ
- Step A4, status code ที่วัดได้เมื่อ user อื่นพยายามอ่าน, แก้ไข และลบ rating คือ GET 404, PUT 400 และ DELETE 400 ตามลำดับ

## ส่วนที่ B: Reflection

1. API test สามารถพบปัญหา integration เช่น server หรือ database ทำงานร่วมกันผิดพลาด และปัญหา network หรือ load balancer ที่ unit test ซึ่งใช้ stub มองไม่เห็น ส่วน unit test ยังจำเป็นเพราะรันเร็ว ผลคงที่ และช่วยตรวจ logic ของฟังก์ชันเล็ก ๆ ได้โดยแยกจากระบบอื่น
2. แต่ละ test สร้าง user ใหม่ด้วยชื่อสุ่ม เพื่อไม่ให้ข้อมูลของ test อื่นหรือผู้ใช้อื่นมารบกวนกัน และไม่แก้ไข rating ผ่าน public token ใน Project 2 จะสร้างข้อมูล shop หรือ hotel ของ test เองด้วยชื่อหรือรหัสสุ่ม แล้วลบหรือแยกข้อมูลหลัง test
3. ทุก request ส่งผ่าน session เดียวกันเพื่อเก็บ cookie เช่น AWSALB ทำให้ request หลัง create และ login ไปยัง server เดิม หากไม่ใช้ session load balancer อาจส่งไปคนละ server ทำให้ login ล้มเหลวแบบ flaky ได้ Step 0.12 เป็น optional และไฟล์ temporary ถูกลบออกจาก final suite
4. Block ที่เปิดเผย defect คือการ exclude tools ทั้ง 3 รายการ เพราะ API ตอบ 500 พร้อม body ว่าง แทนที่จะตอบ 400 พร้อมข้อความ error 500 ทำให้ client แยกไม่ออกว่า input ผิดอย่างไรและอาจเกิดจาก server failure จริง การเก็บ test ไว้เป็น `xfail` ทำให้ defect ยังถูกติดตาม และ `strict=True` จะทำให้ suite แจ้งทันทีเมื่อระบบถูกแก้แล้ว
5. Base choice coverage ต้องใช้ `1 + (3-1) + (4-1) + (2-1) + (3-1) = 9` tests ส่วน all combinations coverage ต้องใช้ `3 × 4 × 2 × 3 = 72` tests
6. User อื่นได้ GET 404, PUT 400 และ DELETE 400 โดยคาดหวังให้ทุก request ถูกปฏิเสธด้วย 4xx เช่น 403 หรือ 404 เพื่อป้องกันการอ่าน แก้ไข หรือลบ rating ของผู้อื่น ความแตกต่างระหว่าง 403 กับ 404 อาจเป็น policy ของระบบ ไม่ถือเป็น defect หากไม่มี 2xx และข้อมูลไม่ถูกเปลี่ยน
7. token สมมติใน `madeup.txt` ถูกตอบกลับด้วย status 200 และข้อมูล ratings จาก API นี่เป็น defect ด้าน authentication เพราะระบบยอมรับ token รูปแบบถูกต้องแต่ไม่ได้ตรวจว่าเป็น token ของ user จริง ในระบบจริง stranger อาจอ่านข้อมูลส่วนตัวหรือข้อมูลลูกค้าได้ เราใช้ token นี้อ่านอย่างเดียวและไม่สร้างหรือแก้ไขข้อมูล
8. API test ตรวจ status code, JSON และข้อมูลที่ตอบกลับได้ละเอียดและรวดเร็วกว่า ส่วน web-page test ตรวจสิ่งที่ผู้ใช้เห็น การจัดวาง และการโต้ตอบบนหน้าจอ ซึ่ง API test ตรวจไม่ได้
- Step 0.2, status code และ headers: HTTP 200, `Content-Type: application/json` และมี `set-cookie` ของ AWSALB
