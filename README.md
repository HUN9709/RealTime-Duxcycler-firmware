# MyPCR_Firmware

- v3.7 release note

> 본 Real-Time Duxcycler firmware는 biomedux의 DuxCycler_Firmware v3.5 를 기반으로 한다.
>(DuxCycler_Firmware v3.5 : https://github.com/biomedux/DuxCycler_Firmware/tree/2797656ed7f225fa034f60f68c1c02ce60e075c1)
> 
> Real-Time pcr 기능이 요구됨에 따라 진행중인 protocol을 멈추는 기능이 필요된다.
> 따라서 STATE_PAUSE 를 추가하지 않고, `./PCR/PCR_Task.c` 의 `Task_Control()` 함수에서 사용하는`IsTimeInfinite` 변수를 사용한다.
> `IsTimeInfinite = TRUE` 상태에서 protocol를 재개하는 Command.RESUME 을 추가하였다.

> 변동사항은 아래와 같다.
> 
> ```C
> // CMD_RESUME 추가
> // ./DEFINE/UserDef.h line 83
> typedef enum _COMMAND
>{
>   ...
>	CMD_RESUME,					// 7	Resume protocol // YSH 220711
>   ...
>} COMMAND;
>```
> ```C
> // CMD_RESUME 받을 시 처리
> // ./DEFINE/PCR_Task.c line 153
> switch(Cur_Command)
> {
> ...
> case CMD_RESUME:	// Resume protocol when IsTimeInfinite == True // YSH 220711
>   IsTimeInfinite = FALSE;
>   break;
> }
>```
