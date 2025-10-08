### **Практическая работа № 1**
**Тема:** «Структура системы Windows и основы скриптового языка PowerShell»  
**Дисциплина:** Операционные системы и среды

---

#### **Цель работы:**
Освоить базовые принципы работы операционной системы Windows и изучить основы скриптового языка PowerShell для автоматизации задач системного администрирования.

---

### **Теоретическая часть**

#### **1. Архитектура Windows**
- **Ядро системы (Kernel)**: Режим ядра (kernel mode) и пользовательский режим (user mode)
- **Ключевые компоненты**: 
  - Executive (диспетчер памяти, процессы, безопасность)
  - HAL (Hardware Abstraction Layer)
  - Драйверы устройств
- **Подсистемы**: Win32, POSIX, OS/2

#### **2. Файловая система NTFS**
- Права доступа (ACL)
- Журналирование
- Сжатие и шифрование (EFS)
- Точки монтирования

#### **3. Реестр Windows**
- Иерархическая база данных конфигурации
- Основные кусты (hives):
  - HKEY_LOCAL_MACHINE (HKLM)
  - HKEY_CURRENT_USER (HKCU)
  - HKEY_CLASSES_ROOT (HKCR)
  - HKEY_USERS (HKU)
  - HKEY_CURRENT_CONFIG (HKCC)

#### **4. Введение в PowerShell**
- Объектно-ориентированный скриптовый язык
- Интегрированная среда выполнения (ISE)
- Командлеты (cmdlets) - структура "Глагол-Существительное"
- Конвейер (pipeline) для передачи объектов

---

### **Практическая часть**

#### **Задание 1: Изучение структуры Windows**

**1.1. Работа с реестром:**
```powershell
# Просмотр содержимого реестра
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion"
[here](#Просмотр содержимого реестра.jpg)
# Создание параметра
New-ItemProperty -Path "HKCU:\Software" -Name "TestValue" -Value "123" -PropertyType String

# Удаление параметра
Remove-ItemProperty -Path "HKCU:\Software" -Name "TestValue"
```

**1.2. Анализ системной информации:**
```powershell
# Информация о системе
Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version, OSArchitecture

# Информация о процессоре
Get-CimInstance Win32_Processor | Select-Object Name, NumberOfCores, MaxClockSpeed

# Объем оперативной памяти
Get-CimInstance Win32_ComputerSystem | Select-Object TotalPhysicalMemory
```

#### **Задание 2: Основы PowerShell**

**2.1. Работа с файловой системой:**
```powershell
# Создание директории
New-Item -Path "C:\Test" -ItemType Directory

# Создание файла
New-Item -Path "C:\Test\example.txt" -ItemType File

# Запись в файл
"Hello, PowerShell!" | Out-File -FilePath "C:\Test\example.txt"

# Чтение файла
Get-Content -Path "C:\Test\example.txt"

# Рекурсивный просмотр директории
Get-ChildItem -Path "C:\Windows" -Recurse -Depth 1 | Select-Object Name, Length, LastWriteTime
```

**2.2. Управление процессами и службами:**
```powershell
# Получение списка процессов
Get-Process | Where-Object {$_.CPU -gt 10} | Sort-Object CPU -Descending

# Запуск/остановка процесса
Start-Process notepad.exe
Stop-Process -Name notepad

# Управление службами
Get-Service | Where-Object {$_.Status -eq "Running"}
Stop-Service -Name "Spooler"
Start-Service -Name "Spooler"
```

#### **Задание 3: Автоматизация задач**

**3.1. Создание скрипта для сбора системной информации:**
```powershell
# SystemInfo.ps1
$computerName = $env:COMPUTERNAME
$osInfo = Get-CimInstance Win32_OperatingSystem
$cpuInfo = Get-CimInstance Win32_Processor
$memoryInfo = Get-CimInstance Win32_ComputerSystem

$report = @"
Системный отчет для: $computerName
Операционная система: $($osInfo.Caption)
Версия OS: $($osInfo.Version)
Архитектура: $($osInfo.OSArchitecture)
Процессор: $($cpuInfo.Name)
Количество ядер: $($cpuInfo.NumberOfCores)
Оперативная память: $([math]::Round($memoryInfo.TotalPhysicalMemory/1GB, 2)) GB
"@

# Сохранение отчета в файл
$report | Out-File -FilePath "C:\Test\SystemReport.txt"
Write-Host "Отчет сохранен в C:\Test\SystemReport.txt"
```

**3.2. Скрипт для мониторинга дискового пространства:**
```powershell
# DiskMonitor.ps1
$disks = Get-WmiObject Win32_LogicalDisk -Filter "DriveType=3"

foreach ($disk in $disks) {
    $freeSpace = [math]::Round($disk.FreeSpace/1GB, 2)
    $totalSpace = [math]::Round($disk.Size/1GB, 2)
    $usedSpace = $totalSpace - $freeSpace
    $percentFree = [math]::Round(($freeSpace/$totalSpace)*100, 2)
    
    Write-Host "Диск $($disk.DeviceID)"
    Write-Host "  Всего места: $totalSpace GB"
    Write-Host "  Свободно: $freeSpace GB ($percentFree%)"
    
    if ($percentFree -lt 10) {
        Write-Warning "Внимание! На диске $($disk.DeviceID) осталось мало места!"
    }
}
```

#### **Задание 4: Работа с конвейером**

**4.1. Фильтрация и сортировка:**
```powershell
# Получение 5 самых ресурсоемких процессов
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 Name, CPU, PM

# Поиск файлов по расширению
Get-ChildItem -Path "C:\Windows\System32" -Filter "*.dll" | 
    Where-Object {$_.Length -gt 1MB} | 
    Sort-Object Length -Descending |
    Select-Object -First 10 Name, Length
```

**4.2. Экспорт данных:**
```powershell
# Экспорт в CSV
Get-Service | Export-Csv -Path "C:\Test\services.csv" -NoTypeInformation

# Экспорт в HTML
Get-Process | ConvertTo-Html | Out-File "C:\Test\processes.html"
```

---

### **Контрольные вопросы**

1. **Каковы основные компоненты архитектуры Windows?**
2. **Что такое командлет в PowerShell? Приведите примеры.**
3. **Как передаются данные между командлетами в конвейере?**
4. **Какие основные разделы реестра Windows вы знаете?**
5. **Как в PowerShell получить информацию о запущенных процессах?**
6. **Какие способы создания файлов и директорий существуют в PowerShell?**
7. **Как организовать условные операции в скриптах PowerShell?**
8. **Какие возможности для экспорта данных предоставляет PowerShell?**

---

### **Требования к отчету**

1. **Титульный лист** с указанием темы, ФИО студента, группы
2. **Цель работы**
3. **Выполненные задания** с примерами кода и скриншотами результатов
4. **Ответы на контрольные вопросы**
5. **Выводы** по работе

---

### **Критерии оценки**

**Отлично (5):**
- Полное выполнение всех заданий
- Правильно работающий код всех скриптов
- Глубокие ответы на контрольные вопросы
- Наличие подробных выводов

**Хорошо (4):**
- Выполнение основных заданий
- Незначительные ошибки в коде
- Полные ответы на большинство вопросов

**Удовлетворительно (3):**
- Частичное выполнение заданий
- Наличие существенных ошибок в коде
- Поверхностные ответы на вопросы

---

**Вывод:** В ходе практической работы были изучены основы архитектуры операционной системы Windows и принципы работы с скриптовым языком PowerShell. Приобретены навыки автоматизации задач системного администрирования, работы с реестром, файловой системой и процессами. Полученные знания являются фундаментом для дальнейшего изучения операционных систем и сред.
