# Карта проекта для агентов

Рабочая среда на этой машине состоит из четырёх разных каталогов. Не смешивайте
их содержимое и не считайте один Git-репозиторием другого:

- `G:\SS\Silent-Storm` (этот репозиторий) — исторические исходники, сборки и
  общий план. `REFACTOR.md` — источник цели и этапов 0–8. Исторические деревья
  `Soft/Andy/**`, `Versions/**` и прочие оригинальные материалы сохраняйте
  неизменными; порт разрабатывается не здесь.
- `G:\SS\Silent-Storm-Reconstruction` — отдельный рабочий Git-репозиторий:
  CMake-сборка, код игры и нативные замены внешних библиотек, диагностика и
  автотесты. Проверяйте его `git status` отдельно. Не переносите коммиты между
  репозиториями без явной задачи.
- `G:\SS\lab` — локальная лаборатория: изолированная копия оригинальных данных
  `baseline`, toolchain, старые архивы `builds`, запуски `runs` и извлечённые
  эталонные потоки. Каталоги `build-x86`/`build-x64` на G: удалены как
  воспроизводимые сборки; новые создавайте здесь при необходимости. Это не Git. Лицензионные
  ресурсы, DLL, сохранения, дампы и скриншоты не добавляйте в репозитории.

- `D:\SS-lab` — удалён пользователем; не считайте его доступной лабораторией
  и не выносите новые операции или артефакты за пределы G:. На G: освобождено
  место удалением старых воспроизводимых сборок и бинарников промежуточных
  архивов этапа 2. Их `source.zip`, журналы, конфигурации и хеши сохранены,
  но такие архивы уже не запускаемы. Ресурсы берите из
  `G:\SS\lab\baseline\res` и после прогона проверяйте их SHA-256.

Для Linux-проб оригинальных ресурсов недостаточно скопировать 23 файла
`*.res`: игра сначала ищет отдельные файлы в 14 каталогах `res` (2421
файл в текущей поставке), а затем читает пакет. В частности, 38 файлов
`res/animations` дают 36 переопределений и два ID, которых нет в
`Animations.res`. Игровые `FaceGenHead.gdp` и `FaceGenHead.mmt` лежат
непосредственно в `res`. Пакето-ориентированный Linux-прогон не выдавайте
за проверку полного эффективного набора данных. Сверку анимаций и точный
способ запуска см. в
`../Silent-Storm-Reconstruction/diagnostics/NATIVE-ANIMATION-RESOURCES.md`.

В `G:\SS\lab\runs` старые запуски сохраняют уникальные сейвы, скриншоты,
CSV, дампы, корпуса сравнения лиц и отладочные журналы. 136 проверенных
совпадающих копий `game\res` и 2070 повторяющихся EXE/DLL/PDB/`game.db` в
устаревших LabRun удалены (ещё 35,18 ГиБ на G:). Эталон Steam и четыре
опорных запуска оставлены целиком. Запускать остальные старые LabRun
повторно без восстановления ресурсов и бинарников нельзя. Для нового теста
создайте новый LabRun с `-LinkResources`, а не подменяйте данные старого.
Каталоги `pre_cutscene` и `stational weapons` оставлены как пользовательские
воспроизведения багов.

Эталон наблюдаемого поведения — оригинальный Steam EXE с соответствующими
данными, запущенный в отдельном LabRun. Восстановленные исходники и x86-сборка
служат для понимания реализации и дополнительных тестов, но сами по себе
не доказывают совпадение со Steam. Gold/Patch PDB пригодны для поиска символов;
при переносе выводов на Steam проверяйте адрес и фактическое поведение там.
Steam не содержит harness: внутренние значения можно снять остановками CDB
и чтением памяти, а игровые действия выполнять обычным вводом. Пример —
`../Silent-Storm-Reconstruction/diagnostics/PORTABLE-GAME-LINKS.md`.
Прямое чтение нового x64-сейва оригинальным Steam EXE через меню
задокументировано в
`../Silent-Storm-Reconstruction/diagnostics/STEAM-SAVE-ORACLE.md`;
это не проверка всех динамических решений ИИ.
Кросс-совместимость сейвов со Steam — диагностический приём, а не отдельное
продуктовое требование. Полный динамический паритет во всём игровом контуре
(Lua-сценарии, ИИ, бой, взрывы, разрушения, маршруты и прочее) относится к
этапу 7, а не к gate этапа 2. В этапе 2 используйте Steam адресно для
неоднозначных контрактов, но проверяйте переносимость, загрузку данных,
сериализацию и регрессии ядра на целевых архитектурах. Успешная загрузка
сейва сама по себе не доказывает совпадение игровых решений.

## Где лежат цели и актуальные результаты

- `REFACTOR.md` здесь — общий объём и критерии завершения этапов. Этап 0 закрыт
  только для согласованных данных и сценариев; этап 1 закрыт по согласованному
  объёму Windows/Linux без проверки macOS. Следующая работа — этап 2,
  переносимое 64-битное ядро и загрузка данных; полный поведенческий паритет
  со Steam отложен до этапа 7.
  Восстановленная 32-битная игра больше не целевая сборка: x86-оригинал и
  старые x86-пробы нужны лишь как сравнительный оракул.
- `../Silent-Storm-Reconstruction/diagnostics/PORTABILITY-MEDIA.md` —
  свидетельства, команды и ограничения этапа 1. SDL3 + bgfx проверены только
  отдельной пробой `../Silent-Storm-Reconstruction/probes/sdl3-bgfx/README.md`,
  не встроены в `Game.exe`; macOS и физический Linux GPU ещё не проверены.
- `../Silent-Storm-Reconstruction/diagnostics/PORTABLE-PACKAGE.md`,
  `../Silent-Storm-Reconstruction/diagnostics/STRUCTURE-WIRE.md` и
  `../Silent-Storm-Reconstruction/diagnostics/PORTABLE-STRUCTURE-CHUNKS.md`
  — проверки этапа 2: переносимый индекс `.res`, 32-битные дисковые ID
  объектного графа, заголовки верхних и вложенных чанков, объектную таблицу
  `game.db`.
  Эти правки проверены на чистой Windows x64 с загрузкой старого сейва и
  отдельным Linux-пробником; сам декодер таблиц описан ниже.
- `../Silent-Storm-Reconstruction/diagnostics/PORTABLE-GAME-DB.md` — переносимый
  декодер всех игровых колонночных таблиц release-v1 `game.db` и сверка хешей
  значений/отношений с текущим x64-загрузчиком. Для v1 он подключён к игре,
  но это ещё не Linux-игра и не паритет с историческим x86-релизом.
- `../Silent-Storm-Reconstruction/diagnostics/PORTABLE-USER-PATHS.md` — политика
  пользовательского каталога и тесты на Windows/Linux. Игра уже пишет сейвы
  отдельно от ресурсов; старый `save\\` — только источник первого импорта.
  Windows-каталог и файловые операции сейвов используют wide API, в том числе
  проверены в LabRun с кириллическим путём. Пользовательский `config.cfg` и
  обычные BMP-скриншоты также пишутся туда; `autoexec.cfg` и `input.cfg`
  остаются ресурсами игры. Новые имена слотов за пределами ACP кодируются
  внутри игры как UTF-8 с маркером, перечисляются и загружаются через wide
  файловый путь; старые ACP-имена остаются совместимыми. Профили используют
  то же кодирование; `game_profile` хранится в конфиге как ASCII `S2U8:<hex>`,
  а старые непомеченные значения читаются. Два запуска чистого архива
  подтвердили восстановление активного Unicode-профиля и загрузку его слота.
  Визуальный рендеринг таких имён в меню ещё не проверен.
- `../Silent-Storm-Reconstruction/diagnostics/ARM64-PORTABLE-CORE.md` —
  кросс-сборка переносимых модулей для ARM64, QEMU-тесты с ASan/UBSan и
  сверка значений/хешей `game.db` и `Fonts.res` с Windows x64. Полная
  игра на ARM64 этим не проверена.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-LUA-LINUX.md` —
  сборка и исполнение оригинального Lua-рантайма и стартовых скриптов на
  Linux x64/ARM64. Это не перенос игровых Lua-привязок, игрового цикла
  или сохранения Lua-состояния; этап 2 остаётся открытым.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-STREAMS-LINUX.md` —
  сборка штатных потоков `FileIO`, тест Unicode-пути и чтение оригинального
  `Fonts.res` на Windows x64, Linux x64/ARM64. Продолжение — ниже.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-SAVE-MANAGER-LINUX.md` —
  файловый слой профилей и слотов Linux: Unicode-имена, запись и загрузка
  заголовка через штатные потоки, отказ от симлинков. Это ещё не загрузка
  целой миссии или игровой UI на Linux.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-SCENARIO-SIGNATURE.md` —
  арифметика игровых битовых сигнатур зон/улик и граница теста: индексы
  31/32/63/64 проверены на трёх архитектурах, но Lua-ветвление миссии
  на Linux ещё не исполнялось.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-SCENE-CORE-SPLIT.md` —
  подлинные CPU-методы `IPart`/комбайнера и регистрация `CNonePart`
  вынесены из D3D-модулей; одинаковые байты сериализации на Windows,
  Linux x64 и ARM64. Подлинный `CLightGroup` теперь виден мировому коду,
  и Linux-линковка проходит без фиктивного cast; до headless-миссии ещё
  остаётся запуск игрового цикла и загрузка состояния миссии.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-WORLD-INIT.md` —
  исполняемая на Windows x64 и Linux x64/ARM64 загрузка исходной `game.db`,
  создание `CWorld` и выполнение четырёх стартовых Lua-файлов; проверка
  Windows-пути и регистра компонентов для чтения на Linux. Команды и
  ограничение этой начальной проверки указаны в документе.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-WORLD-MISSION-LINUX.md` —
  headless-создание миссий 218, 810, 2223, 3829, 4526 и 5240 штатным
  `CWorld::CreateRandom` (3829, 4526 и 5240 — прямые корни игровых сценариев), запуск
  `RunPostInit` и первых сегментов, включая одну карту с двумя юнитами и
  прикреплённым Lua-скриптом. Расширенный `NativeScenarioRootWorlds` получает
  52 активных корня из исходной `game.db` и запускает каждый в отдельном
  процессе: Windows x64 и Linux x64 прошли единым CTest, ARM64/QEMU — девятью
  непересекающимися диапазонами того же скрипта под ASan/UBSan (без LSan).
  Обычный ARM64/QEMU CTest на версии с обеими правками прошёл 123/123
  зарегистрированных тестов; два новых прицельных сценария запускались
  напрямую из-за лабораторной раскладки ресурсов.
  Это проверка старта мира, а не прохождения миссии или паритета ИИ со Steam.
  `NativeScenarioRootPartyWorlds` расширяет тот же список корней настоящей
  партией и 220 обновлениями мира с подтверждением ожидаемых Lua-команд UI;
  после двух исправлений все 52 корня прошли на Windows x64, Linux x64
  и ARM64/QEMU (на последних двух под ASan/UBSan, без LSan на QEMU).
  `NativeWorldMission3832PartyUIAck` и `NativeWorldMission6814PartyUIAck`
  — быстрые регрессии на найденные UBSan-ошибки режима стрельбы и
  неинициализированного `SItem`. Запуск: `ctest -C RelWithDebInfo
  --output-on-failure -R 'NativeScenarioRootPartyWorlds|NativeWorldMission3832PartyUIAck|NativeWorldMission6814PartyUIAck'`
  в Windows CMake build, на Linux без `-C` (если `S2_GAME_DIR` содержит
  оригинальные `game.db` и `res/Waypoints.res`); для раздельных диапазонов
  и ARM64/QEMU см. команду в указанном диагностическом документе.
  `NativeScenarioRootPartySaveWorlds` дополнительно сохраняет каждый из 52
  миров, читает его и продолжает десять обновлений; пройден на Windows x64,
  Linux x86-64 под ASan/UBSan/LSan и ARM64/QEMU под ASan/UBSan без LSan
  (девять диапазонов, по одному процессу на корень). Короткий
  `NativeWorldMission5247PartySave` защищает исправление ссылки на эффект
  юнита: `SBoundEffect` теперь пишет ID записи БД и время, а не сырой
  указатель. Запуск CTest — `-R 'NativeScenarioRootPartySaveWorlds|NativeWorldMission5247PartySave'`;
  для scratch-ресурсов в корне запускайте `RunScenarioRootWorlds.cmake` с
  `PARTY_SAVE_MODE=ON`, `SAVE_DIR` и диапазоном, как описано в диагностике.
  `NativeWorldMission5240` защищает звук шага с бронёй типа 5; в оригинальной
  базе соседнее с пятью радиусами поле `pSound` у записи ID 5 нулевое,
  поэтому переносимый accessor возвращает 0 без чтения за массивом.
  `NativeGameDatabaseLoadTests` проверяет это значение и прежнюю адресацию
  допустимых типов 0–4 на трёх архитектурах. `NativeWorldMission810UIAck` отдельно проводит
  Lua через ожидания UI-команд, подтверждая их ID без настоящего интерфейса,
  и проверяет достижение конца вступительной последовательности. В
  `NativeWorldMission810PartyUIAck` добавлена настоящая игровая партия с
  героем и `CSequenceCommander`; тест видит, что скриптовый
  `UnitShootPrepare` приводит к исполнителю `pers1` (`CExecQueue`). Команды,
  `NativeWorldMission810PartyShot` после вступления выполняет управляемый
  выстрел по герою: проверяет исполнителя, патрон, AP, попадание и события
  атаки/пули. `NativeWorldMission810PartyShotSave` после выстрела делает
  файловый round-trip `CWorld` и `SerializeShared` через штатный сериализатор
  и сверяет HP, патроны, AP, время и позднюю Lua-функцию, затем продвигает
  восстановленный мир ещё на десять сегментов и выполняет второй управляемый
  выстрел уже после загрузки с проверкой патрона, AP и событий; урон
  второго выстрела не обязателен, так как `ScriptToHit=100` не пробивает
  укрытие. Первый выстрел до сохранения обязан нанести урон.
  `NativeWorldMission810PartyShotSlot` повторяет этот путь через активный
  `CSaveManager`-слот: копирует его в именованный слот, очищает и загружает
  обратно, а затем проверяет автономный выстрел ИИ на загруженном мире.
  `NativeWorldMission810PartyExplosionSave` вызывает игровой взрыв по зданию
  и сверяет воксельную сетку до/после и после загрузки по счётчикам и хешу.
  `NativeWorldMission810PartyGrenadeSave` пропускает обычную гранату через
  штатный менеджер взрывов и проверяет повреждение здания и точный
  round-trip сетки; хеш результата самого взрыва может различаться между
  запусками из-за порядка объектов в указательном хеше. Это не паритет Steam.
  `NativeWorldMission810PartyGrenadeFlightSave` дополнительно проводит
  контактную гранату через штатный скриптовый бросок, баллистику и физическое
  столкновение; без рендера контактный запал теперь проверяется на игровом
  сегменте. Он не покрывает инвентарь/AP и анимацию игрока.
  `NativeWorldMission810PartyGrenadeInventorySave` проверяет обычную команду
  броска экипированной гранаты (`CCmdShootTile` + обязательный
  `CCmdContinue`), расход предмета/20 AP, разрушение здания и сохранение
  вокселей. UI и визуальную анимацию он не проверяет.
  `NativeWorldMission810PartyEngGrenadeInventorySave` повторяет игровой
  маршрут для инженерной гранаты №2 с достаточным навыком инженерии; если
  первый поворот прерван `TBS_CANCEL_ACTION`, тест подаёт приказ повторно.
  Он проверяет расход предмета/20 AP, повреждение и точное сохранение
  вокселей, но не устанавливает причину уведомления и паритет со Steam.
  `NativeWorldBase5376UIAck` отдельно проверяет развёртывание героя в базе,
  три UI-подтверждения Lua-скрипта и отказ штатной команды выстрела при
  `NoAttack=1`; Linux LeakSanitizer проверяет освобождение маршрутов ИИ,
  отклонённых для не-AI юнитов. Это не полноценный UI-прогон базы.
  `S2_USER_DATA_DIR` в CTest направлен в каталог сборки; baseline и настоящий
  пользовательский слот не затрагиваются. Найденные UB и границы проверки
  там; это ещё не вызов `CMission::SaveWorld` из игры и не паритет со Steam.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-STRUCTURE-LINUX.md` —
  уже перенесённый на Linux x64/ARM64 штатный `CStructureSaver`, объектный
  граф, packed-кодек, сохранение Lua-состояния и геометрические поля.
  Проверен также Clang x64; классы мира и игровой цикл ещё не включены.
  Читайте этот документ после записи о потоках выше.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-TRANSFORM-LINUX.md` —
  сборка оригинальной математики камеры/границ `Main/Transform.cpp` на Linux
  x64/ARM64, Windows x64 и дополнительная численная сверка с x86. Рендерер,
  классы мира и игровой цикл на Linux этим ещё не перенесены.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-TERRAIN-SPLINE-LINUX.md` —
  используемый игрой сплайн сглаживания высоты `Main/BetaSpline.cpp`, его
  сборка на Windows/Linux x64/ARM64 и численная сверка с x86. Полный кеш
  этажей и маршрутизатор мира на Linux пока не перенесены.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-DG-LINUX.md` —
  штатный `DG` (кадры, версии данных, отложенное удержание объектов),
  необходимый классам мира и кешу высот. Отдельная Linux-цель не является
  переносом полного графа мира.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-TERRAIN-DATA-LINUX.md` —
  текущая граница данных рельефа: оригинальный `TerrainInfo.cpp` выполняется
  на Windows и Linux x64/ARM64, в том числе с типизированным `game.db`;
  тест проверяет регионы `DG`, ссылки на материал/броню и round-trip
  `STerrainInfo` через игровой сериализатор. Это ещё не загрузка карты миссии.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-HEIGHT-LAYERS-LINUX.md` —
  тест штатного `CHeightLayers` (поле высот, этажи и сериализация) на
  Windows/Linux x64/ARM64. Отдельный `NativeHeightNetworkTests` уже
  исполняет `ComputeLayers` с настоящим `CPathNetwork` на синтетическом
  тайле; это ещё не проверка маршрутов загруженной миссии.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-AI-GRID-LINUX.md` —
  перенос оригинального `aiGrid.cpp` и прямых AI-зависимостей: сборка на
  Linux x64/ARM64, исполняемые тесты AI-журнала и высотной сетки.
  `CheckItemsBreakGlass` подключена из настоящего `wOSBase.cpp`, не из
  заглушки. Здесь же временный UBSan-vptr gate и исправление UB в `CPool`.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-AI-LOGIC-LINUX.md` —
  история первой Linux-компиляции оригинального `CAILogic`. Текущий
  `NativeAILogicTests` уже исполняется на Windows x64 и Linux x64/ARM64
  со связанной мировой зависимостью; это не проверка решений ИИ в миссии.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-COMMAND-BRIDGE-LINUX.md` —
  общий Windows/Linux-мост `CCmdSetCommand`, исполняемый тест пропускаемой
  и обязательной команд на x86/x64/ARM64, а также граница до `CUnitServer`.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-UNIT-SERVER-LINUX.md` —
  полная Linux-компиляция исходного `CUnitServer` на x64/ARM64 и история
  первоначальной границы линковки. Общий граф теперь связывается, но
  решений ИИ в живой Linux-миссии эта проверка не доказывает.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-UNIT-EXECUTION-LINUX.md` —
  продолжение командного контура: оригинальные `CDumbUnitServer`, состояния
  и аниматор юнита компилируются на Linux x64/ARM64, но пока не связаны в
  исполняемую миссию. Там перечислены оставшиеся группы зависимостей.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-WORLD-EXECUTION-LINUX.md` —
  текущий пакет путей, атак и объектов мира: Windows x64 и Linux x64/ARM64
  матрицы, тест досягаемости на x86/x64/ARM64 и историческая граница
  линковки; более поздний тест создания мира описан в `NATIVE-WORLD-INIT.md`.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-WORLD-EVENTS-SCENARIO-LINUX.md` —
  пакет игровых событий, ракет и графа сценария с Lua-мостом, проверенная
  матрица Windows/Linux и открытые UI/мировые зависимости.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-WORLD-GAMEPLAY-LINUX.md` —
  Linux-компиляция игровых зданий, рельефа, инвентаря, диалогов,
  последствий взрывов, RPG-зданий и переносимого таймера; матрица
  Windows/Linux и оставшиеся символы диагностической линковки.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-SCENE-DATA-LINUX.md` —
  общий алгоритм геометрии сцены, AI-геометрия, скелетные данные и
  декали; тест паритета x86/x64/ARM64 и оставшаяся граница линковки.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-SCRIPT-BINDINGS-LINUX.md` —
  игровые Lua-привязки, скриптовая логика ИИ и историческая граница
  линковки на момент их первого переноса.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-LUA-USED-SURFACE.md` —
  проверка используемости оконных Lua-функций по исходной `game.db`,
  разделение регистраций Windows/Linux и текущая граница этапа 2.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-HEAD-RESOURCES-LINUX.md` —
  сквозной разбор `Heads.res`, нативные CPU-аниматоры и игровые
  последовательности из `Sequences.res`/`tree.mma` на Windows x64/Linux
  x64/ARM64; живые ленивые загрузчики, `CHeadInfo` и его сериализация из
  оригинальной `game.db`; граница до графических классов сцены.
- `../Silent-Storm-Reconstruction/diagnostics/PORTABLE-HEAD-SEED.md` —
  расчёт seed генерируемой игрой головы из адреса юнита: одинаковое
  сворачивание старших 32 бит на Windows x64, Linux x64 и ARM64.
  `NativeHeadSeedTests` проверяет арифметику без игровых ресурсов; это
  не доказательство одинаковых лиц между запусками или рендерами.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-CLANG-WORLD.md` —
  дополнительная Linux/Clang-проверка настоящего headless `CWorld`, Lua,
  боя и сохранений с ASan/UBSan. Там описаны явный GCC 11 toolchain для
  Clang 14 на `artanis.c.ibgene.org`, `-no-pie` для стабильного запуска
  санитайзера и исправления регистраций `game.db`/стоимости пробития.
  Это не проверка графического Linux EXE или всей Clang-матрицы.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-VOXEL-HASH-LINUX.md` —
  исправление усечения указателя в хеше игровых объектов взрыва,
  тест ширины адреса, тест настоящего воксельного рендера и открытая
  проверка порядка урона против Steam.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-SCENE-SERIALIZATION-LINUX.md` —
  перенос регистраций объектов сцены, одинаковый сериализованный
  `CCInt` на x86/x64/ARM64 и граница до графических частей декали.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-ANIMATION-RUNTIME-LINUX.md` —
  штатные скелетная/путевая анимация, частицы и высотный сэмплер на Linux,
  исполняемый тест интерполяции пути и оставшаяся граница до живого мира.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-CONSOLE-RPG-LINUX.md` —
  Linux-путь пользовательского `config.cfg`, консольные переменные и часть
  настоящих RPG-правил (урон, предметы, юнит, перки); тесты и граница до
  живой миссии описаны там.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-GAME-DB-LINUX.md` —
  загрузка оригинального `game.db` через игровые `BasicDB`/DBFormat на Linux
  x64/ARM64, сверка всех 155 таблиц, 239 310 ID и значений материалов;
  `NativeGameDatabaseLoadTests <game.db> --nonascii` показывает поля с
  не-ASCII без раскрытия текста. Узкие строки русской базы импортируются
  в CP1251 без смены кодировки путей; документ содержит команды тестов,
  диагностику оставшихся ссылок и границу относительно игрового цикла.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-MAP-DB-LINUX.md` —
  `NativeMapDatabaseTests`: все типизированные шаблоны и варианты карт из
  оригинального `game.db`, их игровые поля и связи; x86/x64/ARM64-сверка.
  `--roots` выводит граф от `GlobalMaps`/`ScenarioZones`: 52 корня и 985
  потенциально достижимых вариантов, без оборванных ссылок.
  Отдельный расширенный CTest `NativeScenarioRootMaps` запускает штатный
  `BuildMap` и Lua-парсер для всех 52 корней, сравнивает отпечаток результата
  Windows/Linux x64; `NativeScenarioPlacement3830` отдельно защищает
  исправление неинициализированного сдвига деталей здания на ARM64.
  Команды и граница этих тестов — в документе. Построение карты не заменяет
  выполнение миссии в `CWorld`.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-MAP-BUILD-LINUX.md` —
  Linux-сборка оригинального `MapBuild.cpp` и связанный тест его
  `ConvertFlags` на реальной базе; дальнейший рубеж — игровой загрузчик
  ресурсов и полный `BuildMap` миссии.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-RESOURCE-LOADER-LINUX.md` —
  штатные `FilesPackage.cpp` и `GResource.cpp` на Linux: `.res`, приоритет
  отдельных файлов и модов, асинхронное чтение, точки маршрутов из
  `Waypoints.res`, x86/x64/ARM64-сверка и два известных исключения.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-MAP-POLYGONS-LINUX.md` —
  оригинальный `PolyUtils.cpp` на Linux, тест отсечения полигонов,
  x86/x64/ARM64-паритет, исправление UB и конфликта `Time.h` с C runtime;
  перечень ещё не связанных зависимостей полного `BuildMap`.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-BUILDING-TERRAIN-LINUX.md` —
  оригинальные сетка разрушений, загрузчики зданий и рельефа на Linux;
  прямой тест взрыва и ограниченная сверка `game.db`/`Buildings.res`/
  `Terrain.res` на Windows x86/x64 и Linux x64/ARM64. Полный `BuildMap`
  проверяется отдельно.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-AI-GEOMETRY-RESOURCES.md` —
  строгая сверка всех 1982 ID AI-геометрии из игровой БД с учётом 1974
  отдельных файлов, включая предрассчитанные коллизионные сетки;
  Windows x86/x64, Linux GCC/Clang x64 и ARM64/QEMU.
- `../Silent-Storm-Reconstruction/diagnostics/NATIVE-MISSION-BUILD-LINUX.md` —
  Linux-линковка и исполнение оригинального `BuildMap` на вариантах
  218/810/2400/4526, включая карту с 49 юнитами и 24 путевыми точками,
  сверка карты, полных команд маршрутов, параметров AI и байтов Lua-скриптов
  с Windows x86/x64, исправление UB в
  `SMapElement` и clue-slot; граница проверки — без выполнения скриптов
  и живого AI.
- `../Silent-Storm-Reconstruction/STAGE0-BASELINE.md` — зафиксированный x86
  эталон, данные, сборка, контрольные сценарии и известные ограничения.
- `../Silent-Storm-Reconstruction/PORTING-WINX64.md` — журнал x64-проверок
  ядра. Раннее сообщение о пустом лице там историческое; более поздняя
  проверка портрета описана в следующем документе.
- `../Silent-Storm-Reconstruction/diagnostics/FACE-PARITY.md` — текущие
  свидетельства и границы нативной лицевой анимации. Прямой
  `ITransformer::Load/Generate` для используемого игрой FaceGen реализован:
  проверяйте его отдельно от воспроизведения уже готового аниматора.
  Переносить только вызываемый игрой путь: 14 ползунков расширенного редактора,
  геометрию, каналы текстуры и сохранение выбранного лица, а не весь API FaceGen.
- `../Silent-Storm-Reconstruction/STABILIZATION.md` и
  `../Silent-Storm-Reconstruction/diagnostics/{CHECKS,SCENARIO,INPUT-BLOCKER}.md`
  — история лаборатории, игровые сценарии и диагноз ввода. Датированные
  записи могут описывать уже устранённые блокеры; сверяйте с более поздними
  проверками и текущим кодом.
- `../Silent-Storm-Reconstruction/diagnostics/MANUAL-BUGS-2026-09-23.md` —
  журнал замечаний ручного прогона Windows/x64: что подтверждено и исправлено,
  что ещё требует сравнения с оригиналом, а что отложено до графического этапа.
  Перед разбором новых сообщений сверяйте этот журнал с текущим кодом и
  добавляйте туда воспроизводимый сценарий, артефакты и статус; не считайте
  пользовательское предположение о причине доказанным диагнозом.

## Сборка и автоматические проверки

Команды ниже выполняются в PowerShell из `G:\SS\Silent-Storm-Reconstruction`.
Локальные пути отражают этот стенд; для другой машины подставьте её toolchain,
игровые данные и каталоги сборки. MSVC здесь требует
`$env:UCRTContentRoot='C:\Program Files (x86)\Windows Kits\10\'`.

```powershell
$env:UCRTContentRoot='C:\Program Files (x86)\Windows Kits\10\'
$cmake='G:\SS\lab\tools\VS2022\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin\cmake.exe'
 & $cmake --build 'G:\SS\lab\build-x64-stage2' --config RelWithDebInfo --target Game FaceProbe FaceGDPProbe NativeFaceGenApiCheck PortableGameDatabaseProbe --parallel 12
& 'G:\SS\lab\tools\VS2022\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin\ctest.exe' --test-dir 'G:\SS\lab\build-x64-stage2' -C RelWithDebInfo --output-on-failure
```

Для конфигурации с нуля и архива воспроизводимой сборки используйте
`diagnostics/Build-Lab.ps1 -Architecture x64 -BuildId <новое-имя> -LabRoot G:\SS\lab -ArchiveRoot G:\SS\lab\builds -BuildDirectory G:\SS\lab\build-x64-stage2 -NativeMedia -FFmpegRoot <корень FFmpeg> -MiniaudioIncludeDir <каталог miniaudio>`.
Архив Win32 создавайте только для конкретного сравнения с x86-оракулом.
`New-LabRun.ps1` для нативного SFX исключает `fmod.dll` уже при копировании
baseline; проверяйте его отсутствие в новом запуске.
Скрипт требует чистый рабочий репозиторий и сам создаёт архив EXE/PDB.
По умолчанию он собирает в 12 потоков (не больше числа логических ядер);
для другого лимита задайте `-BuildJobs N`. На проверенном Linux-хосте
32 логических ядра и 62 ГБ RAM, переносимые цели собирайте с `-j 16`.
Не выдавайте инкрементальную сборку или зелёный CTest за полный игровой прогон.

Контрактные проверки этапа 0 находятся в `diagnostics/Test-{Script,Movement,
Combat,AI,Campaign,Save}Surface.ps1`; точный эталон, параметры и границы — в
`STAGE0-BASELINE.md`. `Test-Diagnostics.ps1` проверяет сборку/чтение дампов,
`Test-SteamUnchanged.ps1` — неизменность оригинальной установки.
Для повторения шести контрактных проверок зафиксированного архива (это не
запуск игры) используйте:

```powershell
$build='G:\SS\lab\builds\stage0-control-01'
& .\diagnostics\Test-ScriptSurface.ps1
foreach ($surface in 'Movement','Combat','AI','Campaign') {
  & ".\diagnostics\Test-${surface}Surface.ps1" -CurrentBuildDirectory $build
}
$save='G:\SS\lab\runs\user-ai-save-01\evidence\user-quicksave-20260922-211453\Быстрая запись (2)\game.sav'
& .\diagnostics\Test-SaveSurface.ps1 -CurrentBuildDirectory $build -SaveFile $save
```

Для лицевой анимации сравнивайте x86-оракул с нативным x64, а не только x64
с самим собой. Пример ограниченного паритетного теста (20 последовательностей
одной головы; эталонные файлы остаются в `lab`):

```powershell
& .\diagnostics\Test-FaceParity.ps1 `
  -FixtureDirectory 'G:\SS\lab\runs\stage2-x86-face-corpus-01\evidence\representative-fixtures' `
  -GameRoot 'G:\SS\lab\baseline' `
  -X86Probe 'G:\SS\lab\build-x86\RelWithDebInfo\FaceProbe.exe' `
  -X64Probe 'G:\SS\lab\build-x64\RelWithDebInfo\FaceProbe.exe'
```

Остальные точные сценарии и параметры перечислены в `diagnostics/FACE-PARITY.md`:
`Test-FaceNeutralParity.ps1`, `Test-FaceBoneEffectParity.ps1`,
`Test-FaceMuscleStateParity.ps1`, `Test-MMTree*`, `Test-FaceGDPParity.ps1`.
`Test-FaceGDPParity.ps1` проверяет воспроизведение уже сгенерированного x86
потока. `Test-FaceGDPTransformParity.ps1` — отдельный прямой тест нативной
генерации; сейчас он проходит для игровых ползунков. Не подменяйте этот
результат первым тестом: оба пути должны оставаться зелёными.
`NativeFaceGenApiCheck.exe G:\SS\lab\baseline\Res\FaceGenHead.gdp` проверяет
загрузку правил и архетипов, но не генерацию лица.
`Test-FaceGDPUserItemParity.ps1` проверяет игровые каналы весов текстуры;
`Test-FaceGenEditorPreviewParity.ps1` — настоящий интерфейс расширенного
редактора и CPU-превью в парных x86/x64-запусках. Оба теста прошли на
2026-09-23; последний не доказывает GPU-пиксельный паритет.
`Test-FaceGenCommittedCorpusParity.ps1` прошёл все 6780 извлечённых игровых
последовательностей для одного сохранённого FaceGen-лица (по пять точек
времени); `Test-FaceGenCommittedExpressionCorpus.ps1` отдельно проверяет
все восемь масок эмоций с речью и движением головы по плотной сетке времени.
Прямой gate и отдельная проверка уже готового потока запускаются так:

```powershell
$bin86='G:\SS\lab\build-x86\RelWithDebInfo'
$bin64='G:\SS\lab\build-x64\RelWithDebInfo'
$out='G:\SS\lab\runs\<новый-run-id>\evidence\face-gdp'
& .\diagnostics\Test-FaceGDPParity.ps1 -GameRoot 'G:\SS\lab\baseline' `
  -X86GDPProbe "$bin86\FaceGDPProbe.exe" -X86FaceProbe "$bin86\FaceProbe.exe" `
  -X64FaceProbe "$bin64\FaceProbe.exe" -OutputDirectory "$out\saved-stream"
& .\diagnostics\Test-FaceGDPTransformParity.ps1 -GameRoot 'G:\SS\lab\baseline' `
  -X86GDPProbe "$bin86\FaceGDPProbe.exe" -X64GDPProbe "$bin64\FaceGDPProbe.exe" `
  -OutputDirectory "$out\native-transform"
```

Замените `<новый-run-id>` существующим отдельным LabRun или иным новым
каталогом внутри `lab`; второй скрипт должен стать зелёным только после
реализации генератора, а не после изменения допуска сравнения.

## Как проверять игру, а не только тестовые функции

Запускайте игру из отдельного LabRun, никогда из исходной Steam-установки и
не поверх прежнего `runs/<RunId>`. После архива `Build-Lab.ps1`:

```powershell
& .\diagnostics\New-LabRun.ps1 -BuildId '<имя-архива>' -RunId '<новый-run-id>'
& .\diagnostics\Start-LabRun.ps1 -RunDirectory 'G:\SS\lab\runs\<новый-run-id>'
```

Для проверки Unicode-пути укажите `-UserDataDirectory` с абсолютным путём
внутри этого LabRun; скрипт запишет его в `evidence/run.json` и передаст игре
через `S2_USER_DATA_DIR`. Без параметра используется `<LabRun>/user-data`.

Для быстрых проверок меню и миссии добавьте `-SkipIntro` к
`New-LabRun.ps1`: он создаст только в новом LabRun файл
`cfg/lab-no-intro.cfg` с командой `mainmenu` и запишет `-cfg` в параметры
запуска. `Start-LabRun.ps1` использует эти параметры автоматически.
Сюжетные ролики при этом остаются включёнными. Для проверки стартового
видео создавайте обычный LabRun без `-SkipIntro`.

`Start-LabRun.ps1` запускает игру под CDB и сохраняет журнал/дамп при сбое.
Для динамического сценария определите PID **именно этого** `Game.exe` по пути
в `runs/<RunId>/game`, а не берите первый процесс с таким именем. Используйте
`G:\SS\lab\input-tools\LabInput.exe <PID> focus`, затем `move <dx> <dy>`
(относительные шаги не больше 200 по каждой оси), `click` и
`key <scan-code>`; исходник и сборка инструмента —
`diagnostics/LabInput.cpp` и `diagnostics/Build-Input.ps1`. Инструмент
проверяет активное окно и разрешает ввод только процессу внутри `G:\SS\lab`.
Снимок окна: `diagnostics/Capture-Window.ps1 -ProcessId <PID>
-OutputPath 'G:\SS\lab\runs\<RunId>\evidence\after.png'`.

Обычный computer-use/sky **не годится для управления этой игрой**: его ввод
давал Win32-события, но не нужные события клавиатуры и относительной мыши
exclusive DirectInput. Не трактуйте неудачный клик этим способом как игровой
дефект. Для проверок движения/AP, инвентаря и портрета применяйте `LabInput`,
снимки до/после, игровые логи и состояние сохранения. Lua/harness помогает
подготовить сценарий, но не заменяет настоящий игровой ввод. Если окно
минимизировано или потеряло фокус, чёрный/обрезанный снимок не доказывает
дефект рендеринга; `Capture-Window.ps1` использует физические пиксели DPI.
Он копирует экранный прямоугольник, поэтому перекрывающее окно может попасть
в кадр вместо игры. Это произошло и с обычным захватом, и с computer-use
в LabRun `stage2-clang-penetration-smoke-20260927-01`; не засчитывайте такие
кадры как визуальный smoke. Журнал достижения меню и наличие окна дают
только более узкое подтверждение запуска.

Отделяйте наблюдаемое от доказанного: видимый и анимированный x64-портрет
подтверждён в одном живом прогоне и пользователем; это не паритет всех лиц,
FaceGen, реплик или x86/x64-кадров. FMOD отвечает за звук, а не за геометрию
лица; временные FMOD/Bink-заглушки не являются проверкой медиа.

## Отладка игровых сбоев и рассинхронизации состояния

- Сохраняйте исходный пользовательский сейв и артефакты в `lab` без изменений.
  Для воспроизведения делайте отдельный LabRun с копией сейва; фиксируйте точную
  последовательность действий, кадры до/после, `_console.log`, `_saveload.log`
  и `evidence/debugger.log`. На загрузку можно указать `-loadslot`, записав
  имя слота в `game/_loadslot.txt` **изолированного** LabRun.
- При крэше `Start-LabRun.ps1` оставляет `evidence/crash.dmp` и журнал CDB.
  Открывайте дамп x64-отладчиком из Windows Kits вместе с PDB той же сборки;
  в CDB используйте `.sympath <каталог game с PDB>`, `.reload /f`, `.ecxr`,
  `kv` и `ln <адрес>` для стека и места сбоя. Не выводите причину только из
  верхнего кадра: проверяйте значения аргументов, сохранённое состояние и
  путь, который передал невалидные данные. Сборка и сохранение должны
  соответствовать анализируемому запуску.
- Если ошибка визуальная, сопоставляйте анимацию, логическое состояние юнита
  и момент игрового события. Для редкого перехода допускается временная
  адресная трассировка с `DebugTrace` в изолированной сборке; её вывод попадает
  в `debugger.log`. По адресу вызова с PDB найдите реальный вызывающий код,
  повторите сценарий после исправления и удалите трассировку перед коммитом.
  Так был найден сброс посадки на мотоцикл: `EnterCannon` сменил состояние,
  затем `TBS_STOP_MOVE_AND_CANCEL_ACTION` вызвал `PlaceUnit` ещё до кат-сцены.
- При проверке живых Lua-потоков учитывайте, что `Script::GetGlobal` кладёт
  значение в стек текущего потока. Оборачивайте серии таких чтений в
  `Script::AutoBlock`, иначе сам пробник способен сломать следующий `Sleep`:
  это воспроизводилось как «attempt to call a number value» в
  `TriggersManager.l`. В headless-миссии отдельно отличайте Lua-ошибку от
  ожидания UI-команды (`WaitForUI`): отсутствие позднего callback не означает
  ошибку VM, пока интерфейсный ID не подтверждён.
- Зелёный CTest подтверждает только контрактные тесты. Для игрового фикса
  нужны повторный запуск соответствующего сейва, проверка видимого результата
  и, когда возможно, сравнение с x86-оригиналом. Непроверенные гипотезы об ИИ
  и графике оставляйте в журнале как открытые, не смешивая их с исправленными
  крэшами и рассинхронизацией.
