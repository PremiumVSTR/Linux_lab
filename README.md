# Linux_lab

## Лабораторная работа 1. Реализация функции на C++

<img width="369" height="398" alt="image" src="https://github.com/user-attachments/assets/cb5078a9-5877-4907-9821-b1570b5592a9" />

### Компиляция и запуск

<img width="424" height="104" alt="image" src="https://github.com/user-attachments/assets/d6d12f0c-cc99-4de4-bbb8-f23468db24f1" />

## 2. Компиляция в ассемблерский код с разными оптимизациями

<img width="537" height="170" alt="image" src="https://github.com/user-attachments/assets/0585bc2d-b760-4627-890f-d7a6a7212f10" />

<img width="204" height="200" alt="image" src="https://github.com/user-attachments/assets/36028503-c5e7-48cf-a0f9-925cadad3350" />

### Без оптимизации (-O0)
```
		.file	"fibonacci.cpp"
	.text
	.section .rdata,"dr"
.LC0:
	.ascii "Fibonacci F(\0"              # Строка "Fibonacci F("
.LC1:
	.ascii ") = \0"                       # Строка ") = "
	.text
	.globl	main
	.def	main;	.scl	2;	.type	32;	.endef
	.seh_proc	main
main:
.LFB2239:
	# === ПРОЛОГ ФУНКЦИИ ===
	pushq	%rbp
	.seh_pushreg	%rbp
	movq	%rsp, %rbp
	.seh_setframe	%rbp, 0
	subq	$64, %rsp                    # Выделяем 64 байта на стеке
	.seh_stackalloc	64
	.seh_endprologue
	call	__main
	
	# === ПЕРЕМЕННЫЕ ===
	movl	$10, -16(%rbp)               # n = 10
	movl	$0, -4(%rbp)                 # a = 0
	movl	$1, -8(%rbp)                 # b = 1
	
	# === ВЫВОД "Fibonacci F(" ===
	leaq	.LC0(%rip), %rax
	movq	%rax, %rdx
	movq	.refptr._ZSt4cout(%rip), %rax
	movq	%rax, %rcx
	call	_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc
	
	# === ВЫВОД ЧИСЛА n ===
	movq	%rax, %rcx
	movl	-16(%rbp), %eax
	movl	%eax, %edx
	call	_ZNSolsEi
	
	# === ВЫВОД ") = " ===
	movq	%rax, %rcx
	leaq	.LC1(%rip), %rax
	movq	%rax, %rdx
	call	_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc
	
	# === ПРОВЕРКА: if (n == 0) ===
	cmpl	$0, -16(%rbp)
	jne	.L2
	movl	$0, %edx
	movq	.refptr._ZSt4cout(%rip), %rax
	movq	%rax, %rcx
	call	_ZNSolsEi
	jmp	.L3
	
	# === ПРОВЕРКА: else if (n == 1) ===
.L2:
	cmpl	$1, -16(%rbp)
	jne	.L4
	movl	$1, %edx
	movq	.refptr._ZSt4cout(%rip), %rax
	movq	%rax, %rcx
	call	_ZNSolsEi
	jmp	.L3
	
	# === else (цикл для n >= 2) ===
.L4:
	movl	$2, -12(%rbp)                # i = 2 (счётчик цикла)
	jmp	.L5
	
	# === ТЕЛО ЦИКЛА ===
.L6:
	movl	-4(%rbp), %edx               # берём a
	movl	-8(%rbp), %eax               # берём b
	addl	%edx, %eax                   # a + b
	movl	%eax, -20(%rbp)              # c = a + b
	movl	-8(%rbp), %eax               # берём b
	movl	%eax, -4(%rbp)               # a = b
	movl	-20(%rbp), %eax              # берём c
	movl	%eax, -8(%rbp)               # b = c
	addl	$1, -12(%rbp)                # i++
	
	# === ПРОВЕРКА УСЛОВИЯ ЦИКЛА ===
.L5:
	movl	-12(%rbp), %eax
	cmpl	-16(%rbp), %eax              # сравниваем i с n
	jle	.L6                          # если i <= n, прыгаем в тело
	
	# === ВЫВОД РЕЗУЛЬТАТА ===
	movl	-8(%rbp), %eax               # берём b (результат)
	movl	%eax, %edx
	movq	.refptr._ZSt4cout(%rip), %rax
	movq	%rax, %rcx
	call	_ZNSolsEi                   # вывод числа
	
	# === ВЫВОД endl ===
.L3:
	movq	.refptr._ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_(%rip), %rax
	movq	%rax, %rdx
	movq	.refptr._ZSt4cout(%rip), %rax
	movq	%rax, %rcx
	call	_ZNSolsEPFRSoS_E
	
	# === ВОЗВРАТ ИЗ main ===
	movl	$0, %eax                     # return 0
	addq	$64, %rsp
	popq	%rbp
	ret
	.seh_endproc
```

### Вторая оптимизация (-O2)
```
	.file	"fibonacci.cpp"
	.text
	.p2align 4,,15                    # Выравнивание кода для производительности
	.def	__tcf_0;	.scl	3;	.type	32;	.endef
	.seh_proc	__tcf_0
__tcf_0:
.LFB2083:
	.seh_endprologue
	leaq	_ZStL8__ioinit(%rip), %rcx   # Адрес глобального объекта iostream
	jmp	_ZNSt8ios_base4InitD1Ev        # Уничтожение объекта (деструктор)
	.seh_endproc
	.def	__main;	.scl	2;	.type	32;	.endef
	.section .rdata,"dr"
.LC0:
	.ascii "Fibonacci F(\0"             # Строка "Fibonacci F("
.LC1:
	.ascii ") = \0"                      # Строка ") = "
	.section	.text.startup,"x"
	.p2align 4,,15
	.globl	main
	.def	main;	.scl	2;	.type	32;	.endef
	.seh_proc	main
main:
.LFB1594:
	# === ПРОЛОГ ФУНКЦИИ (упрощённый, без сохранения rbp) ===
	subq	$40, %rsp                    # Выделяем 40 байт на стеке
	.seh_stackalloc	40
	.seh_endprologue
	call	__main                       # Инициализация глобальных объектов
	
	# === ВЫВОД "Fibonacci F(" ===
	movq	.refptr._ZSt4cout(%rip), %rcx  # 1-й аргумент: cout
	movl	$12, %r8d                     # 3-й аргумент: длина строки (12 символов)
	leaq	.LC0(%rip), %rdx              # 2-й аргумент: адрес строки
	call	_ZSt16__ostream_insertIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_PKS3_x  # вывод строки
	
	# === ВЫВОД ЧИСЛА n (10) ===
	movq	.refptr._ZSt4cout(%rip), %rcx  # 1-й аргумент: cout
	movl	$10, %edx                     # 2-й аргумент: число 10 (n)
	call	_ZNSolsEi                     # operator<< (вывод числа)
	
	# === ВЫВОД ") = " ===
	leaq	.LC1(%rip), %rdx              # 2-й аргумент: адрес строки ") = "
	movq	%rax, %rcx                    # 1-й аргумент: результат предыдущего вызова
	call	_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc  # вывод строки
	
	# === ВЫЧИСЛЕНИЕ ЧИСЛА ФИБОНАЧЧИ F(10) ===
	# Регистры:
	#   eax - счётчик итераций (начинается с 9, уменьшается до 0)
	#   edx - текущее число Фибоначчи (b)
	#   ecx - предыдущее число (a)
	#   r8d - временная переменная (c = a + b)
	movl	$9, %eax                     # i = 9 (10 - 1, т.к. цикл выполняется n-1 раз)
	movl	$1, %edx                     # b = 1 (F(1) = 1)
	xorl	%ecx, %ecx                   # a = 0 (F(0) = 0)
	.p2align 4,,10                     # Выравнивание для производительности
	
.L4:                                    # НАЧАЛО ТЕЛА ЦИКЛА
	leal	(%rcx,%rdx), %r8d            # c = a + b
	subl	$1, %eax                     # i-- (уменьшаем счётчик)
	movl	%edx, %ecx                   # a = b (сохраняем предыдущее)
	movl	%r8d, %edx                   # b = c (новое значение)
	jne	.L4                          # если i != 0, переходим на .L4 (продолжаем цикл)
	# === КОНЕЦ ЦИКЛА ===
	
	# === ВЫВОД РЕЗУЛЬТАТА ===
	movq	.refptr._ZSt4cout(%rip), %rcx  # 1-й аргумент: cout
	call	_ZNSolsEi                     # operator<< (вывод числа b, который в edx)
	
	# === ВЫВОД ENDL (перевод строки) ===
	movq	.refptr._ZSt4cout(%rip), %rcx  # 1-й аргумент: cout
	call	_ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_  # endl
	
	# === ЭПИЛОГ ФУНКЦИИ ===
	xorl	%eax, %eax                   # return 0
	addq	$40, %rsp                    # Очищаем стек
	ret                                  # Возврат
	
	.seh_endproc
	.p2align 4,,15
	.def	_GLOBAL__sub_I_main;	.scl	3;	.type	32;	.endef
	.seh_proc	_GLOBAL__sub_I_main
_GLOBAL__sub_I_main:
.LFB2084:
	subq	$40, %rsp
	.seh_stackalloc	40
	.seh_endprologue
	leaq	_ZStL8__ioinit(%rip), %rcx
	call	_ZNSt8ios_base4InitC1Ev      # Инициализация iostream
	leaq	__tcf_0(%rip), %rcx
	addq	$40, %rsp
	jmp	atexit                         # Регистрируем деструктор
	.seh_endproc
	.section	.ctors,"w"
	.align 8
	.quad	_GLOBAL__sub_I_main          # Секция конструкторов
.lcomm _ZStL8__ioinit,1,1               # Объект iostream (глобальный)
	.ident	"GCC: (x86_64-posix-seh-rev0, Built by MinGW-W64 project) 8.1.0"
	.def	_ZNSt8ios_base4InitD1Ev;	.scl	2;	.type	32;	.endef
	.def	_ZSt16__ostream_insertIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_PKS3_x;	.scl	2;	.type	32;	.endef
	.def	_ZNSolsEi;	.scl	2;	.type	32;	.endef
	.def	_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc;	.scl	2;	.type	32;	.endef
	.def	_ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_;	.scl	2;	.type	32;	.endef
	.def	_ZNSt8ios_base4InitC1Ev;	.scl	2;	.type	32;	.endef
	.def	atexit;	.scl	2;	.type	32;	.endef
	.section	.rdata$.refptr._ZSt4cout, "dr"
	.globl	.refptr._ZSt4cout
	.linkonce	discard
.refptr._ZSt4cout:
	.quad	_ZSt4cout
```
## 3. Преобразование программы в модульную и разработка Makefile

<img width="223" height="143" alt="image" src="https://github.com/user-attachments/assets/d2995b97-c035-4973-b151-9f75b39a705a" />

### main.cpp

<img width="511" height="217" alt="image" src="https://github.com/user-attachments/assets/6782c33a-84f9-48a6-8116-1c275749305a" />

### fibonacci_module.cpp

<img width="375" height="262" alt="image" src="https://github.com/user-attachments/assets/3ce73db5-fc7d-4167-8816-137cfa849549" />

### fibonacci.h

<img width="346" height="148" alt="image" src="https://github.com/user-attachments/assets/b7dc5ba3-a6d3-41cf-9460-2ebd09944a78" />

### Makefile

<img width="477" height="347" alt="image" src="https://github.com/user-attachments/assets/f709f8ee-752d-43d8-879e-bdd9ab0b2f9f" />

### Сборка

<img width="369" height="77" alt="image" src="https://github.com/user-attachments/assets/6790f786-b44b-412f-9f4e-306808e4d1f8" />

### Тестирование

<img width="372" height="119" alt="image" src="https://github.com/user-attachments/assets/fe983716-dd18-46b4-9b04-76d435356585" />

## 4. Усовершенствование программы

### Добавление параллельного потока и синхронизации

<img width="501" height="559" alt="image" src="https://github.com/user-attachments/assets/bd9bd50f-66dc-4ce9-90bb-a6f11531a498" />

### Makefile с учетом изменений

<img width="547" height="341" alt="image" src="https://github.com/user-attachments/assets/eef66ef0-3e55-4916-bb70-af372781d799" />

### Тестирование

<img width="468" height="273" alt="image" src="https://github.com/user-attachments/assets/0339b0d4-3df5-4391-999e-7ec0571ce3d0" />


### Общий ресурс

Файл `result.txt` используется как общий ресурс для обмена данными между параллельными потоками с синхронизацией через `join()`.

<img width="230" height="196" alt="image" src="https://github.com/user-attachments/assets/505d5267-07a6-42b6-93d4-f36844547fb9" />
<img width="242" height="65" alt="image" src="https://github.com/user-attachments/assets/86373476-7624-46fb-9072-94ecd1f37c9a" />

## Лабораторная работа 2. Установка Linux
Видео доступно по ссылке: https://disk.yandex.ru/i/MXhHaN-ZgR5FAA
## Лабораторная работа 3а.
### Вариант 4
Создали файл скрипт сразу с кодом

<img width="460" height="68" alt="image" src="https://github.com/user-attachments/assets/f6aebf15-d9cd-4e15-b877-97531ca42c51" />

Сделал файл исполняемым

<img width="331" height="15" alt="image" src="https://github.com/user-attachments/assets/4ef0f281-342a-4cde-8472-38bf32864b96" />

Тесты

<img width="422" height="68" alt="image" src="https://github.com/user-attachments/assets/bcf159db-25eb-497f-bf1d-c88a95755603" />

<img width="592" height="43" alt="image" src="https://github.com/user-attachments/assets/d4c36c49-3f6e-4301-8ab6-ad19f022874f" />


Тестируем

<img width="534" height="345" alt="image" src="https://github.com/user-attachments/assets/05af5459-48c6-4122-b528-6108dc232a76" />

## Лабораторная работа 3b
### Вариант 4.

<img width="339" height="49" alt="image" src="https://github.com/user-attachments/assets/b6f9f917-f6b5-4284-9e4c-3ce631def3f2" />

<img width="686" height="179" alt="image" src="https://github.com/user-attachments/assets/3263a34e-8525-4a9b-af61-5eaf540c5450" />

<img width="674" height="80" alt="image" src="https://github.com/user-attachments/assets/b383c58f-9f91-49e1-826c-fc63f83cb65e" />

<img width="480" height="83" alt="image" src="https://github.com/user-attachments/assets/2672ddfe-1fbf-443f-87a5-ac2da5af4658" />

