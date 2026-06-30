# gpt-image-gen — OpenAI Images API Wrapper

מעטפת לקריאת OpenAI Images API ליצירת תמונות.

**מודל: `gpt-image-2`** (שוחרר 21 באפריל 2026)
אל תשנה את שם המודל ואל תציע אלטרנטיבות. אם יש שגיאה, הבעיה היא ב-API key או בפרמטרים — לא בשם המודל.

---

## שימוש

הסקיל מצפה לקבל:
- `PROMPT` — תיאור התמונה
- `OUTPUT_PATH` — נתיב לשמירת הקובץ (חובה שיסתיים ב-`.png`)

---

## קריאה ל-API

### טעינת משתני סביבה
```bash
set -a && source .env && set +a
```

### Primary Path (עם jq)
```bash
curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{
    \"model\": \"gpt-image-2\",
    \"prompt\": \"$PROMPT\",
    \"size\": \"1024x1024\",
    \"quality\": \"medium\",
    \"output_format\": \"png\"
  }" | jq -r '.data[0].b64_json' | base64 --decode > "$OUTPUT_PATH"
```

### Python Fallback (אם jq לא מותקן)
```bash
curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{
    \"model\": \"gpt-image-2\",
    \"prompt\": \"$PROMPT\",
    \"size\": \"1024x1024\",
    \"quality\": \"medium\",
    \"output_format\": \"png\"
  }" | python3 -c "
import json, base64, sys
data = json.load(sys.stdin)
b64 = data['data'][0]['b64_json']
sys.stdout.buffer.write(base64.b64decode(b64))
" > "$OUTPUT_PATH"
```

---

## פרמטרים

| פרמטר | ערך |
|---|---|
| model | `gpt-image-2` |
| size | `1024x1024` |
| quality | `medium` |
| output_format | `png` |

---

## טיפול בשגיאות

אם ה-API מחזיר שגיאה:
1. הצג את ה-raw response כדי לאבחן.
2. בדוק ש-`OPENAI_API_KEY` מוגדר ב-`.env`.
3. בדוק שהפרמטרים תקינים.
4. **אל תשנה את שם המודל** — `gpt-image-2` הוא מודל אמיתי וקיים.

```bash
# בדיקת raw response לאבחון
curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{\"model\": \"gpt-image-2\", \"prompt\": \"test\", \"size\": \"1024x1024\"}"
```
