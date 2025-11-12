# Atmega328P 16MГц 5В.
USBASP на Arduino pro mini Atmega328P 16Mгц 

если необходимо работать с SREG:
надо добавить в файлы(main.c, icp.c, usbasp.h) новые режимы и функции
SREG (Status Register) содержит флаги состояния процессора:

    Bit 7: I - Global Interrupt Enable
    Bit 6: T - Bit Copy Storage
    Bit 5: H - Half Carry Flag
    Bit 4: S - Sign Bit
    Bit 3: V - Two's Complement Overflow Flag
    Bit 2: N - Negative Flag
    Bit 1: Z - Zero Flag
    Bit 0: C - Carry Flag

| Бит | Назначение | Значение                 |
| --- | ---------- | ------------------------ |
| 7   | SRP0       | 0 — нет защиты           |
| 6   | SRP1       | 0 — нет защиты           |
| 5   | BP4        | 0 — нет блокировки       |
| 4   | BP3        | 0 — нет блокировки       |
| 3   | BP2        | 0 — нет блокировки       |
| 2   | BP1        | 0 — нет блокировки       |
| 1   | BP0        | 0 — нет блокировки       |
| 0   | WEL        | 0 — Write Enable сброшен |

0x00 — разблокировать всё, 0x1C — блокировать всё.

--------------------------------------------------------------------------------------------------------------------------
/*
uint8_t avr_readSREG(void)
{
    /* 1. Отправить команду 0x05 (Read Status Register) */
    ispTransmit(0x05);   // команда Read Status Register

    /* 2. Прочитать 1 байт — это SREG */
    return ispTransmit(0x00);  // dummy-байт, возвращаем ответ
}

void avr_writeSREG(uint8_t value)
{
    /* 1. Write Enable (0x06) — обязательно перед записью */
    ispTransmit(0x06);

    /* 2. Команда Write Status Register (0x01) + данные */
    ispTransmit(0x01);
    ispTransmit(value);   // новое значение SREG
}

uint8_t avr_readSREG(void);
void avr_writeSREG(uint8_t value);

#define USBASP_FUNC_READ_SREG       	21
#define USBASP_FUNC_WRITE_SREG       	22


		// В usbFunctionSetup добавить обработку чтения SREG
	} else if (data[1] == USBASP_FUNC_READ_SREG) {
    		replyBuffer[0] = avr_readSREG();
    		len = 1;

	} else if (data[1] == USBASP_FUNC_WRITE_SREG) {
    		avr_writeSREG(data[2]);   // значение пришло от хоста
    		len = 0;                  // ничего не возвращаем


