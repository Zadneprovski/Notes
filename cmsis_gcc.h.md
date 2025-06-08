

```c
/**
\brief Enable IRQ Interrupts
\details Enables IRQ interrupts by clearing the I-bit in the CPSR.
Can only be executed in Privileged modes.
*/
__STATIC_FORCEINLINE void __enable_irq(void)

/**
\brief Disable IRQ Interrupts
\details Disables IRQ interrupts by setting the I-bit in the CPSR.
Can only be executed in Privileged modes.
*/
__STATIC_FORCEINLINE void __disable_irq(void)
```

