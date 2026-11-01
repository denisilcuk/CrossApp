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
  data/
    sample.csv
    valid.csv
    no-header.csv
  src/
    Core/
      Core.csproj
      EnvironmentInfo.cs
      Dto/
        ProductDto.cs
        WarehouseDto.cs
        ImportResult.cs
      Import/
        ProductCsvImporter.cs
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
dotnet run --project src/Cli -- data/valid.csv
```

Без аргументу програма читає `data/sample.csv` відносно поточного каталогу, тому запускати її слід із кореня репозиторію.

## Формат файлу імпорту

- Кодування: UTF-8.
- Роздільник: крапка з комою (`;`).
- Колонки: `id;sku;name;unit;quantity`.
- Заголовок необов'язковий: перший рядок, що починається з `id`, пропускається.
- Порожні рядки та рядки, що починаються з `#`, ігноруються.
- `quantity` — ціле невід'ємне число.

Тестові файли:

- `data/sample.csv` — 10 коректних рядків і 3 навмисно пошкоджені (рядки 12–14).
- `data/valid.csv` — лише коректні рядки.
- `data/no-header.csv` — коректні рядки без заголовка.

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
