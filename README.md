# CrossApp

Наскрізний проєкт з крос-платформного програмування.

Предметна область: Склад.

Сутності: Product (товар), StockBatch (партія), Warehouse (склад), Movement (переміщення).

Призначення: облік залишків товарів по партіях.

## Структура solution

```text
CrossApp/
  CrossApp.sln
  README.md
  .gitignore
  src/
    Core/
      Core.csproj
      EnvironmentInfo.cs
    Cli/
      Cli.csproj
      Program.cs
```

`Core` — бібліотека зі спільною логікою, `Cli` — консольна точка входу. Напрямок залежності: `Cli → Core`.

Домовленість про каталоги в `Core`:

- `Core/Dto/` — record-типи формату даних (тиждень 3)
- `Core/Domain/` — сутності з поведінкою та інваріантами (тиждень 4)
- `Core/Storage/` — реалізації сховищ (тиждень 5)

## Запуск

```bash
dotnet build
dotnet run --project src/Cli
```

## Публікація

```bash
dotnet publish src/Cli -c Release -r win-x64 --self-contained true
dotnet publish src/Cli -c Release -r win-x64 --self-contained false
```

Обидві команди записують результат у `src/Cli/bin/Release/net10.0/win-x64/publish/`, тому перед зміною режиму каталог `src/Cli/bin/Release` слід очистити.

**Self-contained** містить застосунок разом із .NET Runtime, тому працює без встановленого .NET, але займає більше місця. **Framework-dependent** містить лише застосунок і його залежності, тому на комп'ютері має бути встановлений .NET Runtime потрібної версії.

| RID | Режим | Розмір publish | Файлів | Потрібен runtime |
| --- | --- | --- | --- | --- |
| win-x64 | self-contained | 76,85 МБ | 194 | ні |
| win-x64 | framework-dependent | 0,19 МБ | 7 | так (.NET 10) |

## Середовище

.NET SDK 10.0, Windows 11 x64
