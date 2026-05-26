# Linux_lab

1. Реализация функции на C++
<img width="297" height="174" alt="image" src="https://github.com/user-attachments/assets/177c2849-1e9b-4601-8613-a716f472dff5" />

Компиляция и запуск

<img width="409" height="69" alt="image" src="https://github.com/user-attachments/assets/7a6f67df-615f-4fdd-b6b6-4be75c49eff1" />

2. Компиляция в ассемблерский код с разными оптимизациями
3. 
<img width="473" height="148" alt="image" src="https://github.com/user-attachments/assets/fd01fa17-a2f7-4c0e-85cf-4a054034fa9d" />
<img width="215" height="181" alt="image" src="https://github.com/user-attachments/assets/fa18bd2f-8deb-4832-86a8-7516c68cb6b7" />

Без оптимизации (-O0)
```
	.file	"program.cpp"
	.text
	.section .rdata,"dr"
.LC0:
	.ascii "Sum = \0"                    # ФОРМАТНАЯ СТРОКА для вывода "Sum = "
	.text
	.globl	main
	.def	main;	.scl	2;	.type	32;	.endef
	.seh_proc	main
main:
.LFB2239:
	# === ПРОЛОГ ФУНКЦИИ (подготовка стека) ===
	pushq	%rbp                         # сохраняем старый %rbp
	.seh_pushreg	%rbp
	movq	%rsp, %rbp                   # устанавливаем новый кадр стека
	.seh_setframe	%rbp, 0
	subq	$48, %rsp                    # выделяем 48 байт на стеке под локальные переменные
	.seh_stackalloc	48
	.seh_endprologue
	call	__main                       # инициализация C++ (конструкторы глобальных объектов)
	
	# === 1. ПЕРЕМЕННЫЕ ===
	movl	$0, -4(%rbp)                 # sum = 0  (переменная sum, лежит на стеке по адресу rbp-4)
	movl	$1, -8(%rbp)                 # i = 1    (переменная i, лежит на стеке по адресу rbp-8)
	
	# === 2. ПЕРВЫЙ ПРЫЖОК НА ПРОВЕРКУ УСЛОВИЯ ===
	jmp	.L2                          # переходим к проверке условия (не заходя в тело цикла)
	
	# === 3. ТЕЛО ЦИКЛА (НАЧАЛО) ===
.L3:
	movl	-8(%rbp), %eax               # загружаем значение i в регистр eax
	addl	%eax, -4(%rbp)               # sum = sum + i (прибавляем eax к sum)
	addl	$1, -8(%rbp)                 # i++ (увеличиваем счётчик i на 1)
	# === ТЕЛО ЦИКЛА (КОНЕЦ) ===
	
	# === 4. ПРОВЕРКА УСЛОВИЯ ВЫХОДА ИЗ ЦИКЛА ===
.L2:
	cmpl	$5, -8(%rbp)                 # сравниваем i с 5 (вычитаем 5 из i, результат в невидимый регистр)
	jle	.L3                          # если i <= 5 (флаг меньше или равно), прыгаем в тело цикла .L3
	# если i > 5, то идём дальше (выход из цикла)
	
	# === 5. ВЫВОД СТРОКИ "Sum = " (operator<< для строки) ===
	leaq	.LC0(%rip), %rax             # загружаем адрес строки "Sum = " в rax
	movq	%rax, %rdx                   # кладём адрес во второй аргумент (rdx)
	movq	.refptr._ZSt4cout(%rip), %rax # загружаем адрес объекта cout
	movq	%rax, %rcx                   # кладём cout в первый аргумент (rcx)
	call	_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc  # вызов operator<< (вывод строки)
	
	# === 6. ВЫВОД ЧИСЛА sum (operator<< для int) ===
	movq	%rax, %rcx                   # результат предыдущего вызова (cout) - первый аргумент
	movl	-4(%rbp), %eax               # загружаем значение sum в eax
	movl	%eax, %edx                   # кладём sum во второй аргумент (edx)
	call	_ZNSolsEi                     # вызов operator<< (вывод числа)
	
	# === 7. ВЫВОД ПЕРЕВОДА СТРОКИ (endl) ===
	movq	%rax, %rcx                   # результат предыдущего вызова - первый аргумент
	movq	.refptr._ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_(%rip), %rax
	movq	%rax, %rdx                   # кладём адрес endl во второй аргумент
	call	_ZNSolsEPFRSoS_E              # вызов operator<< (вывод endl)
	
	# === 8. ЭПИЛОГ ФУНКЦИИ (возврат из main) ===
	movl	$0, %eax                     # return 0
	addq	$48, %rsp                    # очищаем стек (освобождаем 48 байт)
	popq	%rbp                         # восстанавливаем старый %rbp
	ret                                 # возвращаемся в вызывающую функцию
	.seh_endproc
	.section .rdata,"dr"
_ZNSt8__detail30__integer_to_chars_is_unsignedIjEE:
	.byte	1
_ZNSt8__detail30__integer_to_chars_is_unsignedImEE:
	.byte	1
_ZNSt8__detail30__integer_to_chars_is_unsignedIyEE:
	.byte	1
	.def	__main;	.scl	2;	.type	32;	.endef
	.ident	"GCC: (MinGW-W64 x86_64-ucrt-posix-seh, built by Brecht Sanders, r3) 14.2.0"
	.def	_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc;	.scl	2;	.type	32;	.endef
	.def	_ZNSolsEi;	.scl	2;	.type	32;	.endef
	.def	_ZNSolsEPFRSoS_E;	.scl	2;	.type	32;	.endef
	.section	.rdata$.refptr._ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_, "dr"
	.globl	.refptr._ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_
	.linkonce	discard
.refptr._ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_:
	.quad	_ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_
	.section	.rdata$.refptr._ZSt4cout, "dr"
	.globl	.refptr._ZSt4cout
	.linkonce	discard
.refptr._ZSt4cout:
	.quad	_ZSt4cout
```
3. Преобразование программы в модульную и разработка Makefile

<img width="235" height="127" alt="image" src="https://github.com/user-attachments/assets/e3f34a5b-494d-475e-99fb-de732eb629c4" />

main.cpp

<img width="344" height="183" alt="image" src="https://github.com/user-attachments/assets/e3155691-e863-4e19-9221-e864dff92f22" />

sum.cpp

<img width="293" height="193" alt="image" src="https://github.com/user-attachments/assets/eb4fc877-c8df-43fe-a6a5-48bf5a344e57" />

sum.h

<img width="217" height="109" alt="image" src="https://github.com/user-attachments/assets/cd37a9ed-7fbe-4adb-bc25-1fe5c41873dc" />

Создаем Makefile

<img width="433" height="338" alt="image" src="https://github.com/user-attachments/assets/8ae0d48b-eb80-40df-a1d1-2b8cc9aca4e5" />

Собираем

<img width="279" height="94" alt="image" src="https://github.com/user-attachments/assets/0afdb0b4-6485-414f-877e-a40c7a9291f2" />

Тестируем

<img width="244" height="49" alt="image" src="https://github.com/user-attachments/assets/69ab1404-2c4c-4549-8b38-4360faf897ad" />

4. Усовершенствование программы
Добавление параллельного потока и синхронизации

<img width="582" height="583" alt="image" src="https://github.com/user-attachments/assets/57250f89-9fa5-439a-b8c8-fb095c1d357e" />


Меняем Makefile с учетом изменений

<img width="441" height="330" alt="image" src="https://github.com/user-attachments/assets/ec5f0223-f9e8-41d5-b112-42503906b368" />

Тестируем(из-за неправильного вывода русского языка я заменил все на английский)

<img width="379" height="483" alt="image" src="https://github.com/user-attachments/assets/31e519d6-a04b-499f-a320-27470c051794" />



Файл result.txt используется как общий ресурс для обмена данными между параллельными потоками с синхронизацией через join().

<img width="231" height="218" alt="image" src="https://github.com/user-attachments/assets/d4b0454e-b148-421d-9d64-8f22b0b0a5e5" />


