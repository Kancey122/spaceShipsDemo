# SpaceShips Demo — контекст проекта

## Репозиторий
- GitHub: `kancey122/spaceshipsdemo`
- Рабочая ветка: `claude/check-repo-file-HwURY`

---

## Структура файлов

```
spaceShipsDemo/
├── spaceship_builder.html   # Главный и единственный HTML-файл
└── parts/                   # OBJ-части корабля
    ├── hull_main.obj        # Корпус (5×0.8×2, сужается к носу)
    ├── wing_left.obj        # Левое крыло (swept delta)
    ├── wing_right.obj       # Правое крыло (зеркало левого)
    ├── engine_pod.obj       # Двигатель (8-гранный конус)
    ├── cockpit_dome.obj     # Кабина (гексагональный купол)
    └── weapon_cannon.obj    # Орудие (длинный тонкий ствол)
```

---

## Что делает spaceship_builder.html

3D-редактор кораблей в браузере на **Three.js r128**. Один файл, без сборки.

### Зависимости (CDN)
- `three.min.js` r128 — cdnjs
- `OBJLoader.js` r128 — jsdelivr

### UI-панели
| Панель | Содержимое |
|--------|-----------|
| Левая | Библиотека блоков (кнопки), секция "OBJ Части" + кнопка загрузки .obj, форма "Добавить свой блок" |
| Центр | Three.js canvas (3D сцена) |
| Правая | Свойства выбранного объекта: позиция X/Y/Z, вращение Y, кнопка удалить |
| Топбар | Режимы Разместить/Выбрать, Отменить, Очистить, Сохранить/Загрузить (JSON), Сброс камеры |
| Статусбар | режим · кол-во блоков · активный блок · углы камеры |

### Встроенная библиотека блоков (BLOCKS)
16 предустановленных блоков с формами: `box`, `cylinder`, `sphere`, `cone`, `wedge`.
Примеры: Корпус XL/L/M/S, Кабина, Крыло Л/П, Двигатель L/S, Орудие, Турель, Щит, Сенсор, Ангар, Киль, Под-ракета.

### Формы геометрий
```
box       → BoxGeometry(w, h, d)
cylinder  → CylinderGeometry, повёрнут rotation.x = PI/2
sphere    → SphereGeometry
cone      → ConeGeometry, повёрнут rotation.x = PI/2
wedge     → BoxGeometry (визуально как box)
obj       → BufferGeometry из OBJLoader, DoubleSide материал
```

### OBJ-система
- `objGeometries[key]` — хранит загруженные BufferGeometry
- `objBBoxMin[key]` — хранит `boundingBox.min.y` для посадки на землю
- `mergeObjGroup(group)` — сливает все mesh из OBJ-группы в одну BufferGeometry (toNonIndexed + applyMatrix4 + computeVertexNormals)
- `loadObjFromText(text, key, label, color)` — парсит через THREE.OBJLoader
- `handleObjUpload(event)` — обработчик `<input type=file>`
- `tryPreloadParts()` — fetch('parts/X.obj') для каждого из 6 пресетов; работает только через HTTP-сервер (не file://)
- После загрузки OBJ блок добавляется в `BLOCKS` с `shape:'obj'` и `objKey`

### Посадка OBJ на землю (makeMesh)
```js
else if(b.shape==='obj') mesh.position.y = -(objBBoxMin[b.objKey] || 0);
```

### Камера
Orbit-камера: ПКМ — вращение, колесо — зум, СКМ — панорама.
Состояние: `{ theta, phi, dist, tx, tz }`.

### Сохранение/загрузка сцены
JSON-файл с массивом `placed[]` (blockId, px, py, pz, ry) и массивом `blocks[]`.

### Горячие клавиши
`G` — переключить режим, `Ctrl+Z` — отмена, `Del` — удалить, `Escape` — снять выделение,
стрелки — двигать выбранный, `R` / `Shift+R` — вращать на ±90°.

---

## Что было сделано в этом чате
1. Прочитан и изучен существующий `spaceship_builder.html`
2. Создана папка `parts/` с 6 OBJ-файлами (вручную написаны вершины и грани)
3. В HTML добавлены: OBJLoader CDN, секция "OBJ Части" в левой панели, все JS-функции для OBJ
4. Всё закоммичено и запушено в ветку `claude/check-repo-file-HwURY`
