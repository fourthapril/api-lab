# API Lab

โปรเจกต์นี้เป็นการทดสอบ QuickPizza API ด้วย Python, `requests` และ `pytest`

## การติดตั้งและรันทดสอบ

```powershell
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
pytest -v
```

สร้างผู้ใช้ใหม่และข้อมูลที่ใช้ใน test ผ่าน fixtures ทุกครั้ง เพื่อให้ test แยกจากกันและไม่แก้ไขข้อมูลของผู้ใช้อื่น

## Input domain model: POST /api/ratings

ตารางนี้แบ่ง input space ตามกฎของ API และใช้ base choice เป็นค่าที่พบบ่อยที่สุด

| Characteristic | Blocks | ค่าที่ใช้ทดสอบ | Test ที่ครอบคลุม |
|---|---|---|---|
| C1: จำนวนดาว | ต่ำกว่า 1, 1 ถึง 5 (base), มากกว่า 5 | `0`, `3`, `6` | `test_base_choice_is_saved`, `test_stars_out_of_range` |
| C2: ชนิดของดาว | จำนวนเต็ม (base), ทศนิยม, ข้อความ, ไม่มี field | `3`, `3.5`, `"3"`, ไม่มี field | `test_base_choice_is_saved`, `test_stars_with_wrong_type_or_missing_are_rejected` |
| C3: pizza | มีอยู่ (base), ไม่มีอยู่ | `pizza_id`, `99999999` | `test_base_choice_is_saved`, `test_rating_for_missing_pizza_is_rejected` |
| C4: token | token ของผู้ใช้เอง (base), ไม่มี token, token ผิดรูปแบบ | `auth`, ไม่มี header, token สั้น | `test_base_choice_is_saved`, `test_rating_without_token_is_rejected`, `test_rating_with_short_token_is_rejected` |
