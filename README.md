# Семейное древо Андросовых

Интерактивный семейный сайт, задеплоенный на GitHub Pages.

## Открыть сайт

После деплоя: `https://[username].github.io/androsov-family/`

Локально: `python -m http.server 8080` → [http://localhost:8080](http://localhost:8080)

---

## Текущее состояние (v1 — май 2026)

- `index.html` — весь сайт в одном файле
- **97 персон** из `family.ged` (8 поколений, 1812–2026)
- Вкладки: Главная · Древо (canvas) · Фамилии · Поиск
- Фильтр дерева: Все 97 / Прямые предки
- Анимированный фон (canvas orbs + particles)

---

## ПЛАН ПОЛНОЙ РЕКОНСТРУКЦИИ

### Инструменты и ресурсы

| Инструмент | Репозиторий | Назначение | Статус |
|---|---|---|---|
| **SunTree** | [sedrakk/personal-family-tree](https://github.com/sedrakk/personal-family-tree) | SVG радиальная визуализация дерева | Фаза 2 |
| **ged2site** | [nigelhorne/ged2site](https://github.com/nigelhorne/ged2site) | Генерация полноценного сайта из `.ged` | Фаза 3 |
| **Research Skill** | [emaynard/claude-family-history-research-skill](https://github.com/emaynard/claude-family-history-research-skill) | Методология генеалогических исследований | ✅ установлен |
| **Architecture Diagram** | [Cocoon-AI/architecture-diagram-generator](https://github.com/Cocoon-AI/architecture-diagram-generator) | SVG-диаграммы архитектуры в тёмной теме | ✅ установлен |

---

### Фаза 1 — Стабилизация текущего сайта

- [x] Добавить все 97 персон из `family.ged` в PP-массив
- [x] Установить Research Skill в Claude (`~/.claude/skills/family-history-research.md`)
- [x] Установить Architecture Diagram скил в Claude (`~/.claude/skills/architecture-diagram.md`)
- [x] Исправить canvas-дерево: `#vt{position:absolute;inset:0}` — был `position:relative`, из-за чего `offsetWidth=0`
- [x] Добавить фильтр-бар «Все 97 / Прямые» с функцией `setTF()`
- [x] Обернуть инициализацию дерева в `requestAnimationFrame` для корректного timing
- [x] Добавить диалог «Кто ты?» при входе в дерево (localStorage, живые персоны ≥1940)
- [x] Исправить линии связи: реальные родитель-ребёнок из GEDCOM (объект LINKS, 38 семей)
- [x] Исправить `dr`-флаги: сёстры (Ангелина, Юля), тётя/дядя, мачеха, племянница, муж сестры
- [x] Добавить фотографии (Ольга Щербакова, групповое фото) из `assets/` — отображаются в модале
- [x] Проверить корректность dr-флагов всех прямых предков и ближайших родственников
- [ ] Задеплоить на GitHub Pages

**Диаграмма архитектуры v1** (сгенерирована через `/architecture-diagram`):
```
 index.html (single file)
 ├── Animated BG canvas (#bgc) — orbs + particles
 ├── Nav (4 tabs)
 ├── View: Главная  — hero + stats cards
 ├── View: Древо    — canvas (#tc), PP[97], drag/zoom
 ├── View: Фамилии  — 12 surname cards
 └── View: Поиск    — live filter over PP array
```

---

### Фаза 2 — Радиальный вид (SunTree)

Добавить вкладку «Солнце» с SVG-визуализацией по образцу [sedrakk/personal-family-tree](https://github.com/sedrakk/personal-family-tree).

**Архитектура вкладок:**
```
index.html
├── tab: Главная
├── tab: Древо (canvas, текущий)
├── tab: Солнце (SVG радиальное) ← ДОБАВИТЬ
├── tab: Фамилии
└── tab: Поиск
```

**Технические задачи:**
- [ ] Адаптировать `svg_renderer.js` из SunTree под наши данные
- [ ] Конвертер PP-массива → формат SunTree (father/mother/children)
- [ ] Встроить `sun_layout.js` и `geometry.js` (или написать упрощённый аналог)
- [ ] Герман (i51) — центр солнца, Оливия — первое кольцо
- [ ] Интерактивность: клик на персону открывает modal
- [ ] Сгенерировать диаграмму радиальной архитектуры через `/architecture-diagram` для документации

**Формат данных SunTree (адаптировать из PP):**
```
ID, First names, Surname, Gender(M/F), Deceased(Y/N),
Mother ID, Father ID, Partner ID, Birth year, Death year
```

---

### Фаза 3 — Полная генерация через ged2site

[ged2site](https://github.com/nigelhorne/ged2site) — Perl-инструмент для генерации полноценного сайта из `.ged` файла.

**Что даёт:**
- Отдельная страница для каждого человека
- Карты мест (Google Maps)
- Временные линии
- Защита данных живых родственников (автоматически)
- Поддержка изображений
- Мобильная оптимизация
- XML/API для каждого человека

**Шаги:**
- [ ] Установить Perl + зависимости (`cpan Ged2site`)
- [ ] Запустить: `perl ged2site.pl family.ged`
- [ ] Настроить шаблоны (templates/) под стиль Андросовых
- [ ] Добавить фотографии в правильные папки
- [ ] Сгенерировать статический сайт
- [ ] Задеплоить на GitHub Pages (или Vercel)

**Структура выходного сайта (сгенерировать диаграмму через `/architecture-diagram`):**
```
/people/           ← страница каждого человека
/surnames/         ← по фамилиям
/places/           ← карты мест
/dates/            ← временная линия
/index.html
```

---

### Фаза 4 — Расширение данных (Research Skill)

Используя установленный Claude-скил `family-history-research` (GPS-стандарт, FAN-принцип):

**Приоритетные исследования:**
- [ ] Метрические книги 1812–1900, Тамбовская губерния (Ромашкины, Коротеевы)  
  → Источники: FamilySearch Wiki, ГАТО (Государственный архив Тамбовской области)
- [ ] Архивный поиск: репрессия Георгия Саломина 13.12.1958  
  → Источники: Мемориал, ФСБ-архивы, Книги памяти
- [ ] Уточнить дату рождения Алексея Щербакова (≈1912) — несоответствие в `.ged`  
  → Источники: перепись 1926, метрические книги
- [ ] Военные записи Константина Блинникова (1916–1998, ВОВ)  
  → Источники: ОБД «Мемориал», ЦАМО Подольск
- [ ] Восстановить ветку Ромашкиных → Одесса → Европа
- [ ] Добавить фотографии к каждой персоне
- [ ] Дополнить биографии (`st`-поле) для персон без историй

**Методология (по скилу):**
```
1. Gather known info → 2. Define objective → 3. Create research plan
4. Present plan → 5. Execute only after explicit approval
GPS: exhaustive research → citations → analysis → conflict resolution → conclusion
```

---

### Фаза 5 — Семейная книга для Оливии

Финальный артефакт — PDF-книга для Оливии Германовны (05.04.2026):

- [ ] Экспорт данных из ged2site в формат для книги
- [ ] Дизайн обложки и шаблона страниц
- [ ] Включить все фотографии, карты, истории
- [ ] Напечатать к совершеннолетию

---

## Архитектурная диаграмма

> Для генерации полной SVG-диаграммы архитектуры выполни: `/architecture-diagram`
> Описание системы: "Androsov Family Website — single HTML file, canvas tree with 97 people across 8 generations, animated background, 4 tabs (Home/Tree/Surnames/Search), family.ged GEDCOM source"

---

## Структура файлов

```
FAMILY/
├── index.html              ← текущий сайт (v1)
├── family.ged              ← исходные данные (97 персон)
├── README.md               ← этот файл
├── assets/
│   ├── photo_I517_olga_solomina.jpg
│   └── photo_group_taya_family.jpg
├── fonts/
│   ├── Bodoni_Z37_M_Compressed_Bold.otf
│   └── ...
├── bg.mp4                  ← видеофон (не используется в v1)
└── mnt/user-data/outputs/
    ├── androsov-final-deploy/   ← v1 финальная версия
    ├── androsov-cinematic/      ← v2 с видеофоном (в разработке)
    └── androsov-v2,v4,v6/       ← старые версии (пергаментный стиль)
```

## Деплой на GitHub Pages

```bash
git add .
git commit -m "Семейное древо Андросовых v1 — 97 персон"
git push origin main
```

Включить Pages: Settings → Pages → Branch: main / (root) → Save

---

## Семья в цифрах

- 97 персон в базе
- 8 поколений
- 214 лет истории (1812–2026)
- 12 фамилий
- Создано для Оливии Германовны Андросовой (05.04.2026)
