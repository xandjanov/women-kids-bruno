# wk-api-docs

WK API — Go (Gin) + Bun + PostgreSQL kolleksiyasi. **OpenCollection** formatida
(`opencollection.yml` + `.yml` so'rovlar), Bruno'da ochiladi.

```
opencollection.yml      kolleksiya sozlamalari + to'liq hujjat
environments/           WK Api.yml  (base_url, public_url, token, user_token)
_samples/sample.png     multipart so'rovlar uchun namuna fayl — o'chirmang
Admin/                  xodimlar paneli   {{base_url}}  + {{token}}
Public/                 sayt uchun ochiq  {{public_url}} — token kerak emas
User/                   sayt foydalanuvchisi {{base_url}} + {{user_token}}
```

Har papkaning va har so'rovning o'z hujjati bor — Bruno'da o'ng tarafdagi
hujjat panelini oching. Umumiy qoidalar, rollar, til, token oqimi va fayl
formatlari `opencollection.yml` ichidagi hujjatda.

## Boshlash

1. Bruno'da kolleksiyani oching, yuqori o'ng burchakdan **`WK Api`** environment'ini tanlang.
2. `Admin / auth / login` — `token` avtomatik yoziladi.
3. `User / auth / register verify` yoki `login verify` — `user_token` avtomatik yoziladi.
