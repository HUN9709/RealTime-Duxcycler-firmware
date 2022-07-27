# MyPCR_Firmware

- v3.7 release note (Added melting curve)

> 본 Real-Time Duxcycler firmware는 biomedux의 DuxCycler_Firmware v3.5 를 기반으로 한다.
>(DuxCycler_Firmware v3.5 : https://github.com/biomedux/DuxCycler_Firmware/tree/2797656ed7f225fa034f60f68c1c02ce60e075c1)
> 
>Melting curve를 위한 firmware 수정

> 변동사항은 아래와 같다.
> 
> ```C
> // MaxDuration 변수 Global type으로 추가
>// ./DEFINE/GlobalTypeVars.c line 47
>// YSH 220718
>int MaxDuration = 0;
>
>// ./DEFINE/GlobalTypeVars.h line 58
>// YSH 220718
>extern int MaxDuration;
>```
> ```C
> // RX, TX buffer 수정
> // ./DEFINE/UserDefs.h line 21
>typedef enum _RXINDEX 			//	Rx Buffer matching at index
>{								//  Used in PCR_Task.c
>...
>	RX_MAXDURATION = 0x08, // YSH 220720
>} RXINDEX;
>
>...
>
> // ./DEFINE/UserDefs.h line 34
>typedef enum _TXINDEX			//	Tx Buffer matching at index
>{
>...
>	TX_MAXDURATION = 45			// 45	// YSH 220718
>
>} TXINDEX;
>```
> ```C
> // HOST 에서 받은 MaxDuration 값 저장
> // ./PCR/PCR_Task.c line 125
>void Pre_Process(void)
>{
>...
>	// YSH 220718
>	if (Rx_Buffer[RX_MAXDURATION] != 0x00)
>	{
>		MaxDuration = Rx_Buffer[RX_MAXDURATION];
>	}
>...
>}
>```
> ```C
> // Tx buffer 보내기 전 현재 MaxDuration 값 setting
> // ./PCR/PCR_Task.c line 566
>void Post_Process(void)
>{
>...
>
>	// YSH 220718
>	ToSendDataBuffer[TX_MAXDURATION] = (unsigned char)MaxDuration;
>}
>```
> ```C
> // Tx buffer 보내기 전 현재 MaxDuration 값 setting
> // ./PCR/PCR_Task.c line 566
>void Post_Process(void)
>{
>...
>
>	// YSH 220718
>	ToSendDataBuffer[TX_MAXDURATION] = (unsigned char)MaxDuration;
>}
>```
> ```C
> // 설정된 MaxDuration 값으로 PWM_Duration 을 limit
> // ./PCR/Temp_Ctrl.c.c line 421
>void Get_Chamber_Duration(float temp)
>{
>...
>	// YSH 220718
>	if (out > MaxDuration) PWM_Duration = MaxDuration;
>	else if (out < -MaxDuration)  PWM_Duration = -MaxDuration;
>	else PWM_Duration = out;
>...
>}
>```