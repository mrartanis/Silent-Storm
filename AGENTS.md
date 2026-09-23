# Карта проекта для агентов

Рабочая среда на этой машине состоит из трёх разных каталогов. Не смешивайте
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
  `baseline`, toolchain, каталоги `build-x86`/`build-x64`, архивы `builds`,
  запуски `runs` и извлечённые эталонные потоки. Это не Git. Лицензионные
  ресурсы, DLL, сохранения, дампы и скриншоты не добавляйте в репозитории.

## Где лежат цели и актуальные результаты

- `REFACTOR.md` здесь — общий объём и критерии завершения этапов. Этап 0 закрыт
  только для согласованных данных и сценариев; Windows/x64-шаг проверен на
  ограниченном наборе, но этап 1 целиком не закрыт.
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
& $cmake --build 'G:\SS\lab\build-x86' --config RelWithDebInfo --target FaceProbe FaceGDPProbe
& $cmake --build 'G:\SS\lab\build-x64' --config RelWithDebInfo --target Game FaceProbe FaceGDPProbe NativeFaceGenApiCheck
& 'G:\SS\lab\tools\VS2022\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin\ctest.exe' --test-dir 'G:\SS\lab\build-x86' -C RelWithDebInfo --output-on-failure
& 'G:\SS\lab\tools\VS2022\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin\ctest.exe' --test-dir 'G:\SS\lab\build-x64' -C RelWithDebInfo --output-on-failure
```

Для конфигурации с нуля и архива воспроизводимой сборки используйте
`diagnostics/Build-Lab.ps1 -Architecture x64 -BuildId <новое-имя>` (или
`Win32`). Он требует чистый рабочий репозиторий и сам создаёт архив EXE/PDB.
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
- Зелёный CTest подтверждает только контрактные тесты. Для игрового фикса
  нужны повторный запуск соответствующего сейва, проверка видимого результата
  и, когда возможно, сравнение с x86-оригиналом. Непроверенные гипотезы об ИИ
  и графике оставляйте в журнале как открытые, не смешивая их с исправленными
  крэшами и рассинхронизацией.
