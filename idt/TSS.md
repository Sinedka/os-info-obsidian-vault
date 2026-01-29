# Task State Segment (TSS)

## Общая информация

**TSS (Task State Segment)** — это специальная структура архитектуры x86, описывающая состояние задачи и данные, необходимые процессору для:

- перехода между уровнями привилегий    
- (исторически) аппаратного переключения задач

В современных ОС (Linux, *BSD, Windows):

- **аппаратное переключение задач не используется**
- **TSS применяется только для stack switching и IST**

TSS:

- описывается **дескриптором в GDT**
- загружается инструкцией `LTR`
- должен быть **уникален для каждого CPU**
# TSS в x86 (32-bit, protected mode)

## Размер
- **104 байта** (без I/O bitmap)
- может быть больше при наличии I/O Permission Bitmap

---

## Формат TSS (32-bit)

```
Offset  Size  Поле
-----------------------------------------
0x00    2     Previous Task Link
0x02    2     Reserved
0x04    4     ESP0
0x08    2     SS0
0x0A    2     Reserved
0x0C    4     ESP1
0x10    2     SS1
0x12    2     Reserved
0x14    4     ESP2
0x18    2     SS2
0x1A    2     Reserved
0x1C    4     CR3
0x20    4     EIP
0x24    4     EFLAGS
0x28    4     EAX
0x2C    4     ECX
0x30    4     EDX
0x34    4     EBX
0x38    4     ESP
0x3C    4     EBP
0x40    4     ESI
0x44    4     EDI
0x48    2     ES
0x4A    2     Reserved
0x4C    2     CS
0x4E    2     Reserved
0x50    2     SS
0x52    2     Reserved
0x54    2     DS
0x56    2     Reserved
0x58    2     FS
0x5A    2     Reserved
0x5C    2     GS
0x5E    2     Reserved
0x60    2     LDT Selector
0x62    2     Reserved
0x64    2     I/O Map Base
```

---

## Используемые поля (реально)

В современных ОС используются **только**:

```
ESP0  (0x04)
SS0   (0x08)
I/O Map Base
```

### Назначение:

- **ESP0 / SS0** — стек ядра при переходе с CPL3 → CPL0
    
- CPU автоматически загружает их при прерывании/исключении
    

❌ Остальные поля:

- не инициализируются
    
- аппаратное task switching не используется
    

---

## I/O Permission Bitmap

- располагается **после TSS**
    
- управляет доступом к портам `IN/OUT`
    
- Linux почти всегда **запрещает всё**
    

---

# TSS в x86-64 (long mode)

## Размер

- **104 байта** (фиксированный)
    
- I/O bitmap опционален
    

---

## Главное отличие

В x86-64:

- **нет аппаратного task switching**
    
- **нет хранения регистров**
    
- TSS используется **ТОЛЬКО** для:
    
    - stack switching
        
    - IST (Interrupt Stack Table)
        

---

## Формат TSS (64-bit)

```
Offset  Size  Поле
-----------------------------------------
0x00    4     Reserved
0x04    8     RSP0
0x0C    8     RSP1
0x14    8     RSP2
0x1C    8     Reserved
0x24    8     IST1
0x2C    8     IST2
0x34    8     IST3
0x3C    8     IST4
0x44    8     IST5
0x4C    8     IST6
0x54    8     IST7
0x5C    8     Reserved
0x64    2     Reserved
0x66    2     I/O Map Base
```

---

## Используемые поля (64-bit)

### 🔹 RSP0

- **основной стек ядра**
    
- используется при переходе CPL3 → CPL0
    

### 🔹 IST1–IST7
- отдельные стеки для конкретных IDT entries
- задаются в gate descriptor IDT

Используются для:
- #DF (double fault) 
- `NMI`
- иногда #PF, #MC
## IST — Interrupt Stack Table

Если IDT entry содержит `IST = n`:
- CPU **игнорирует текущий RSP**
- загружает `RSP = ISTn`
- гарантирует валидный стек

👉 Критично для обработки фатальных ошибок.
## Чего НЕТ в x86-64 TSS
❌ Нет:
- EIP / RIP
- CR3
- регистров общего назначения
- task link
- аппаратного task switching
# Дескриптор TSS в GDT
## Тип

| Архитектура | Type                      |
| ----------- | ------------------------- |
| x86         | 0x9 (Available TSS)       |
| x86-64      | 0x9 (64-bit TSS, 16 байт) 
В x86-64:
- дескриптор TSS **занимает 16 байт**
- base адрес — 64-битный
## Загрузка TSS

```asm
ltr ax   ; ax = selector TSS
```

После этого:
- CPU знает, где TSS
- использует его при privilege switch
# Ключевые правила

- TSS **обязателен**, даже если не используется task switching
- Один TSS **на каждый CPU**
- Без корректного TSS:    
    - #GP
    - triple fault
    - reset CPU
# Краткое сравнение

|                    | x86              | x86-64    |
| ------------------ | ---------------- | --------- |
| Task switch        | Да (исторически) | Нет       |
| Хранение регистров | Да               | Нет       |
| Stack switch       | Да               | Да        |
| IST                | Нет              | Да        |
| Размер             | ≥104 байта       | 104 байта |
