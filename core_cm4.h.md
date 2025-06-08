Макросы
```c
#define NVIC_SetPriorityGrouping __NVIC_SetPriorityGrouping
#define NVIC_GetPriorityGrouping __NVIC_GetPriorityGrouping
#define NVIC_EnableIRQ __NVIC_EnableIRQ
#define NVIC_GetEnableIRQ __NVIC_GetEnableIRQ
#define NVIC_DisableIRQ __NVIC_DisableIRQ
#define NVIC_GetPendingIRQ __NVIC_GetPendingIRQ
#define NVIC_SetPendingIRQ __NVIC_SetPendingIRQ
#define NVIC_ClearPendingIRQ __NVIC_ClearPendingIRQ
#define NVIC_GetActive __NVIC_GetActive
#define NVIC_SetPriority __NVIC_SetPriority
#define NVIC_GetPriority __NVIC_GetPriority
#define NVIC_SystemReset __NVIC_SystemReset
#define NVIC_SetVector __NVIC_SetVector
#define NVIC_GetVector __NVIC_GetVector
```

FPU function
```c
/**

\brief get FPU type
\details returns the FPU type
\returns
- \b 0: No FPU
- \b 1: Single precision FPU
- \b 2: Double + Single precision FPU
*/
__STATIC_INLINE uint32_t SCB_GetFPUType(void)
```

SysTick function
```c
/**
\brief System Tick Configuration
\details Initializes the System Timer and its interrupt, and starts the System Tick Timer.
Counter is in free running mode to generate periodic interrupts.
\param [in] ticks Number of ticks between two interrupts.
\return 0 Function succeeded.
\return 1 Function failed.
\note When the variable <b>__Vendor_SysTickConfig</b> is set to 1, then the
function <b>SysTick_Config</b> is not included. In this case, the file <b><i>device</i>.h</b>
must contain a vendor-specific implementation of this function.
*/
__STATIC_INLINE uint32_t SysTick_Config(uint32_t ticks)
```

Debug In/Output function
```c
/**
\brief ITM Send Character
\details Transmits a character via the ITM channel 0, and
\li Just returns when no debugger is connected that has booked the output.
\li Is blocking when a debugger is connected, but the previous character sent has not been transmitted.
\param [in] ch Character to transmit.
\returns Character to transmit.
*/
__STATIC_INLINE uint32_t ITM_SendChar (uint32_t ch)

/**
\brief ITM Receive Character
\details Inputs a character via the external variable \ref ITM_RxBuffer.
\return Received character.
\return -1 No character pending.
*/
__STATIC_INLINE int32_t ITM_ReceiveChar (void)

/**
\brief ITM Check Character
\details Checks whether a character is pending for reading in the variable \ref ITM_RxBuffer.
\return 0 No character available.
\return 1 Character available.
*/
__STATIC_INLINE int32_t ITM_CheckChar (void)
```
