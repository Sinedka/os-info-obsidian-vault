# Исключения процессора (x86/x86_64)

Ниже приведены стандартные векторы 0–31, зарезервированные для исключений процессора.

- **0** — #DE Divide Error (деление на ноль)
- **1** — #DB Debug (отладочное исключение)
- **2** — NMI (немаскируемое прерывание)
- **3** — #BP Breakpoint (точка останова)
- **4** — #OF Overflow (переполнение)
- **5** — #BR BOUND Range Exceeded (выход за границы в BOUND)
- **6** — #UD Invalid Opcode (недопустимая инструкция)
- **7** — #NM Device Not Available (FPU не доступен)
- **8** — #DF Double Fault (двойной сбой)
- **9** — Coprocessor Segment Overrun (устаревшее, зарезервировано)
- **10** — #TS Invalid TSS (некорректный TSS)
- **11** — #NP Segment Not Present (сегмент не присутствует)
- **12** — #SS Stack-Segment Fault (ошибка сегмента стека)
- **13** — #GP General Protection (общая защита)
- **14** — #PF Page Fault (ошибка страницы)
- **15** — Reserved (зарезервировано)
- **16** — #MF x87 FPU Floating-Point Error (ошибка FPU)
- **17** — #AC Alignment Check (проверка выравнивания)
- **18** — #MC Machine Check (аппаратная ошибка)
- **19** — #XM/#XF SIMD Floating-Point Exception (ошибка SIMD/FPU)
- **20** — #VE Virtualization Exception (виртуализация)
- **21** — #CP Control Protection Exception (защита управления)
- **22** — Reserved (зарезервировано)
- **23** — Reserved (зарезервировано)
- **24** — Reserved (зарезервировано)
- **25** — Reserved (зарезервировано)
- **26** — Reserved (зарезервировано)
- **27** — Reserved (зарезервировано)
- **28** — #HV Hypervisor Injection Exception (гипервизор)
- **29** — #VC VMM Communication Exception (связь с VMM)
- **30** — #SX Security Exception (безопасность)
- **31** — Reserved (зарезервировано)

Примечания:
- Набор векторов одинаков в 32‑ и 64‑битных режимах, но некоторые исключения зависят от наличия функций процессора.
- Некоторые векторы добавлялись по мере развития архитектуры (например, #VE, #CP, #VC, #SX).

