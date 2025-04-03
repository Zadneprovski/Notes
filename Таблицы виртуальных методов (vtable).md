Это таблица поиска виртуальных функций, создаваемая компилятором для выполнения наиболее дочерних функций.

Пример:
```cpp
#include <iostream>

class Parent
{
    public:
    	virtual void func1(){std::cout<<"Parent";}
    	virtual void func2(){};
};

class Child1: public Parent
{
    public:
	   virtual void func1(){std::cout<<"Child1";}
};

class Child2: public Parent
{
	void func2(){}
};

int main()
{
	Child1 c1;
	Parent* cPtr = &c1;
	
	// Будет вызвана C1::function1()
	cPtr->func1(); // Child1
```

Assembler:
```asm
Parent::func2():

        push    {r7}
        sub     sp, sp, #12
        add     r7, sp, #0
        str     r0, [r7, #4]
        nop
        adds    r7, r7, #12
        mov     sp, r7
        ldr     r7, [sp], #4
        bx      lr

Child1::func1():

        push    {r7}
        sub     sp, sp, #12
        add     r7, sp, #0
        str     r0, [r7, #4]
        nop
        adds    r7, r7, #12
        mov     sp, r7
        ldr     r7, [sp], #4
        bx      lr
main:
        push    {r7, lr}
        sub     sp, sp, #8
        add     r7, sp, #0
        ldr     r3, .L5
        str     r3, [r7, #4]
        adds    r3, r7, #4
        mov     r0, r3
        bl      Child1::func1()
        movs    r3, #0
        mov     r0, r3
        adds    r7, r7, #8
        mov     sp, r7
        pop     {r7, pc}
.L5:
        .word   vtable for Child1+8

vtable for Child1:
        .word   0
        .word   typeinfo for Child1
        .word   Child1::func1()
        .word   Parent::func2()

typeinfo for Child1:
        .word   _ZTVN10__cxxabiv120__si_class_type_infoE+8
        .word   typeinfo name for Child1
        .word   typeinfo for Parent

typeinfo name for Child1:
        .ascii  "6Child1\000"

typeinfo for Parent:
        .word   _ZTVN10__cxxabiv117__class_type_infoE+8
        .word   typeinfo name for Parent
        
typeinfo name for Parent:
        .ascii  "6Parent\000"
```
