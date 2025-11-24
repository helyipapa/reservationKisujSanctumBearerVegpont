## API végpontok (ReservationKisujSanctumBearer)

# Általános:
- Base URL (lokálisan): http://127.0.0.1/reservationKisujSanctumBearer/public/api
- Auth: Laravel Sanctum Bearer token szükséges az authentikált végpontokhoz.
- Hibaválaszok: 401 = Unauthorized, 403 = Forbidden, 422 = Validation error.

# Regisztráció
- POST /api/register
- Body (JSON):
{
  "name": "Név",
  "email": "email@example.com",
  "password": "jelszo"
}
- Sikeres válasz: 201 Created
- Példa válasz (201):
{
  "message": "User registered successfully",
  "user": {
    "id": 1,
    "name": "Név",
    "email": "email@example.com",
    "is_admin": false,
    "created_at": "2025-11-20T08:00:00.000000Z",
    "updated_at": "2025-11-20T08:00:00.000000Z"
  }
}

# Bejelentkezés
- POST /api/login
- Body (JSON):
{
  "email": "email@example.com",
  "password": "jelszo"
}
- Sikeres válasz: 200 OK
- Példa válasz:
{
  "access_token": "TOKEN_HERE",
  "token_type": "Bearer"
}

# Kijelentkezés
- POST /api/logout
- Header: Authorization: Bearer {access_token}
- Sikeres válasz: 200 OK
- Példa:
{ "message": "Logged out" }

# Foglalások listázása
- GET /api/reservations
- Header: Authorization: Bearer {access_token}
- Viselkedés:
  - Admin: visszaad minden foglalást (200).
  - Normál felhasználó: csak a saját foglalásait (200).
- Példa válasz (200):
[
  {
    "id": 1,
    "user_id": 1,
    "reservation_time": "2025-12-01 08:48:00",
    "guests": 4,
    "note": "Szülinapi vacsi",
    "created_at": "...",
    "updated_at": "..."
  },
  ...
]

# Egy foglalás lekérése
- GET /api/reservations/{id}
- Header: Authorization: Bearer {access_token}
- Válaszok:
  - 200 OK + foglalás JSON (ha jogosult)
  - 403 Forbidden (ha nem jogosult)
  - 404 Not Found (ha nem létezik)

# Foglalás létrehozása
- POST /api/reservations
- Header: Authorization: Bearer {access_token}
- Body (JSON):
{
  "reservation_time": "2025-12-01 19:00:00",
  "guests": 4,
  "note": "Megjegyzés (opcionális)"
}
- Sikeres válasz: 201 Created
- Példa válasz (201):
{
  "id": 12,
  "user_id": 3,
  "reservation_time": "2025-12-01 19:00:00",
  "guests": 4,
  "note": "Megjegyzés",
  "created_at": "2025-11-20T09:00:00.000000Z",
  "updated_at": "2025-11-20T09:00:00.000000Z"
}

# Foglalás frissítése
- PUT /api/reservations/{id} vagy PATCH /api/reservations/{id}
- Header: Authorization: Bearer {access_token}
- Body (JSON) — lehet részleges:
{
  "note": "Új megjegyzés",
  "guests": 5
}
- Válasz: 200 OK + frissített foglalás JSON vagy 403 ha nincs jogosultság.

# Foglalás törlése
- DELETE /api/reservations/{id}
- Header: Authorization: Bearer {access_token}
- Válaszok:
  - 200 OK + { "message": "Foglalás törölve." } (sikeres törlés)
  - 403 Forbidden (ha nincs jogosultság)
  - 404 Not Found

Megjegyzések:
- Validációs hibák esetén a válasz 422 és a body tartalmazza a mezőnkénti hibákat.
- Token generálás: /api/login ad vissza access_token-et, ezt kell megadni Authorization fejlécben.
- Teszteléshez ajánlott Postman vagy curl használata.

